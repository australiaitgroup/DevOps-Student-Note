# Class-15-Monitoring2

# Monitoring Basics

Often, in SRE interviews, the interviewer may ask you about how would you monitor the following system 
and trouble shoot a latency issue?

![Alt text](../images/voting_system_example.png?raw=true)

If we break this question down, there are literally two questions:
* 1. how do you design the monitoring systems? What metrics are you going to look at?
   
* 2. are you able to troubleshooting the latency problem of this system?

Okay, before we answer the first question let us look at the monitoring from a top down approach

# What is monitoring?

Collecting, processing, aggregating, and displaying real-time quantitative data about a system, such as query counts 
and types, error counts and types, processing times, and server lifetimes.

# Monitoring Types

```
whitebox
blackbox
```

## White-box monitoring
Monitoring based on metrics exposed by the internals of the system, including logs, interfaces like the Java Virtual Machine Profiling Interface, or an HTTP handler that emits internal statistics.

Metrics: 
* Prometheus + Grafana
* Datadog (Commercial)
* SignalFx (Commercial)

Log Monitoring:
* Kibana  (Open Source/Commercial)
* Splunk  (Commercial)

## Black-box monitoring
Testing externally visible behavior as a user would see it.

Synthetic monitoring
* Selenium Webdriver
* Cypress
* New Relic (Commercial)

Load-testing
* Gatling
* Locust 

# Why monitor?
* Analyzing long-term trends
   * How big is my database and how fast is it growing? 
      * How quickly is my daily-active user count growing?
      * Are they caused by abusive, bug in the code or a real growth?
      
* Comparing over time or experiment groups
   * Are queries faster with Acme Bucket of Bytes 2.72 versus Ajax DB 3.14? 
   * How much better is my memcache hit rate with an extra node? 
   * Is my site slower than it was last week?
   
* Alerting
   * Something is broken, and somebody needs to fix it right now!
   * Or, something might break soon, so somebody should look soon.
   
* Building dashboards
   * Dashboards should answer basic questions about your service, 
     and normally include some form of the four golden signals
     
* Debugging
   * Our latency just shot up; what else happened around the same time?

## SRE - 4 Golden Signals

### 1. Latency

The time it takes to serve a request.

#### Visbility
Backend Latency: 
* The latency to serve an API (access log)
   - by web nodes
   - by log level
   - by method
   - by user agent
   - by environment (dev, stg, prod)
   - by region (prod-east, prod-west)
   - by customer* (be careful with cardinality)
   - by microservices (if there is any)

* DB time (application logging)
   - by logger
   - by log level

Frontend Latency:
* Page/Component loading time 
* TTI (Time to interaction)


#### Should we look at the max or the average?
For latency, we shall look at it statistically with percentile e.g. p50 (median), p75 (~average), p90 (the majority)

Sometimes, we need to now about 99 percentile to make sure we are not missing some outliers

For example, the 50th percentile is the value below which 50% of the observations may be found.
https://en.wikipedia.org/wiki/Percentile


#### Error latency is also important

It’s important to distinguish between the latency of successful requests and 
the latency of failed requests. For example, an HTTP 500 error triggered due to loss of connection to a database or 
other critical backend might be served very quickly; however, as an HTTP 500 error indicates a failed request, 
factoring 500s into your overall latency might result in misleading calculations. On the other hand, a slow error is 
even worse than a fast error! Therefore, it’s important to track error latency, as opposed to just filtering out errors.

#### Trouble shooting
If it takes too long: 
* Do we have network issue/connectivity issue? 
* Was this reported by a single customer?
    * cross-region issue
    * other connectivity issue (if our application does not show a high latency)
* Is our DNS or CDN having issue?
* Is there any function of our application not efficient?
* Is our DB under pressure?



### 2. Traffic

A measure of how much demand is being placed on your system.

