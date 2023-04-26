

## Tips for Debugging Code Errors
Typos are probably the simplest errors to identify
* A <ins>typo</ins> is a method name will generate a <ins>short attribute error</ins>
* An <ins>incorrect column name</ins> will result in a <ins>long analysis exception error</ins>
* <ins>Typos in variable</ins> can result in <ins>lengthy errors</ins>
* While Spark supports the Python API, its native language is Scala. That's why some of the error messages are referring to Scala, Java, or JVM issues even when we are running Python code.
* Whenever you use `collect`,be careful how much data are you collecting
* <ins>Mismatched parantheses</ins> can cause end-of-file (EOF) errors that may be misleading

>You are running Spark code, and you encounter an error. What are some things you should look for in your code to resolve it?
> - [x] Typos in your method names
> - [ ] How long your Spark program is taking to run
> - [x] Mismatched parantheses
> - [x] Use of collect() in your code

## Data Errors
When you work with big data, some of the records might have missing fields or have data that's malformed or incorrect in some other usnexpected way.
* If data is malformed, Spark populates these fields with nulls
* If you try to do someting with a missing field, <ins>nulls remain nulls</ins>

>You are working with some data with Spark, and you are concerned about malformed data in your dataset. How would you go abour locating it?
> - [ ] Look through the data to see if you can spot any malformed data
> - [x] filter a data frame for `_corrupt_record` results
> - [ ] Compare the size of your data before, and after you Spark code runs
> - [ ] Filter a data frame for null values

