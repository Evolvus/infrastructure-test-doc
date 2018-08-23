# MongoDB Performance Load Test Setup Using YCSB Tool

Scope of this document will list below Activities
* Insight to YCSB suite.
* Setup YCSB.
* How to perform test using YCSB.

## YCSB(Yahoo Cloud serving BenchMark)

 An open source framework for evaluating and comparing the performance of NOSQL database such as MongoDB,Apache HBase,Apache Cassandra,Redis etc..It was developed at Yahoo!.It has two parts:
1.The YCSB Client,workload Generator.
2.The core Workload,set of scenarios to be executed.

### Set up
Below are the steps to download YCSB.
1.Navigate to path were we need to download the YCSB from console and Execute the below command.
a. git clone git://github.com/brianfrankcooper/YCSB.git
b. cd YCSB
c. mvn clean package

### Running workload
There are 6 steps to running a workload.
1.Set up the database system to test
2.Choose the appropriate DB interface layer
3.Choose the appropriate workload
4.Choose the appropriate runtime parameters (number of client threads, target throughput, etc.)
5.Load the data
6.Execute the workload

### Choose Appropriate Workload
The workload defines the data that will be loaded into the database during the loading phase and the operations that will be executed against the load is the transaction phase.
YCSB includes a set of core workloads that defines the basic benchmark.

The core workloads consists of six different workloads:
1. Workload A:Update heavy workload mix of 50%reads and 50% writes.
2. Workload B:Read mostly workload mix of 95% reads and 5% writes.
3. Workload C:Read only :100% reads.
4. Workload D:Read latest workload.new records are inserted, and the most recently inserted records are the most popular
5. Workload E:Short ranges.
6. Workload F:Read-modify-write:will read a record, modify it, and write back the changes.

### Choose Appropriate Runtime parameters
Parameter files will define a specific workload,these are additional settings that may specify for a particular run of a benchmark.These settings are:
a) -threads:the number of client threads on load to database.
b) -s :status status will be shared for every seconds based on the value specified.

### Load the data
Workloads have two executable phase
a.Loading phase: load parameter in the command tells to execute the loading phase
b.Transaction phase:run command in the command line tells to execute the transaction phase.

### Execute the workload
Once the data is loaded ,we can execute the workload.This tells the client to execute the transaction phase of the workload.

### Execute phase
We have to first load the data into the database by executing the below command.
```console
./bin/ycsb load mongodb -P workloads/workloada -p recordcount=10000000 -threads 16 -p mongodb.url=mongodb://tagqa:evolvus*123@13.127.227.147:27017/mongotag
```
1.Recordcount is the number of load on the database.
2.Threads are the number of client on database.
3.Workloada is the different scenarios based on the Appropriate load .

Once the data has been loaded into the database we have to perform transaction phase for different scenarios.Below is the command to execute the transaction phase based on the scenarios.
```console
./bin/ycsb run mongodb -s -P workloads/workloada -p mongodb.url=mongodb://tagqa:evolvus*123@13.127.227.147:27017/mongotag -p mongodb.auth="true" -p operationcount=2500000 -threads 2
```
OperationCount is the number of operations we have to perform on each thread.