* Network In (Load Balancer, Application, Microservices, Cache and DB)
* Network Out (Load Balancer, Application, Microservices, Cache and DB)
* HTTP Request Rate (by region, by status_code, etc...) 
* Transactions per second (Cache, Queue and DB)

For a web service, this measurement is usually HTTP requests per second, 
perhaps broken out by the nature of the requests (e.g. static versus dynamic content). 

For a key-value storage system, this measurement might be transactions and retrievals per second.

### 3. Errors

The rate of requests that fail, either explicitly (e.g., HTTP 500s), 
implicitly (for example, an HTTP 200 success response, but coupled with the wrong content), 
or by policy (for example, "If you committed to one-second response times, any request over one second is an error"). 

* Error rate = Error/(Error+Success) (Sidecar, Microservice, API, DB etc...)
   * 5xx Error Rate (Application Error)
   * 4xx Error Rate (User generated Error)

* The size of Dead Letter Queue (Failed messages that are dumped into message queue) 
   
#### Tips
Where protocol response codes are insufficient to express all failure conditions, secondary (internal) protocols may
be necessary to track partial failure modes. Monitoring these cases can be drastically different: catching HTTP 500s
at your load balancer can do a decent job of catching all completely failed requests, while only end-to-end system
tests can detect that you’re serving the wrong content.


### 4. Saturation

How "full" your service is.
 
A measure of your system fraction, emphasizing the resources that are most constrained (e.g., in a memory-constrained 
system, show memory; in an I/O-constrained system, show I/O). Note that many systems degrade in performance before they
achieve 100% utilization (typically 60%-80%), so having a utilization target is essential.

In complex systems, saturation can be supplemented with higher-level load measurement: 
can your service properly handle double the traffic, handle only 10% more traffic, 
or handle even less traffic than it currently receives? For very simple services that have no parameters that alter 
the complexity of the request (e.g., "Give me a nonce" or "I need a globally unique monotonic integer") that rarely
change configuration, a static value from a load test might be adequate. As discussed in the previous paragraph, 
however, most services need to use indirect signals like CPU utilization or network bandwidth that have a known 
upper bound. Latency increases are often a leading indicator of saturation. Measuring your 99th percentile response
time over some small window (e.g., one minute) can give a very early signal of saturation.

Finally, saturation is also concerned with predictions of impending saturation, such as "It looks like your database
will fill its hard drive in 4 hours."

System Level
* CPU Utilisation
* Memory Utilisation
* Disk Utilisation
* I/O Utilisation

Application Level
* Thread Pool Saturation
* Message Queue Saturation

The key here: try to understand the system constrain first by running some load testing or fundamental analysis.

# Symptoms Versus Causes

#### Your monitoring system should address two questions: what’s broken, and why?

The "what’s broken" indicates the symptom; the "why" indicates a (possibly intermediate) cause.
```
Symptom	                               |    Cause                         
I’m serving HTTP 500s or 404s          |    Database servers are refusing connections
                                       |
My responses are slow                  |    CPUs are overloaded by a bogosort, or an Ethernet cable is crimped under a
                                       |    rack, visible as partial packet loss

Users in Antarctica aren’t 
receiving animated cat GIFs            |    Your Content Distribution Network hates scientists and felines, 
                                       |    and thus blacklisted some client IPs

Private content is world-readable      |    A new software push caused ACLs to be forgotten and allowed all requests
```

However, In read world, it can be more complicated than the above example.
e.g. 
* multiple metrics could be just the symptoms
   *  e.g. tomcat thread saturated, multiple nodes become unhealthy, but the main cause can happens at the garbage 
      collection.
      
* entangled dependencies
   *  e.g. when the alert of my application gets triggered, it could be a micro-service's problem or AWS may have
    an ongoing problem.      

Recap:
* Black-box monitoring is symptom-oriented and represents active—not predicted—problems: "The system isn’t working 
correctly, right now." 