## Debugging Code
If you were writing a traditional Python script, you might use print statement to output the values held by variables. These print statements can be helpful when debugging your code, but this won't work on Spark. Think back to how Spark runs your code.
* You have a **driver node** coordinating the tasks of various **worker nodes**.
* Code is running on those worker nodes an not the driver, so print statements will only run on those workder nodes.
* You cannot directly see the output from them because you're not connected directly to them.
![image](https://user-images.githubusercontent.com/58150666/234269701-ff645a30-862b-4d72-8c35-46185083c5fd.png)
* Spark makes a copy of the input data every time you call a function. So, the origional debugging variables that you created won't actually get loaded into the worker nodes. Instead, each workder has their own copy of these variables, and only these copies get modified.
![image](https://user-images.githubusercontent.com/58150666/234270058-d03d121a-69e6-499a-b2d4-00fbc2112f31.png)

To get around these limitations, Spark gives you special variables known as **accumulators**. Accumulators are like global variables for your entire cluster.


## What are Accumulators?
As the name hints, accumulators are variables that accumulate. Because Spark runs in distributed mode, the workers are running in parallel, but asynchronously. For example, **worker 1** will not be able to know how far **worker 2** and **worker 3** are done with their tasks. With the same anology, the variables that are local to workers are not going to be shared to another worker unless you accumulate them. Accumulators are used for mostly `sum` operators, like in Hadoop MapReduce, but you can implement it to do otherwise.
For additional deep-dive, here is the [Spark documentation on accumulators](https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#accumulators) if you want to learn more about these.

> What would be the best scenario for using Spark Accumlatiors? 
>- [ ] When you're using transformation functions accross your code
>- [x] When your know you will have different values across your executors :tada:

### What is Spark Broadcast? :shipit:
Spark Broadcast variable are `secured`, `read-only` variables that get distriburted and cached to worker nodes. This is helpfu to Sparl because when the driver sends the data and tasks attached together which could be a little heavier on the network side.
Broadcast variable seek to reduce network overhead and to reduce communications. Spark Broadcast variables are used only with Spark Context.

> When is broadcast usually used in Spark?
> - [x] Broadcast join is a way of joining a large table and small table in Spark.
> - [ ] Broadcast variable is cached variable in the driver.
> - [ ] Broadcast variable is shipped to each machine with tasks
> - [x] Broadcast join is like map-side join in MapReduce 

## What are Accumulators?
As the name hints, accumulators are variables that accumulate. Because Spark runs in distributed mode, the workers are running in parallel, but asynchronously. For example, **worker 1** will not be able to know how far **worker 2** and **worker 3** are done with their tasks. With the same anology, the variables that are local to workers are not going to be shared to another worker unless you accumulate them. Accumulators are used for mostly `sum` operators, like in Hadoop MapReduce, but you can implement it to do otherwise.
For additional deep-dive, here is the [Spark documentation on accumulators](https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#accumulators) if you want to learn more about these.

> What would be the best scenario for using Spark Accumlatiors? 
>- [ ] When you're using transformation functions accross your code
>- [x] When your know you will have different values across your executors :tada:

## What is Spark Broadcast? :shipit:
Spark Broadcast variable are `secured`, `read-only` variables that get distriburted and cached to worker nodes. This is helpfu to Sparl because when the driver sends the data and tasks attached together which could be a little heavier on the network side.
Broadcast variable seek to reduce network overhead and to reduce communications. Spark Broadcast variables are used only with Spark Context.

> When is broadcast usually used in Spark?
> - [x] Broadcast join is a way of joining a large table and small table in Spark.
> - [ ] Broadcast variable is cached variable in the driver.
> - [ ] Broadcast variable is shipped to each machine with tasks
> - [x] Broadcast join is like map-side join in MapReduce 

## Different types of Spark Functions

### Transformation and Actions
There are two types of functions in Spark:
1. **Transformation**
2. **Actions**

Spark uses <ins>lazy evaluation</ins> to evaluate RDD and dataframe. Lazy evaluation means the code is not executed until it is needed. The __action__ functions trigger the lazily evaluated functions.
For example,
```python
df = spark.read.load("some csv file")
df1 = df.select("some column").filter("some condition")
df1.write("to path")
```

* In this code, `select` and `filter` are **transformation functions**, and `write` is an **action function**.
* If you execute this code line by line, the second line will be loaded, but you **will not see the function being executed in your Spark UI**.
* When you actually **execute using action `write`, then you will see your Spark program being executed:
    * `select` --> `filter` --> `write` chained in Spark UI
        * but you will see `Wrtie` show up under your tasks.

This is significant because you can chain you **RDD** or dataframe as much as you want, but it might not do anything until you actually **trigger** with some **action words**. And if you have lengthy **transformations**, then it might take your executors quite some time to complete all the tasks.


## Understanding Data Skewness
![image](https://user-images.githubusercontent.com/58150666/234598610-b11dbbcc-d174-4971-9fa5-a468cb8cdd59.png)

### Introduction to Data Skew
**Skewed data** means due to non-optimal partitioning, the data is heavy on few partitions. This could be problematic.

Imagine you're processing a dataset, and the data is distributed through your cluster by partition.

* In this case, only a few partitions will continue to work, while the rest or the partitions do not work.
* If you were to run your cluster like this, you will get billed by the time of the data processing, which means you will get billed for the duration of the longest partitions working.
* We would like to re-distribute the data in a way to that all the partitions are working.

### **Figure A.** Time to process with non-optimal partitioning with skewed data
![image](https://user-images.githubusercontent.com/58150666/234599974-a4b59d02-31db-4254-8f18-3c9c48efabbc.png)

### **Figure B.** Time to process with optimal partitioning with skewed data
![image](https://user-images.githubusercontent.com/58150666/234600476-41748171-35ab-441d-a0f0-4fbf04e52edc.png)

| In order to loot at the skewness of the data: |
|-----------------------------------------------|
| :bar_chart: Check for `min`, `max` and data RANGES.       | 
| :briefcase: Examine how the workers are working.           |
| :triangular_flag_on_post: Identify workers that are running longer and aim to optimize it. |


## Optimizing for Data Skewness

### So how do we solve skewed data problems?
The Goal is to change the partitioning columns to take out the data skewness(e.g., the `year` column is skewed).
1. **Use Alternate Columns that are more normally distributed:**
E.g., Instead of the `year` column, we can use `Issue_Date` column that isn't skewed.
2. **Make Composite Keys:**
For e.g., you can make composite keys by combining two columns so that the new column can be used as a composite key titled `Issue_Date + State`. The **new** column will now include data from 2 columns, e.g., `2017-04-15-NY`. This clumn can be used to partiion the data, create more normally distributed datasets(e.g., distribution of parking violations on 2017-04-15 would now be more spread out across states, and this can now help addresss skewness in the data.

3. **Partition by number of Spark workers:**
Another easy way is using the Spark workers. If your know the number of your workers for Spark, then you can easily partition the data by the number of workers `df.repartition(number_of_workers)` to repartition your data evenly across your workers. For Example, if you have 8 workers, then you should do `df.repartition(8)` before doing any operations.

| **Two different ways to solve skewed data problem**|
|----------------------------------------------------|
| :heavy_check_mark: **Assign a new, temporary partition key** before processing any huge shuffles.|
| :scissors: The second method is using **repartition**|

