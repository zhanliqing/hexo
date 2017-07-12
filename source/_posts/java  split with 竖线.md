---
title: Java中用竖线|对字符串split
date: 2017-02-08 14:14:40
tags: java 
categories: java 
---
在Java中，对字符串进行spilt要特别注意，split里边的参数是一个正则表达式，如果参数是|，因为|在正则表达式里边是一个特殊字符，所以需要进行转义，否则结果跟预期的不符，例如

```java
	public class Split {
	    public static void main(String[] args) {
	        String phone = "123456|789";
	        List<String> phones = Arrays.asList(phone.split("|"));
	        System.out.println(phones);
	    }
	}
```

输出结果是：
>  [1, 2, 3, 4, 5, 6, |, 7, 8, 9]    
而不是我们期望的[123456, 789]

原因
---
因为|在正则表达式中表示或者的意思，上边代码的意思是用空字符串或者空字符串进行分割，即同phone.split("")一样    

解决
----

只需对|进行转义即可，因为\在Java中也是一个特殊字符，需要用\\\\|对|进行转义    
或者用Pattern.quote("|")来给加上转义字符

```java
	public class Split {
	    public static void main(String[] args) {
	        String phone = "123456|789";
	        //List<String> phones = Arrays.asList(phone.split"\\|");
	        List<String> phones = Arrays.asList(phone.split(Pattern.quote("|")));
	        System.out.println(phones);
	    }
	}
```