* White-box monitoring depends on the ability to inspect the innards of the system, such as logs or HTTP endpoints, 
with instrumentation. White-box monitoring therefore allows detection of imminent problems, failures masked by retries,
and so forth.

There are three approaches that you could use:

### Problem Statement Approach
1. What makes you think there is a performance problem?
2. Has this system ever performed well?
3. What has changed recently? (Software? Hardware?
Load?)
4. Can the performance degradation be expressed in
terms of latency or run time?
5. Does the problem affect other people or applications (or
is it just you)?
6. What is the environment? Software, hardware,
instance types? Versions? Configuration?

### Workload Characterization Approach	
1. Who is causing the load? PID, UID, IP addr, ...
2. Why is the load called? code path, stack trace
3. What is the load? IOPS, tput, type, r/w
4. How is the load changing over time? 

### The USE Approach
1. Utilization: busy time
2. Saturation: queue length or queued time 
3. Error: error logs


## Other Tips

* Choosing an Appropriate Resolution for Measurements

    Different aspects of a system should be measured with different levels of granularity. For example:

    * Observing CPU load over the time span of a minute won’t reveal even quite long-lived spikes that drive high tail latencies.
    * On the other hand, for a web service targeting no more than 9 hours aggregate downtime per year (99.9% annual uptime), probing for a 200 (success) status more than once or twice a minute is probably unnecessarily frequent.
    * Similarly, checking hard drive fullness for a service targeting 99.9% availability more than once every 1–2 minutes is probably unnecessary.

* Most of the alerts are caused by changes in the system; double check them.
  * Code release
  * Database upgrade
  * Configuration changes
  * Performance tuning
  * Hardware (AWS) maintenance
  * Traffic increase

* Logs are your best friend, but logs may not always help.
  * Use metrics as the indicator and use logs to locate the true problem.
  * Sometimes you may need to use performance profiler such as CodeGuru to get the heapdump, threaddump or flamechart
  
* Monitoring for the Long Term
  * (Homework) try to understand what are SLI, SLO and SLAs? 
  * What are the trends/relations between e.g. CPU increase and MAU increase?   
  
Now, would you be able to answer the first question?



## Linux system stats breakdown
### 1. vmstat
vmstat reports describe the current state of a Linux system. Information regarding the running state of a system is useful when diagnosing performance related issues.
```
vmstat [interval] [count]
```

```
vagrant@linux:/home/vagrant$ vmstat 1 20
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0   1804 296112 132244 1207392    0    0    21   119   53  130  0  0 99  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0   98  217  0  0 100  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0  106  243  0  1 99  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0   70  220  0  0 100  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0   66  215  0  0 100  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0   66  221  0  1 100  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0   62  230  0  0 100  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0   73  235  0  0 100  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0   65  244  0  0 100  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0   73  241  0  0 100  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0   80  243  0  0 100  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0  109  231  0  0 99  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0   84  225  0  0 100  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0   94  285  1  0 99  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0   66  234  0  0 100  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0   76  257  0  0 100  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0   65  237  0  0 100  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0   65  247  0  0 100  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0   75  250  0  0 100  0  0
 0  0   1804 296104 132244 1207392    0    0     0     0  114  327  0  0 100  0  0
```
more readable: convert the number to Megabytes e.g. 1M=1000KB=1000000B
```
vagrant@linux:/home/vagrant$ vmstat -S m 1 10
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      1    303    135   1236    0    0    21   118   53  130  0  0 99  0  0
 0  0      1    303    135   1236    0    0     0     0  103  248  0  1 100  0  0
 0  0      1    303    135   1236    0    0     0     0   97  239  0  0 100  0  0
 0  0      1    303    135   1236    0    0     0     0   91  249  0  0 100  0  0
 0  0      1    303    135   1236    0    0     0     0  106  272  0  0 100  0  0
 0  0      1    303    135   1236    0    0     0     0  113  252  0  0 100  0  0
 0  0      1    303    135   1236    0    0     0     0   91  233  0  0 100  0  0
 0  0      1    303    135   1236    0    0     0     0   92  246  0  0 100  0  0
 0  0      1    303    135   1236    0    0     0     0   86  234  0  0 100  0  0
 0  0      1    303    135   1236    0    0     0     0   96  266  0  0 100  0  0
```
for 1M=1024KB, use `-S M` instead.


