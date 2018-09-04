### MongoDB Performance Load Test Analysis:

### Objective : 
Monitor the behaviour while loading (Operation like CURD) with different scenario, existing load , Run Workload , Fail over, DB in cluster, Distributed load. 
1. Understand the  Industry Benchmark for MongoDB
2. Simulation of MongoDB environment in VM 
3. Identify the framework(YCSB) for loading the MongoDB with initial load of 6.5Lrecords
4. Load the Workload for different scenario / ops 
 4b. 50/50 read/ update

MongoDB    Version : 3.6     (No SQL DataBase)
YCSB       Version  : 0.1.2 (Framework / tool to test MongoDB by Yahoo)

Initial load = 6.5 Lakhs and 4 GB RAM. 
One Replica Collection (Primary & Secondary)  -- 1KB of data with 10 column
Work Load Pattern is 50/50 % mix of Reads / Writes.
Conducted the test for interval of threads / VMs (1,5,10,20,30,40,50 and 60)
Conducted the overnight test and 2 days test in the weekend.  
Recorded the throughput and latency.
Our bench mark limit of 500ms  B 

## Observations:
Latency were with 35 to 40ms for average, minimum, 95th Percentile,
                        250 to 300ms for 99th  Percentile for both read and update 
                        The Maximum Latency is more than our bench mark for all the tests. 
RAM is with in the limit 1.62 GB and CUP is 20% . (Both are well within the bench mark limit)
 
Pending Item to Test for conclusion are
1. Failover during the loading the data 
2. Proxy URL
3. Distributed Load Testing (Read and Write in different nodes) 
4. Capacity Planning  
5. Configuration of RAM for performance ( 4GB and 8 GB )

### Results for Incremental Operational load
![Result1](https://storage.googleapis.com/slt12/Result1.png)
![Capture1](https://storage.googleapis.com/slt12/Capture1.PNG)
![Result3](https://storage.googleapis.com/slt12/Result3.png)
![result4](https://storage.googleapis.com/slt12/result4.png)
![Result5](https://storage.googleapis.com/slt12/Result5.png)


