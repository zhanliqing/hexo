---
title: hdfs小文件合并存在的问题
date: 2017-04-24 22:58:28
tags: hadoop
categories: hadoop
---

在hdfs中，如果存在很多小文件，不但浪费存储空间，而且占用大量namenode的内存，一种可行的方案是把小文件合并为一个大文件，今天就说一下合并小文件经历的问题

如果在hdfs中有很多gzip压缩的小文件，合并可以通过一个mr程序来实现，把reduce的个数设置为一个远小于输入文件的个数，但可能会有一个比较奇怪的现象，就是合并后的文件大小跟合并前文件大小不一致，为什么呢，没研究过gzip算法，我猜测gzip压缩可能会联系上下文信息进行压缩，也就是如果相邻行相同的内容挨在一起，压缩比率会更大，做个小实验

```shell
$  test cat g_log.sh
line=`seq 1 1000000`

chars=("aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa" \
       "bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb" \
       "ccccccccccccccccccccccccccccccccccccccc" \
       "ddddddddddddddddddddddddddddddddddddddd" \
       "eeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee" \
       "fffffffffffffffffffffffffffffffffffffff" \
       "ggggggggggggggggggggggggggggggggggggggg")

echo ${#chars[@]}

for i in $line ;do
    id=$(($RANDOM%7))
    echo ${chars[$id]},$i,$id >>tmp
done
$  test ll tmp
-rw-rw-r--. 1 zlq zlq 47M Apr 24 23:27 tmp	

$  test cat tmp|gzip >tmp.gz
$  test cat tmp|sort|gzip >tmp1.gz
$  test ll
-rw-rw-r--. 1 zlq zlq  480 Apr 24 23:26 g_log.sh
-rw-rw-r--. 1 zlq zlq  47M Apr 24 23:27 tmp
-rw-rw-r--. 1 zlq zlq 2.9M Apr 24 23:28 tmp1.gz
-rw-rw-r--. 1 zlq zlq 3.5M Apr 24 23:28 tmp.gz

```
可以看到排序后的压缩更高，因此多个小文件合并，如果直接进行repartition，则压缩前后大小不一致，如果源文件是相同key的在一起，则重排后可能文件大小可能会变大，一种办法是在map中分key写，但这样程序不通用，在spark中有一个变化叫coalesce，他不会引起数据的repartition，因此可以用他来实现文件合并，事例代码如下

```scala
import java.text.SimpleDateFormat
import java.util.Calendar

import org.apache.hadoop.io.compress.GzipCodec
import org.apache.spark.{SparkConf, SparkContext}

/**
  * Created by liqing.zhan on 2017/4/24.
  */
object SmallFileMerge {
    def main(args: Array[String]) {
        val conf: SparkConf = new SparkConf().setAppName("merge_small_file")
        val context: SparkContext = new SparkContext(conf)
        var start = args(0).toInt
        val end = args(1).toInt

        val path = args(2)

        val partitions = args(3).toInt

        if (end < start) return

        do {
            val sc = context.textFile(path + "/" + start)
            sc.coalesce(partitions).saveAsTextFile(path + "/" + start + "_bak", classOf[GzipCodec])
            println("process %s".format(start))
            start = addDate(start, 1)
        } while (start <= end)


    }

    def addDate(day: Int, c: Int): Int = {
        val dateFormat: SimpleDateFormat = new SimpleDateFormat("yyyyMMdd")
        val day1 = dateFormat.parse(day + "")
        val cal: Calendar = Calendar.getInstance
        cal.setTime(day1)
        cal.add(Calendar.DAY_OF_MONTH, c)
        dateFormat.format(cal.getTime).toInt
    }
}

```


借助CombineFileInputFormat
--------------------------

```java

/**
 * 合并小文件工具，依赖CombineFileInputFormat类
 * 传入三个参数，
 * args1:合并文件的最大值，如果用gzip压缩，设置大点，1000M左右
 * args2:input path
 * args3:output path
 *
 * <p>
 * <p>
 * Created by liqing.zhan on 2017/5/15.
 */
public class MergeSmallFile extends Configured implements Tool {
    private static Logger log = Logger.getLogger(MergeSmallFile.class);

    public static final long MB = 1024 * 1024L;

    public static void main(String[] args) {
        int exitCode = 0;
        try {
            exitCode = ToolRunner.run(new MergeSmallFile(), args);
        } catch (Exception e) {
            log.error("merge small file exception ", e);
        }
        System.exit(exitCode);
    }

    @Override
    public int run(String[] args) throws Exception {
        Configuration conf = new Configuration(getConf());
        int maxSize = Integer.parseInt(args[0]);
        conf.setLong("mapreduce.input.fileinputformat.split.maxsize", MB * maxSize);
        conf.setBoolean("mapred.output.compress", true);
        conf.setClass("mapred.output.compression.codec", GzipCodec.class, CompressionCodec.class);
        conf.set("mapred.job.queue.name", "yourqueue");
        conf.set("mapred.job.priority", JobPriority.VERY_HIGH.name());
        conf.set("mapreduce.task.timeout", "6000000");


        Job job = Job.getInstance(conf);
        job.setJobName("merge_smallfile.liqing.zhan");
        FileInputFormat.setInputPaths(job, new Path(args[1]));
        FileOutputFormat.setOutputPath(job, new Path(args[2]));
        job.setJarByClass(MergeSmallFile.class);
        job.setInputFormatClass(CombineTextInputFormat.class);
        job.setMapOutputKeyClass(NullWritable.class);
        job.setOutputValueClass(Text.class);
        job.setOutputFormatClass(TextOutputFormat.class);
        job.setOutputKeyClass(NullWritable.class);
        job.setOutputValueClass(Text.class);
        job.setMapperClass(PureMapper.class);
        job.setNumReduceTasks(0);

        return job.waitForCompletion(true) ? 0 : 1;
    }

    public static class PureMapper extends Mapper<LongWritable, Text, NullWritable, Text> {
        @Override
        protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
            context.write(NullWritable.get(), value);
        }
    }
}

```

还有一个问题，如果小文件是hive的数据源，并且hive的表结构中途增加过字段，则用add partition增加分区后，查询输出的内容可能不一致。

例如假设有如下建表语句
```sql
CREATE EXTERNAL TABLE `test_zz`(
  `a1` string, 
  `a2` string, 
  `a3` string)
PARTITIONED BY ( 
  `day` int)
ROW FORMAT DELIMITED 
  FIELDS TERMINATED BY '\t' 
  LINES TERMINATED BY '\n' 
STORED AS INPUTFORMAT 
  'org.apache.hadoop.mapred.TextInputFormat' 
OUTPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
LOCATION
  'hdfs://abc/liqing.zhan/hive'
```