####Procs
Permalink
The procs data reports the number of processing jobs waiting to run and allows you to determine if there are processes “blocking” your system from running smoothly.
    
The `r` column displays the total number of processes waiting for access to the processor. 

The `b` column displays the total number of processes in a “sleep” state.
    
These values are often 0.

####MemoryPermalink
The information displayed in the memory section provides the same data about memory usage as the command `free -m`.

The `swpd` or “swapped” column reports how much memory has been swapped out to a swap file or disk. 

The `free` column reports the amount of unallocated memory. 

The `buff` or “buffers” column reports the amount of allocated memory in use. 

The `cache` column reports the amount of allocated memory that could be swapped to disk or unallocated if the resources are needed for another task.

####SwapPermalink
The swap section reports the rate that memory is sent to or retrieved from the swap system. By reporting “swapping” separately from total disk activity, vmstat allows you to determine how much disk activity is related to the swap system.

What is swap? see swap.md


The `si` column reports the amount of memory that is moved from swap to “real” memory per second. 

The `so` column reports the amount of memory that is moved to swap from “real” memory per second.

####I/OPermalink
The `io` section reports the amount of input and output activity per second in terms of blocks read and blocks written.

The `bi` column reports the number of blocks received, or “blocks in”, from a disk per second. The bo column reports the number of blocks sent, or “blocks out”, to a disk per second.

#### SystemPermalink
The system section reports data that reflects the number of system operations per second.

The `in` column reports the number of system interrupts per second, including interrupts from system clock. 

The `cs` column reports the number of context switches that the system makes in order to process all tasks.

CPUPermalink
The `cpu` section reports on the use of the system’s CPU resources. The columns in this section always add to 100 and reflect “percentage of available time”.


The `us` column reports the amount of time that the processor spends on userland tasks, or all non-kernel processes. 

The `sy` column reports the amount of time that the processor spends on kernel related tasks. 

The `id` column reports the amount of time that the processor spends idle. 

The `wa` column reports the amount of time that the processor spends waiting for IO operations to complete before being able to continue processing tasks.

### 2.uptime
```
vagrant@linux:/home/vagrant$ uptime
 15:22:38 up  5:26,  2 users,  load average: 0.00, 0.04, 0.06
```
uptime gives a one line display of the following information. The current time, how long the system has been running, how many users are currently logged on, and the system load averages for the past 1, 5, and 15 minutes.

Load > # of CPUs, may mean CPU saturation.

### 3.top (htop)
System and per-process interval summary: 
```
Tasks: 142 total,   1 running, 106 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.2 sy,  0.0 ni, 99.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  2041120 total,   295024 free,   406084 used,  1340012 buff/cache
KiB Swap:  2097148 total,  2095344 free,     1804 used.  1453912 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
17229 root      20   0  907880  44200  25148 S   0.7  2.2   0:18.58 containerd
19715 vagrant   20   0   41804   3696   3072 R   0.3  0.2   0:00.01 top
    1 root      20   0  159956   9080   6504 S   0.0  0.4   0:04.49 systemd
    2 root      20   0       0      0      0 S   0.0  0.0   0:00.00 kthreadd
    4 root       0 -20       0      0      0 I   0.0  0.0   0:00.00 kworker/0:0H
    6 root       0 -20       0      0      0 I   0.0  0.0   0:00.00 mm_percpu_wq
    7 root      20   0       0      0      0 S   0.0  0.0   0:00.28 ksoftirqd/0
    8 root      20   0       0      0      0 I   0.0  0.0   0:00.58 rcu_sched
    ...
```
• %CPU is summed across all CPUs
• Can miss short-lived processes (atop won’t)
• Can consume noticeable CPU to read /proc


