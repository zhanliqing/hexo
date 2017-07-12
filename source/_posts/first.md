---
title: first
date: 2016-12-02 16:22:54
tags: algorithm
categories: algorithm
---

堆排序
-----

```java
public class HeapSort {
    public static void main(String args[]) {
        int values[] = new int[10001];
        initValues(values);
        print(values);
        shuffer(values);
        print(values);
        makeMaxHeap(values);
        print(values);
        heapSort(values);
        print(values);
        System.out.println(isSort(values, true));
    }

    public static boolean isSort(int values[], boolean asc) {
        boolean sort = true;
        int start = 1;
        if (asc) {
            while (start < values.length - 2 && sort) {
                sort &= (values[start] < values[start + 1]);
                start++;
            }
        } else {
            while (start < values.length - 2 && sort) {
                sort &= (values[start] > values[start]);
                start++;
            }
        }

        return sort && start >= (values.length - 2);
    }

    public static void heapSort(int values[]) {
        makeMaxHeap(values);
        int end = values.length;

        while (end > 2) {
            swap(values, 1, end - 1);
            change(values, 1, end - 2);
            end--;
        }
    }

    public static void shuffer(int values[]) {
        Random r = new Random();
        for (int i = 1; i < values.length; i++) {
            int j = r.nextInt(values.length - i) + i;
            swap(values, i, j);
        }
    }

    public static void initValues(int values[]) {
        values[0] = 0;
        for (int i = 1; i < values.length; i++) {
            values[i] = i;
        }
    }

    public static void makeMaxHeap(int[] values) {
        int middle = (values.length - 1) / 2;
        while (middle > 0) {
            change(values, middle, values.length - 1);
            middle--;
        }
    }

    private static void print(int[] values) {
        StringBuilder builder = new StringBuilder("[");
        for (int i = 0; i < values.length; i++) {
            builder.append(values[i]).append(",");
        }
        builder.deleteCharAt(builder.length() - 1);
        builder.append("]");
        System.out.println(builder.toString());
    }

    public static void change(int[] values, int start, int end) {
        int i = start;
        while (left(i) <= end || right(i) <= end) {
            int idx = i;
            int max = values[idx];
            int now = idx;

            if (max < values[left(i)]) {
                max = values[left(i)];
                now = left(i);
            }

            if (right(i) <= end) {
                if (max < values[right(i)]) {
                    now = right(i);
                }
            }

            if (now != idx) {
                swap(values, idx, now);
                i = now;
            } else {
                break;
            }
        }
    }

    private static void swap(int[] values, int idx, int now) {
        int tmp = values[idx];
        values[idx] = values[now];
        values[now] = tmp;
    }

    public static int left(int n) {
        return 2 * n;
    }

    public static int right(int n) {
        return 2 * n + 1;
    }
}
```