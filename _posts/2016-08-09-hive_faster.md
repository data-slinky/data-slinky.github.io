---
layout: post
title: "Speed up your Hive queries"
date: 2016-08-09
description: Hive hacks.
tags: [big_data, Hive]
---

Part of project: [Slinky Projects](http://data-slinky.com/project/4_Slinky_projects/)

At work I had to do a simply inner join with two rather large Hive tables (~4.8m and 
~2.1b rows) which, even in the MapReduce paradigm, took forever since the computational 
complexity of Cartesian product is usually $$O(n^2)$$. I did a lot of research on ways to 
speed up Hive and here's a summary of what I found.

# 1. Enable Tez

[Apache Tez](https://tez.apache.org/) is essentially a generalization of the MapReduce paradigm. Among of the many things which makes Tez better than the usual MapReduce is that it avoids unnecessary writes to HDFS. You can enable Tez for Hive as follows:

```Hive
SET hive.execution.engine=tez;
```

# 2. Use ORCfile

ORCfile is a big data format for Hadoop and Hive. It takes less space and more efficiently than the old RCfile format in reading, processing and writing data. I haven't read too much on what the "O" (stands for optimized) means but I will take it because I do see speed improvements. You can convert your tables into ORC in Hive as follows:

```Hive
CREATE TABLE TABLEA_ORC (personID int, personName string, personAge int, Address string)
STORED AS ORC tblproperties (“orc.compress" = “SNAPPY”);
INSERT INTO TABLE TABLE_NAME_ORC SELECT * FROM A;
CREATE TABLE B_ORC (customerID int, role string, salary float, department string
) STORED AS ORC tblproperties (“orc.compress" = “SNAPPY”);
```

# 3. Use Tablesample to sample the data

It is always useful to test your queries on a small section of data before you run it over the entire data. There are two types of sampling: (1) buckets and (2) blocks. The former needs you to create a new table populated with bucketed data and then sample by selecting the buckets by

```Hive
TABLESAMPLE(BUCKET 2 OUT OF 25 ON user_id)
```

whereas the latter can be done directly

```Hive
SELECT *
FROM mytable TABLESAMPLE(0.1 PERCENT);
```

Block sampling can be done by percetange (shown above), by literal input size (`TABLESAMPLE(100M)`) or by rows (`TABLESAMPLE(10 ROWS)`). Note, this does not mean the query will return the exact number of samples (i.e., 10 rows Tablesample does not give you 10 rows) since we are sampling from the input split. Note, you should bucket if stream key and join keys are the same.

# 4. Partitioning

First you should save yourself the headache with the usual SQL partitioning which requires you to specify the range to be left or right by just doing so automatically with this statement:

```Hive
SET hive.exec.dynamic.partition = true;
SET hive.exec.dynamic.partition.mode = nonstrict;
```

You can then do the usual partitioning. Recall that partitioning is essentially the horizontal slicing of data.  Hive supports both managed and external tables .

# 5. Vectorization

Process 1,024 rows together instead of processing one row at a time.

```Hive
SET hive.vectorized.execution.enabled = true;
SET hive.vectorized.execution.reduce.enabled = true;
```

# 6. Join optimization with StreamTable and MapJoins

This will help take care of those stupid Java Heap-space exception when you have one giant table to join with a much smaller one.

```Hive
SELECT /*+ STREAMTABLE(bigTable) */ bigTable.user_id, otherTable.password from bigTable join otherTable on bigTable.user_id = otherTable.user_id;
```

If you have one small table in join, you can also use a MapJoin.

```Hive
SELECT //*+ MAPJOIN(otherTable) */ bigTable.user_id, otherTable.password from bigTable join otherTable on bigTable.user_id = otherTable.user_id;
```

Note, you can have Hive do the MapJoin automatically by simply turning it on:

```Hive
SET hive.auto.convert.join = true;
```

# 7. Write better queries

Most of the time you can probably rewrite your queries to be more efficient. Self-explanatory.