# Managing Grafana Dashboard with Terraform

## Goal
* Use Grafana to manage a team's dashboards

## Steps
### 1. Spin up your grafana server locally
```
cd flask_statsd_prometheus
docker build -t jr/flask_app_statsd .
docker-compose -f docker-compose.yml -f docker-compose-infra.yml up
```
If you have trouble to spin it up, please follow the last week lecture notes to clean up your environment first.

### 2. Let us prepare a main.tf and variables.tf file
```
cd ..
mkdir terraform_grafana
touch main.tf
touch variables.tf
```
Your folder structure should now look like:
```
WK8_Monitoring_2
|_docs
|_...
|_terraform_grafana
    |_main.tf
    |_variables.tf
```

### 3. Find the provider
go to https://registry.terraform.io/ and search for grafana
![Alt text](../images/registry.png?raw=true)

click on the "grafana/grafana verified"

### 4. Copy the content to main.tf
![Alt text](../images/use_provider.png?raw=true)


### 5. Fill in the options so that you can connect to grafana
Checkout https://registry.terraform.io/providers/grafana/grafana/latest/docs for the example usage

Fill in the main.tf as follows:
```
terraform {
  required_providers {
    grafana = {
      source  = "grafana/grafana"
      version = "1.14.0"
    }
  }
}

provider "grafana" {
  url  = "http://localhost:3000"
  auth = var.grafana_auth
}
```

Fill in the variables.tf as follows:
```
variable "grafana_auth" {
  type    = string
  default = "admin:foobar"
}
```
This is to simplify the demonstration. You should avoid exposing your username/password

### 6. Let us do some formatting and validating
```
terraform init
terraform fmt
terraform validate
```

You should see
```
Success! The configuration is valid.
```

### 7. Check what you can do with the grafana provider
https://registry.terraform.io/providers/grafana/grafana/latest/docs/resources/dashboard

### 8. Share your already created dashboard as Json
Open localhost:3000 -> Manage -> Dashboards -> Your recently created dashboard -> Share dashboard
![Alt text](../images/share_dashboard.png?raw=true)
Export -> Save to file

Now move it under `terraform_grafana/`
```
mv ~/Downloads/<your dashboard.json> .
```

Once you finish creating your dashboard and panels, export it out as a json file

### 9. Add the dashboard to your terraform config
In main.tf add the following:
```
resource "grafana_dashboard" "metrics" {
  overwrite   = true
  config_json = file("Your dashboard.json")
}
```
What is the meaning of the config `overwrite`?

### 10. Try apply
```
terraform fmt
terraform validate
terraform apply
```

Try to `terraform destroy` the current one and redo the `terraform apply`.

Now, no matter what others may change your dashboard, you can simply recover it by `terraform apply`


### Homework
Are you able to config the data_source and the alert?

Tips:
https://registry.terraform.io/providers/grafana/grafana/latest/docs/resources/data_source
https://registry.terraform.io/providers/grafana/grafana/latest/docs/resources/alert_notification



# Setup a traffic generator

## Goal 
* We would like to generate traffic automatically to avoid manual refresh page
* Also, have an automated way to trigger alerts

## Steps

### 1. Write a quick traffic_generator.py
Identify the two endpoints that you would like to get tested on 
`/test/` and `/test1/`
```
mkdir traffic_generator
cd traffic_generator
touch traffic_generator.py
```
Add the following content to the traffic_generator.py
```
from locust import HttpUser, task, between


class QuickstartUser(HttpUser):
    wait_time = between(5, 9)  # will make the simulated users wait between 5 and 9 seconds

    # @task is the key. For every running user, Locust creates a greenlet (micro-thread), that will call those methods.
    # @task(5) indicates the weight is 5.
    @task
    def index_page(self):
        self.client.get("/test1/")

    @task(5)
    def view_organisation(self):
        self.client.get("/test/")

    def on_start(self):
        pass
```
Reference: https://docs.locust.io/en/stable/writing-a-locustfile.html

