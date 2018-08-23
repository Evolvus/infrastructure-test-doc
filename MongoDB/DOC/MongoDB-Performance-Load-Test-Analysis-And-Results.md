### MongoDB Performance Load Test Analysis:
We are clear on performing the performance testing on the application build on Mongo DB

### Objective : 
Monitor the behaviour while loading (Operation like CURD) with different scenario, existing load , Run Workload , Fail over, DB in cluster, Distributed load. 
1. Understand the  Industry Benchmark for MongoDB
2. Simulation of MongoDB environment in VM 
3. Identify the framework(YCSB) for loading the MongoDB with initial load of 6.5Lrecords
4. Load the Workload for different scenario / ops 
 4b. 50/50 read/ update

### Analysis of results: 
Observe the  Throughput (ops/sec) , latency( @99% in ms)  for single thread, multiple thread, etc.. 

## MongoDB results Analyis  

Using the MongoDB (version3.6) along with YCSB (version 0.1.2) is a popular Java open-source specification and program suite developed at Yahoo! to compare the relative performance of various NoSQL databases. 

*  Initial load = 6.5 Lakhs and 4 GB RAM. 
*  Work Load Pattern is 50/50 % mix of Reads / Writes
*  Test should be conducted more than 30 mints to achieve the good results 
*  Conducted the test for interval of threads (1,5,10,20,30,40,50 and 60) and recorded the throughput and latency . 
*  Repeated the test for couple of threads to observe the consistency.(10.30,40 threads).  
*  Also conducted the overnight test and 2 days test in the weekend. 
### Results for Incremental Operational load
![Result1](https://storage.googleapis.com/slt12/Result1.png)
![Result3](https://storage.googleapis.com/slt12/Result3.png)
![Result4](https://storage.googleapis.com/slt12/Result4.png)
![Result5](https://storage.googleapis.com/slt12/Result5.png)
## Conclusion 
   As per the test data the latency were with 35 to 40ms for average, minimum, 95th Percentile, 250 to 300ms for 99th  Percentile for both read and update which is within our bench mark limit of 500ms and there is no drastic difference across the tests . The Maximum Latency is more than our bench mark for all the tests. 

RAM is with in the limit 1.62 GB and CUP is 20% . (both are well within the bench mark limit