### 2a. Run the traffic generator
If you have python and locust installed, you can simply run: 
```
locust -f traffic_generator.py
```
open http://0.0.0.0:8089/ and then fill in the load test with the following parameters:
![Alt text](../images/run_locust_locally.png?raw=true)
The last entry is http://localhost:5000

Assuming you have started the `flask_statsd_prometheus`, click "Start swarming"



### 2b. Or wrap it up with Dockerfile and then run it
```
touch Dockerfile
```
add the following to the Dockerfile
```
FROM locustio/locust
COPY . .
ENTRYPOINT ["locust"]
CMD ["-f", "traffic_generator.py"]
```
build the image
```
docker build -t jr/traffic_generator . 
```
run locust via docker
```
docker run -p 8089:8089 --rm  --name traffic_generator jr/traffic_generator
```
open http://0.0.0.0:8089/ and then fill in the load test with the following parameters:
![Alt text](../images/run_locust_via_docker.png?raw=true)
http://host.docker.internal:5000

### 3. Adjust the parameters and observe the differences
* What if you change the weight of the task?
* What if you change the number of users?
* What if you change the spawn rate?
* What can you observe from the Grafana?

## 





## Error Rate & Throughput
Error Rate
```
sum (rate(request_count{status="500"}[15s])) / sum (rate(request_count[15s]))
```
Total Throughput
```
sum (rate(request_count[15s]))
```
Throughput by API
```
sum by (endpoint) (rate(request_count[15s]))
```
Ref: https://ibm-cloud-architecture.github.io/b2m-nodejs/Prometheus-Grafana/#throughput

## Request Duration
Average Request Duration
```
rate(request_latency_seconds_sum[1m])/rate(request_latency_seconds_count[1m])
```
Or use the percentile in three separate queries
```
request_latency_seconds{quantile="0.5"}
request_latency_seconds{quantile="0.9"}
request_latency_seconds{quantile="0.99"}
```
Ref: https://povilasv.me/prometheus-tracking-request-duration/

## Availability
Availability of your check

You will need to add a `/healthcheck` endpoint in your application and monitor the `/healthcheck` status via the
load balancer.  

## Saturation
### CPU
This is a counter
```
sum by (job) (irate(process_cpu_seconds_total[1m]))
```
https://stackoverflow.com/questions/48916798/monitoring-cpu-utilization-using-prometheus
https://utcc.utoronto.ca/~cks/space/blog/sysadmin/PrometheusRateVsIrate

### Memory
This is a gauge
```
process_resident_memory_bytes/1000/1000
```
### Disk
Leave it as a homework

## Export and redo the terraform step
You now should have a beautiful dashboard like this:
![Alt text](../images/dashboard_ready.png?raw=true)


# Integrate Grafana with OpsGenie

## 1.Register an OpsGenie Free Account
Register a free 14 days trial account by filling in the tables below and give a name for the site.

![Alt text](../images/register.png?raw=true)


## 2.Wait for the new instance spinning up
Once it is done, you should see the following page
![Alt text](../images/new_instance.png?raw=true)

## 3. Configure your profile
Put your phone number in and send test notifications. Do you receive a phone call?
![Alt text](../images/configure_phone.png?raw=true)

## 4. Setup a team
Give your team a name and invite yourself to be the owner
![Alt text](../images/setup_team.png?raw=true)

## 5. Enable Integration
Select grafana for the integration
![Alt text](../images/Integrate.png?raw=true)
Once saved, click integration
![Alt text](../images/grafana_config_1.png?raw=true)
You now should see the URL and API key
![Alt text](../images/grafana_config_2.png?raw=true)

![Alt text](../images/grafana_config_3.png?raw=true)

## 6. Spin up grafana
Follow these steps to spin up webapp, prometheus and grafana
```
cd flask_statsd_prometheus
docker-compose -f docker-compose.yml -f docker-compose-infra.yml up
```
Go to `localhost:3000` and go to Alerting -> Notification Channels
![Alt text](../images/notification_channel.png?raw=true)

Copy paste the API key and API URL in step 5 to here
And give it a name: `JiangRenMainAlert` (You can name it whatever you like)
![Alt text](../images/notification_channel_config.png?raw=true)
Save and test


## 7. Create a dashboard and a chart
Let us create a new dashboard and name it WebApp

Let us add a panel and name it WebApp Error Rate

Make sure it selected prometheus as source of data

To calculate the error rate, we need to calculate (total errors in X min/ total requests in X min)

Therefore, 
```
sum (rate(request_count{status="500"}[15s])) / sum (rate(request_count[15s]))
```

![Alt text](../images/query.png?raw=true)

We should have generated enough data on `localhost:5000/test` and `localhost:5000/test1` from the previous steps

## 8. Configure alert for the chart
Let us evaluate this alert every 15 seconds for 1 minute
And set a condition that if the value is above 0.5 (50%) for 10s then alert
![Alt text](../images/alert_1.png?raw=true)

![Alt text](../images/alert.png?raw=true)

Save and hit `localhost:5000/test1`

Do you receive an alert on your phone?

I have got a phone call, an email and a message in the OpsGenie portal
![Alt text](../images/triggered_alert.png?raw=true)
If you have OpsGenie App, it can also push notifications.

## 9. Other configs
Currently, the alert is a default alert. Of course, you could customise for you and your team.
To adjust the notification order or time, go to settings -> notification and configure the change alerts here 
![Alt text](../images/notif_config.png?raw=true)

For a team schedule, go to Teams -> <Your Team> -> On-call. Try to invite a classmate and schedule an on-call for him/her
![Alt text](../images/team_schedule.png?raw=true)

For any change that is made for the team, you can track the logs here:
![Alt text](../images/team_log.png?raw=true)


## Questions
Are you able to make P1 and P2 alerts call you immediately?
Hint: search google `grafana opsgenie priority`


# Build a comprehensive dashboards and alerting system


## Goal

Let us start with the WebApp that we have.

One thing we must be clear:

* Monitor -> For analysis
* Alerts -> For issues 

Alert is just a subset of Monitor. 

Each alert should map to a response action. 

## Starting from the 4 golden signals

According to the 4 golden signals, we need Error Rate, Throughput, Latency, Saturation. 

These 4 golden signals can be used as alerts.

* Would the monitor show a good picture of the webApp when we only introduce error rate?
* What about the healthy response?
* How do I tell if every host/region/url is functional? 
  
* Should we set alerts on them if they are not functional? What problems do we see?
* How we set a threshold? How to make the alert less noisy?


## Build the monitors first

You need to gain enough visibility to decide which metrics with which labels can be used for alerts

Let us build an all-inclusive Throughput, Success/Error Rate, Error count and Latency dashboard, which includes all the labels for now.


## Filter and play around with the threshold

The labels are your good companions and use them to filter your data. You may need to try a different math for the threshold calculation

For example, avg() is going to slow down the trigger of an alert. There are min(), max() and many other could help with triggering alerts faster.


## Test the alerts

Make sure you have tested the alerts yourself before making it available to prod. 
You can 
* set up a testbed environment 
* trigger the alerts via a script
* channel the alerts to slack to avoid being paged
* review the alerts


## When an alert triggered what do we do?

An alert means that the system may not be able to recover itself and require human to look into it.


## Questions
* Are you able to create a dashboard with variables that can monitor a WebApp comprehensively?
  (Hint: https://grafana.com/docs/grafana/latest/variables/)
* Are you able to monitor container saturation (CPU, memory, disk of the WebApp container)?
  (Hint: https://prometheus.io/docs/guides/cadvisor/)
