# Class-20_Logging

# ELK Hands-on

## Step 1. Clone the repo and read the ELK repo 

Let us get a docker-elk
```
git clone https://github.com/deviantony/docker-elk
```

Quickly read through the repo and identify logstash.conf

## Step 2. Spin up the ELK stack

Now, let us spin up the ELK stack by
```
cd docker-elk
docker-compose up setup
docker-compose up
```
Expecting to see something like this:
```
...
Creating docker-elk_elasticsearch_1 ... done
Creating docker-elk_kibana_1        ... done
Creating docker-elk_logstash_1      ... done
Attaching to docker-elk_elasticsearch_1, docker-elk_logstash_1, docker-elk_kibana_1
logstash_1       | OpenJDK 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release.
elasticsearch_1  | Created elasticsearch keystore in /usr/share/elasticsearch/config/elasticsearch.keystore
kibana_1         | {"type":"log","@timestamp":"2020-07-20T11:03:35Z","tags":["warning","plugins-discovery"],"pid":7,"message":"Expect plugin \"id\" in camelCase, but found: apm_oss"}
```
The stack is pre-configured with the following privileged bootstrap user:
```
user: elastic
password: changeme
```
## Step 3. Change the password
Execute the following three commands in `docker-elk`

```
docker-compose exec elasticsearch bin/elasticsearch-reset-password --batch --user elastic
docker-compose exec elasticsearch bin/elasticsearch-reset-password --batch --user logstash_internal
docker-compose exec elasticsearch bin/elasticsearch-reset-password --batch --user kibana_system
```

Sample output will look like this

```
❯ docker-compose exec elasticsearch bin/elasticsearch-reset-password --batch --user elastic
Password for the [elastic] user successfully reset.
New value: Wb7Yv6niUXEayOtNMCl*
❯ docker-compose exec elasticsearch bin/elasticsearch-reset-password --batch --user logstash_internal
Password for the [logstash_internal] user successfully reset.
New value: 3cHnndylWE0Dm3eHNx6N
❯ docker-compose exec elasticsearch bin/elasticsearch-reset-password --batch --user kibana_system
Password for the [kibana_system] user successfully reset.
New value: DzA=zWV8dxvTqsm=MVNS
```

Now, update `.env` in docker-elk

```
ELASTIC_VERSION=8.2.3

## Passwords for stack users
#

# User 'elastic' (built-in)
#
# Superuser role, full access to cluster management and data indices.
# https://www.elastic.co/guide/en/elasticsearch/reference/current/built-in-users.html
ELASTIC_PASSWORD='Wb7Yv6niUXEayOtNMCl*'

# User 'logstash_internal' (custom)
#
# The user Logstash uses to connect and send data to Elasticsearch.
# https://www.elastic.co/guide/en/logstash/current/ls-security.html
LOGSTASH_INTERNAL_PASSWORD='3cHnndylWE0Dm3eHNx6N'

# User 'kibana_system' (built-in)
#
# The user Kibana uses to connect and communicate with Elasticsearch.
# https://www.elastic.co/guide/en/elasticsearch/reference/current/built-in-users.html
KIBANA_SYSTEM_PASSWORD='DzA=zWV8dxvTqsm=MVNS'
```
and then restart your service
```
docker-compose down
docker-compose up
```

## Step 4. Check the docker containers
Check the containers and logs, make sure they are running
```
docker ps
CONTAINER ID   IMAGE                      COMMAND                  CREATED         STATUS         PORTS                                                                                            NAMES
7054d9fafeb9   docker-elk_logstash        "/usr/local/bin/dock…"   7 minutes ago   Up 7 minutes   0.0.0.0:5000->5000/tcp, 0.0.0.0:5044->5044/tcp, 0.0.0.0:9600->9600/tcp, 0.0.0.0:5000->5000/udp   docker-elk_logstash_1
d506ff044997   docker-elk_kibana          "/bin/tini -- /usr/l…"   7 minutes ago   Up 7 minutes   0.0.0.0:5601->5601/tcp                                                                           docker-elk_kibana_1
2dba2aed0b46   docker-elk_elasticsearch   "/bin/tini -- /usr/l…"   7 minutes ago   Up 7 minutes   0.0.0.0:9200->9200/tcp, 0.0.0.0:9300->9300/tcp                                                   docker-elk_elasticsearch_1
```
ELK stack - FILEBEAT (Installed on your webapp servers) + Logstash + Elastic Search + Kibana

![Alt text](images/high_level.png?raw=true)

* **Filebeat** is a lightweight shipper for forwarding and centralizing log data. Installed as an agent on your servers,
  Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to 
  Elasticsearch or Logstash for indexing.
  
* **Logstash** is a free and open server-side data processing pipeline that ingests data from a multitude of sources, 
  transforms it, and then sends it to your favorite "stash." 
  
* **Elasticsearch** is a distributed, free and open search and analytics engine for all types of data, including textual, 
  numerical, geospatial, structured, and unstructured. Elasticsearch is built on Apache Lucene and was first released 
  in 2010 by Elasticsearch N.V. (now known as Elastic). Known for its simple REST APIs, distributed nature, speed, and
  scalability, Elasticsearch is the central component of the Elastic Stack, a set of free and open tools for data 
  ingestion, enrichment, storage, analysis, and visualization.
  
* **Kibana** is a free and open frontend application that sits on top of the Elastic Stack, providing search and data 
  visualization capabilities for data indexed in Elasticsearch. Commonly known as the charting tool for the Elastic
  Stack (previously referred to as the ELK Stack after Elasticsearch, Logstash, and Kibana), Kibana also acts as the
  user interface for monitoring, managing, and securing an Elastic Stack cluster — as well as the centralized hub for built-in solutions developed on the Elastic Stack.

## Step 3. Load the test/sample data
### 1. The UI way:
Login to localhost:5601 -> Home (http://localhost:5601/app/kibana#/home) -> Add Sample data -> Sample web logs -> Add data
![Alt text](images/add_sample_data.png?raw=true)

Similarly, if you would like to load your own data via the UI, I would recommend to get a syslog data or generate your
own application log (See WK4 How we generate logfile in python)

* Syslog location
```
/var/log/syslog
```
Windows may use the event logs, which is under:
```
C:\Windows\System32\winevt\Logs
```
But I have not yet verified.

Login to localhost:5601 -> Home (http://localhost:5601/app/kibana#/home) -> Import a CSV, NDJSON, or log file
-> Drag and drop your syslog file -> give index a prefix "logstash-*" and select @timestamp -> click through

### 2. The CLI way:
You can go ahead and inject some log entries. The shipped Logstash configuration allows you to send content via TCP:
```
# Using GNU netcat (CentOS, Fedora, MacOS Homebrew, ...)
$ cat /path/to/logfile.log | nc -c localhost 5000
```

```
# Using BSD netcat (Debian, Ubuntu, MacOS system, ...)
$ cat /path/to/logfile.log | nc -q0 localhost 5000
```

What is `nc`? https://linux.die.net/man/1/nc
Open another terminal and Create an index pattern via the Kibana API:
```
$ curl -XPOST -D- 'http://localhost:5601/api/saved_objects/index-pattern' \
    -H 'Content-Type: application/json' \
    -H 'kbn-version: 7.8.0' \
    -u elastic:<your generated elastic password> \
    -d '{"attributes":{"title":"logstash-*","timeFieldName":"@timestamp"}}'
```
or you can login to the UI -> "Connect to your Elasticsearch index" -> give index a prefix "logstash-*" and select @timestamp

## Step 3 View the data
Click http://localhost:5601/app/kibana#/discover
![Alt text](images/discover_the_data.png?raw=true)

and you should see sample logs or logstash indexes

Please click through any button on the page to get familiar with it.

## KQL Knowledge
Filter
```
request: /kibana
```

```
response: 5*
```

```
host: *elastic* and not response 200
```

```
referer : http\://facebook.com* 
```

You must escape `\`
```
\():<>"*
```



## Step 4 Analyse the data
Let us go to dashboards http://localhost:5601/app/kibana#/dashboard
-> Create New -> Lens

What is Lens?
Kibana Lens is an easy-to-use, intuitive UI that simplifies the process of data visualization through a drag-and-drop 
experience. Whether you're exploring billions of logs or spotting trends from your website traffic, Lens gets you from
data to insights in just a few clicks — no prior experience in Kibana required.


Task: What we would like to understand is the health of the webapp: how many requests were returning 2** vs 5**

Without Lens:
![Alt text](images/status_code_chart.png?raw=true)

![Alt text](images/status_code_chart_config.png?raw=true)

With Lens:
![Alt text](images/lens.png?raw=true)

Could you also answer the following questions with line, bar or pie charts:
* where do most of the requests coming from over the last 7 days? Location wise and Ip wise?
* what is the percentage of the error logs?
* what are the top requests?
* what extension is used the most?
* what browser do most customers use?


## Homework
1. We would always wanna be notified if the system goes wrong, what does an alert mean?

   * The configuration is not set for this system. Could you do your own research and see how to set up an alert for the logging system?
e.g. alert when the number of 5xx > 10

2. Is it possible to set up dashboards with terraform?


# Integrate the ELK with filebeat

![Alt text](images/high_level.png?raw=true)
reference: https://www.freecodecamp.org/news/docker-container-log-analysis-with-elastic-stack-53d5ec9e5953/

## Step 1. Let us fix something in docker-elk repo
Go to the docker-elk folder that you cloned (mentioned in 01.elk_hands_on.md)
```
cd docker-elk
```
Open logstash -> pipeline -> logstash.conf and add 
```
index => "%{[@metadata][beat]}-%{[@metadata][version]}"
```
on line 18 
```
...
output {
	elasticsearch {
		hosts => "elasticsearch:9200"
		user => "elastic"
		password => "changeme"
		index => "%{[@metadata][beat]}-%{[@metadata][version]}"
	}
}
```
This is to index the logs that are sent from filebeats
## Step 2. Rebuild and run the docker-compose
```
docker-compose build
docker-compose up
```
In another window, type 
```
docker-compose ps
```
You should see
```
           Name                         Command               State                                               Ports                                             
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
docker-elk_elasticsearch_1   /bin/tini -- /usr/local/bi ...   Up      0.0.0.0:9200->9200/tcp, 0.0.0.0:9300->9300/tcp                                                
docker-elk_kibana_1          /bin/tini -- /usr/local/bi ...   Up      0.0.0.0:5601->5601/tcp                                                                        
docker-elk_logstash_1        /usr/local/bin/docker-entr ...   Up      0.0.0.0:5000->5000/tcp, 0.0.0.0:5000->5000/udp, 0.0.0.0:5044->5044/tcp, 0.0.0.0:9600->9600/tcp

```



## Step 3. Let us build the filebeat docker container
```
cd filebeat
docker build -t fcc/filebeat .
```
Build the docker image

## Step 4. Run the filebeat agent to collect docker logs
Run filebeat with the following command to collect docker logs 

```
docker run --rm -v '/var/lib/docker/containers:/usr/share/dockerlogs/data:ro' -v '/var/run/docker.sock:/var/run/docker.sock' --name filebeat fcc/filebeat:latest
```

In the above Docker command, note the two bind mount parameters: /var/lib/docker/containers is the path where docker 
logs exist within the host machine, and it has been bound to /usr/share/dockerlogs/data path within Filebeat container
with read only access.

In the second bind mount argument, /var/run/docker.sock is bound into the Filebeat container’s Docker daemon. 
It is the unix socket the Docker daemon listens on by default and it can be used to communicate with the daemon 
from within a container. This allows our Filebeat container to obtain Docker metadata and enrich the container log 
entries along with the metadata and push it to ELK stack.

## Step 5. login to kibana and try to create the index
Login to Kibana and Go to `Stack Management` -> `Kibana` -> `Data View` -> `Create`

![Alt text](images/create_data_view.png?raw=true)

You will not see `filebeat-7.2.0` index patterns but some generic ones only.

This is because we have errors in our logs

```
logstash_1       | [2022-06-27T11:42:29,498][INFO ][logstash.outputs.elasticsearch][main]
[495aaf9432a30cd7a341bf5aa2516b8e676e8f0b36e9f332d23786beb5574df2] Retrying failed action
 {:status=>403, :action=>["index", {:_id=>nil, :_index=>"filebeat-7.2.0", :routing=>nil}, 
 {"container"=>{"id"=>"3a070ddebde91dece2a008789f27d958bd851b674942efe850c09a1398fe33a2"}, 
 "stream"=>"stderr", "event"=>{"original"=>"W0625 07:31:11.091697       1 watcher.go:229] 
 watch chan error: etcdserver: mvcc: required revision has been compacted"}, 
 "tags"=>["beats_input_codec_plain_applied"], 
 "@version"=>"1", "host"=>{"name"=>"b86a955ef061"}, 
 "input"=>{"type"=>"docker"}, 
 "@timestamp"=>2022-06-25T07:31:11.094Z, 
 "message"=>"W0625 07:31:11.091697       1 watcher.go:229]
  watch chan error: etcdserver: mvcc: required revision has been compacted", 
  "agent"=>{"type"=>"filebeat", "ephemeral_id"=>"0d7f7643-59d2-40fc-b3c5-4174a9af49e9", 
  "hostname"=>"b86a955ef061", "version"=>"7.2.0", "id"=>"387bd947-1740-44fa-aed8-e607f5402b08"}, 
  "ecs"=>{"version"=>"1.0.0"}, "log"=>{"offset"=>168212, 
  "file"=>{"path"=>"/usr/share/dockerlogs/data/3a070ddebde91dece2a008789f27d958bd851b674942efe850c09a1398fe33a2/3a070ddebde91dece2a008789f27d958bd851b674942efe850c09a1398fe33a2-json.log"}}}], 
  :error=>{"type"=>"security_exception", 
                   "reason"=>"action [indices:admin/auto_create] is unauthorized for user [logstash_internal] with roles
                              [logstash_writer] on indices [filebeat-7.2.0], this action is granted by the index 
                              privileges [auto_configure,create_index,manage,all]"}}
```
Search `is unauthorized for user [logstash_internal] with roles
[logstash_writer]`

You will find this thread https://github.com/deviantony/docker-elk/issues/687

## Step 6. Fix the security issue

For simplicity, some may take the option to give logstash_internal a superuser privilege

![Alt text](images/superuser.png?raw=true)

After updating, you should see no more errors. However, you should never do this in production.

Instead, search the error with ChatGPT, you will get a more acceptable answer

The error message you provided appears to be related to a security exception in Elasticsearch. Let me break down the error message for you:

1. `type`: This field indicates the type of exception that occurred, which is a "security_exception" in this case. Security exceptions typically occur when there are issues related to user permissions and access control.

2. `reason`: The "reason" field provides a description of why the security exception occurred. In this case, it states that the action `[indices:admin/auto_create]` is unauthorized for the user `[logstash_internal]` with effective roles `[logstash_writer]` on the indices `[filebeat-7.2.0]`. It also mentions that the required action is granted by the index privileges `[auto_configure, create_index, manage, all]`.

To resolve this issue, you should consider the following steps:

1. **Review User and Role Permissions**: Ensure that the user `[logstash_internal]` has the appropriate permissions and roles assigned. In this case, it appears that the user has the `logstash_writer` role, but it may not have the necessary index privileges to perform the action `[indices:admin/auto_create]`.

2. **Grant Necessary Index Privileges**: As mentioned in the error message, the required action is granted by the index privileges `[auto_configure, create_index, manage, all]`. You may need to modify the user's role to include these index privileges or create a new role with the required privileges and assign it to the user.

3. **Check Index Settings**: Verify the settings of the index `[filebeat-7.2.0]`. Make sure that it allows the necessary actions, including index creation, according to the privileges defined in the user's role.

4. **Review Configuration**: Check your Logstash configuration to ensure that it is using the correct user and role for interacting with Elasticsearch. Any misconfiguration in Logstash can lead to authorization issues.

5. **Elasticsearch Version Compatibility**: Ensure that the version of Elasticsearch, Logstash, and the Beats (e.g., Filebeat) components are compatible and have no known security-related issues. Sometimes, upgrading or patching components can resolve such issues.

6. **Logging and Monitoring**: Implement proper logging and monitoring to help diagnose and troubleshoot security-related issues in your Elasticsearch cluster. This can provide insights into the root cause of the problem.

7. **Consult Elasticsearch Documentation**: If you continue to face issues, consult the official Elasticsearch documentation or seek assistance from your organization's Elasticsearch administrators or support team.

By addressing these points and ensuring that the user has the necessary permissions and index privileges, you should be able to resolve the security exception in Elasticsearch.

---

All you need to do is to go to roles -> logstash_writer -> index privileges add filebeat-* in the indices




## Step 7. Create the data view/index pattern

go to `stack management` from your sidebar

![Alt text](images/create_data_view.png?raw=true)

type `filebeat-*` and select `@timestamp`

click "Create data view"

## Step 6. Viola! Let us check the docker logs
You should see the following in `http://localhost:5601/app/discover` 
![Alt text](images/docker_logs.png?raw=true)


## Homework
Filebeat has been evolved and can replace logstash to some extent.
![Alt text](images/evo.png?raw=true)

Could you follow the guide and try this different setup? 
https://medium.com/@bcoste/powerful-logging-with-docker-filebeat-and-elasticsearch-8ad021aecd87

Once you have done the above practice, you should be able to set up logging for EasyCRM. Try it out and see how you go. 


##


## PYTHON LOGGING LEVELS
There are six log levels in Python; each level is associated with an integer that indicates the log severity: 
* NOTSET=0, 
* DEBUG=10, 
* INFO=20, 
* WARN=30, 
* ERROR=40,
* CRITICAL=50.

## Ways to log
1. Logging to Standard Output for Systemd
2. The alternative is to send it directly to syslog. This is great for older operating systems that don’t have systemd.
3. The final option is to log messages directly to a file.

## Python Components for Logging
### Log formatter
Formatting is important to machines to understand 

For example, when a log “hello world” is sent through a log formatter:
```
"%(asctime)s — %(name)s — %(levelname)s — %(funcName)s:%(lineno)d — %(message)s"
```
it will become
```
2018-02-07 19:47:41,864 - a.b.c - WARNING - <module>:1 - hello world
```

### Log handler
As the name suggested, this component is for developers to decide how to handle the data:
* Display/Write in the console (via StreamHandler)
* Display/Write in a file (via FileHandler)
* And many more ...

## Hands-on
Now, let us add logging in the console and in a file to our `flask_statsd_prometheus`

### Step 1. Create a logger file
Create a file under `flask_statsd_prometheus/src` and call it `my_logger.py`
and copy/paste in the following:
```
import logging
import sys
from logging.handlers import TimedRotatingFileHandler


FORMATTER = logging.Formatter("%(asctime)s — %(name)s — %(levelname)s — %(message)s")
LOG_FILE = "my_app.log"


def get_console_handler():
   console_handler = logging.StreamHandler(sys.stdout)
   console_handler.setFormatter(FORMATTER)
   return console_handler

def get_file_handler():
   file_handler = TimedRotatingFileHandler(LOG_FILE, when='midnight')
   file_handler.setFormatter(FORMATTER)
   return file_handler

def get_logger(logger_name):
   logger = logging.getLogger(logger_name)
   logger.setLevel(logging.DEBUG) # better to have too much log than not enough
   logger.addHandler(get_console_handler())
   logger.addHandler(get_file_handler())
   # with this pattern, it's rarely necessary to propagate the error up to parent
   logger.propagate = False
   return logger
```
Now, we have a logger that can log to a file and log to console

### Step 2. Introduce the logger to your main program

Add logger to your main program by openning `flask_app.py`
and add the following lines at the top
```
from my_logger import get_logger
log = get_logger(__name__)
```
and then to each function add either log.info or log.error
```
@app.route('/test/')
def test():
    log.info('hitting /test/ endpoint')
    return 'rest'
```

```
@app.errorhandler(500)
def handle_500(error):
    log.error(f'something went wrong {error}')
    return str(error), 500
```

### Step 3. Now let us try it out

You can either run it locally by 
```
python flask_app.py
```
or build and run via docker-compose.

Before proceed, please change 5000 to 5001 port `dockerfile` and `docker-compose.yml`
Because ELK is using 5000

Also, we need to change `flask_app.py` the last line to:
```
if __name__ == '__main__':
    app.run(port=5001)
```
After changing, run:
```
docker build -t jr/flask_app_statsd .
docker-compose -f docker-compose.yml
```



### Step 4. Figure out the problems
Now, when you hit `localhost:5001/test1`
You should be able to see this in your console
```
2021-10-26 10:17:35,098 — flask_app — ERROR — something went wrong division by zero
```
and a `my_app.log` in your `flask_statsd_prometheus/src` folder

If you run docker you should run 
```
docker exec -it webapp ls
```
to see the `my_app.log` file

But the stacktrace
```
Traceback (most recent call last):
  File "flask_app.py", line 22, in test1
    1/0
ZeroDivisionError: division by zero
```
is not there in the `my_app.log` file and not logged by logger

It is really important to log this info for debugging purpose as it points out the faulty file and the line no

### Step 5. Wrap around with try except to catch stacktrace
To log the stack trace, wrap the most fragile function with try except
and raise the exception to its parent function. You should write something like:
```
@app.route('/test1/')
def test1():
    log.info('hitting /test1/ endpoint')
    try:
        1/0
    except Exception as e:
        log.exception(e)
        raise
    return 'rest'
```

Now, rebuild or rerun and you should see the stacktrace get logged to the file and the console
```
2021-10-25 22:36:49,676 — __main__ — ERROR — division by zero
Traceback (most recent call last):
File "flask_app.py", line 22, in test1
1/0
ZeroDivisionError: division by zero
```
However, it is still not good enough, because it is in multiple lines and would require extra filters to be
written in filebeat. How do we turn it into one-liner?

## Step 6. Turn all logs into one liner
To be able to present the data better, we do need to add some logics back to the `my_logger.py`:
```
class OneLineExceptionFormatter(logging.Formatter):
    def formatException(self, exc_info):
        result = super().formatException(exc_info)
        return repr(result)  # The repr() function returns a printable representation of the given object

    def format(self, record):
        result = super().format(record)
        if record.exc_text:
            result = result.replace("\n", "")
        return result
```

And modify the my_logger.py file so it looks like 
```
import logging
import sys
from logging.handlers import TimedRotatingFileHandler

LOG_FILE = "my_app.log"


class OneLineExceptionFormatter(logging.Formatter):
    def formatException(self, exc_info):
        result = super().formatException(exc_info)
        return repr(result)  # The repr() function returns a printable representation of the given object

    def format(self, record):
        result = super().format(record)
        if record.exc_text:
            result = result.replace("\n", "")
        return result


def get_console_handler(formatter):
    console_handler = logging.StreamHandler(sys.stdout)
    console_handler.setFormatter(formatter)
    return console_handler


def get_file_handler(formatter):
    file_handler = TimedRotatingFileHandler(LOG_FILE, when='midnight')
    file_handler.setFormatter(formatter)
    return file_handler


def get_logger(logger_name):
    logger = logging.getLogger(logger_name)
    logger.setLevel(logging.DEBUG)  # better to have too much log than not enough in dev/staging
    one_line_formatter = OneLineExceptionFormatter("%(asctime)s — %(name)s — %(levelname)s — %(message)s")
    logger.addHandler(get_console_handler(one_line_formatter))
    logger.addHandler(get_file_handler(one_line_formatter))
    # with this pattern, it's rarely necessary to propagate the error up to parent
    logger.propagate = False
    return logger
```

Whenever we `addHandler`, we will get the handler to do `one_line_formatter` first.

Now, build and/or rerun, you should see something like this:
```
2021-10-25 22:44:00,039 — __main__ — INFO — hitting /test1/ endpoint
2021-10-25 22:44:00,039 — __main__ — ERROR — division by zero'Traceback (most recent call last):\n  File "flask_app.py", line 22, in test1\n    1/0\nZeroDivisionError: division by zero'
2021-10-25 22:44:00,045 — __main__ — ERROR — something went wrong division by zero
```

Viola, we now have proper logging for our webapp. 

## Step 7. Send logs to Kibana
Spin up your app or container
```
cd flask_statsd_prometheus
docker build -t jr/flask_app_statsd .
docker-compose -f docker-compose.yml
```
Now, you should have filebeat, ELK and your app up and running
```
docker ps
CONTAINER ID   IMAGE                      COMMAND                  CREATED          STATUS          PORTS                                                                                                                                                                        NAMES
3c5709600cb9   fcc/filebeat:latest        "/usr/local/bin/dock…"   8 minutes ago    Up 7 minutes                                                                                                                                                                                 filebeat
4f3eca24138d   jr/flask_app_statsd        "/bin/sh -c 'uwsgi -…"   40 minutes ago   Up 40 minutes   0.0.0.0:5001->5001/tcp, :::5001->5001/tcp                                                                                                                                    webapp
7caf66c5c07b   docker-elk_kibana          "/bin/tini -- /usr/l…"   58 minutes ago   Up 58 minutes   0.0.0.0:5601->5601/tcp, :::5601->5601/tcp                                                                                                                                    docker-elk_kibana_1
bd80a6313542   docker-elk_logstash        "/usr/local/bin/dock…"   58 minutes ago   Up 58 minutes   0.0.0.0:5000->5000/tcp, :::5000->5000/tcp, 0.0.0.0:5044->5044/tcp, :::5044->5044/tcp, 0.0.0.0:9600->9600/tcp, 0.0.0.0:5000->5000/udp, :::9600->9600/tcp, :::5000->5000/udp   docker-elk_logstash_1
e0c1c2045b79   docker-elk_elasticsearch   "/bin/tini -- /usr/l…"   58 minutes ago   Up 58 minutes   0.0.0.0:9200->9200/tcp, :::9200->9200/tcp, 0.0.0.0:9300->9300/tcp, :::9300->9300/tcp                                                                                         docker-elk_elasticsearch_1
```
They are connected like this:
![Alt text](images/high_level.png?raw=true)

You can also use the traffic generator to generate some traffic. 
Now, open the discover tab, you should be able to see your logs on Kibana.


### Homework
Could you figure out how to populate each field from the log message? 
e.g. log_level: ERROR, logger_name: __webapp__ etc


# Open question
* What is logging? 
* What log types do we know?
* If you are a developer, what do you use the logs for?
* Which tool do you use for logging?
* How do you tell if your application is buggy or not by log?
* How frequent do you collect the logs?
* What if you have millions of logs, how do you find the useful one?



# Logging basics
- Visibility is key for your application

## What is logging?
In computing, a log file is a file that records either events that occur in an operating system or other software runs,
 or messages between different users of a communication software. 
 Logging is the act of keeping a log. In the simplest case, messages are written to a single log file.

```
- Logging
   - Event Logs
   - Transaction Logs
```

### Event logs 
Event logs record events taking place in the execution of a system in order to provide an __audit__ trail that can be used 
to understand the activity of the system and to diagnose problems. They are essential to understand the activities of 
complex systems or applications.

Keywords: record, events, audit, activities of the system or application

### Transaction logs
 
Most database systems maintain some kind of transaction log, which are not mainly intended as an audit trail for later 
analysis, and are not intended to be human-readable. These logs record changes to the stored data to allow the database 
to recover from crashes or other data errors and maintain the stored data in a consistent state. Thus, database systems 
usually have both general event logs and transaction logs

Keywords: transaction, database, human-unreadable

Today, we focus mainly on event logs. We have talked about to many concepts. Let us look at the big picture.
What do we use the logs for and what does the logs look like?


# Why do we log?
## Logging Purpose
Debugging/Incident Analysis/Optimisation
  * err stack
  * src/dst/request rate
  * http info
  * request info
  * thread name
  * process id
  * avoid cross region requests 
 
Incident Detection
  * request rate/error rate
  * error threshold
  * capture NPE/runtime exception automatically

   
Health monitoring
  * request counts
  * request rate/error rate
  * request duration
  * request size
  * request method
  * healthcheck status
  
Auditing
  * time
  * user id
  * action name 
  * method
  
Rollback
  * transaction log only
 
  
# What do we log?
## System Logs vs Application Logs
```
* System Logs 
    * Linux/Unix/Windows/Docker Logs
* Application Logs
    * Access Logs (e.g. Apache Logs/Nginx Logs)
    * Web Server Logs (e.g. Python Logfile, Log4j)
    * Cache Logs (May Contain PII or UGC)
    * Database Logs (May Contain PII or UGC)
    * Message Queue Server Logs (e.g. Python Logfile, Log4j)
```

## System Logs

### Generic system activity logs
```
cat /var/log/messages # Ubuntu
```
or 
```
cat /var/log/syslog # Debian
```
* informational 
* non-critical system messages.

### Authentication events
```
cat /var/log/auth.log # Ubuntu/Debian
```
or 
```
cat /var/log/secure # CentOS
```
* authentication
* authorisation

### Initialisation Logs
```
cat /var/log/boot.log
```
* The system initialization script

### Device/driver messages
* Kernel ring buffer messages
* Hardware devices and their drivers

#Application Logs
### Apache server logs
```
cat var/log/httpd/
```
* logs recorded by the Apache server.
* error log and access log.

Error Log - the web server encountered when processing requests
```
[Thu Mar 13 19:04:13 2014] [error] [client 50.0.134.125] File does not exist: /var/www/favicon.ico
```

Access Log - requests coming in to the web server
```
10.185.248.71 - - [09/Jan/2015:19:12:06 +0000] 808840 "GET /inventoryService/inventory/purchaseItem?userId=20253471&itemId=23434300 HTTP/1.1" 500 17 "-" "Apache-HttpClient/4.2.6 (java 1.5)"
```


### Access Log
- HTTP Request passing through the load balancer/apache server
    * Time
    * Status Code
    * Url Path
    * Remote IP or Host
    * Request Time Seconds
    * Request ID
    * User Agent
    * Region Info
    * Thread Name
    * Process Id
    * Trace Id
    
### Application Log    
- HTTP Request passing through the application
    * Server info (EC2 ID, IP, Version etc..)
    * Deployment info (Deployment Id or Tag)
    * Log Level
    * Logger Name (Class)
    * Error Message
    * Request ID
    * Url Path
    * Region Info
    * (Optional) Tenant Url
    * (Optional) Shard Info
    



# How do we log?
Modern logging system contains three parts:

1) Logging Aggregator/Forwarder
  * Collect/Aggregate logs
  * Forward Logs
  * Process the log format

2) Search 
  * Indexing by default/customisation

3) Visualisation 
  * UI Search bar
  * Timeline
  * Pie/Bar/Other Charts

![Alt text](images/technolush-elk-stack.png?raw=true)

The most common ones: 
* ELK stack- FILEBEAT + Logstash + Elastic Search + Kibana
* Splunk stack - Fluentd + Splunk


## Log Aggregation
https://sematext.com/blog/log-aggregation/
* File Replication
  - copy your log files to a central location such as rsync and cron. 
  
    However, although it does bring together all of your logs, this option is not really the same as an aggregation, 
    but more of a “co-location.” 
    
    Furthermore, since you need to follow a cron schedule, in the long-term, file replication is not a good solution
    as you don’t get real-time access to your log data.
    
* Forwarder(e.g. Syslog) 
  - They allow processes to send log entries to them that they’ll then redirect to a central location.
    
    You need to set up a central syslog daemon on your network as well as the clients. 
    The client logging daemons will forward these messages to the daemons.
                                                                                     
    Syslog is also a simple method to aggregate your logs since you have already installed it and you
    only have to configure it. The catch is to make sure the central syslog server is available and 
    figure out how to scale it.
    https://en.wikipedia.org/wiki/Syslog
    
    
## Example:  Using Logstash to collect/aggregate logs    
![Alt text](images/logstash-instance-input-filter-output-plugins.png?raw=true)

Logstash is a plugin-based data collection and processing engine. It comes with a wide range of plugins that makes it 
possible to easily configre it to collect, process and forward data in many different architectures.

Processing is organized into one or more pipelines. In each pipeline, one or more input plugins 
https://www.elastic.co/guide/en/logstash/current/input-plugins.html receive or collect data
that is then placed on an internal queue. This is by default small and held in memory, but can be configured to be 
larger and persisted https://www.elastic.co/guide/en/logstash/6.2/persistent-queues.html on disk in order to improve
 reliability and resiliency.
 
### What is the persistent queue for? How does it improve the reliability or resiliency?
There are chances that logstash can fail/crash with errors such as filter errors or cluster unavailability. This can
lead to data loss in the log monitoring system. To guard against such data loss, Logstash provides data resilience 
mechanisms such as persistent queues and dead letter queues.

### Where is the log data kept?
Answer: Disk

In the default configuration, Logstash keeps the log data in in-memory queues. The size of these in-memory queues is 
fixed and not configurable. As the primary memory is volatile, in case of machine failures, the data in the memory 
queue will be lost. If we enable the persistent queue mechanism in Logstash, the message queue will be stored in 
the disk. Enabling this feature helps in removing additional architectural modification by adding some buffering layer 
before Logstash pipelines. Also, this assures log delivery even in the case of system shutdown, restart, or temporary 
failure.


Persistent queue works in between the input and filter section of Logstash. To configure persistent queue-enabled 
Logstash, we need to update the logstash.yml.

```
queue.type: persisted
path.queue: "path/to/data/queue" 
queue.max_bytes: 4gb
```

In addition to persistent queue, we can use dead letter queue to store write failed events, process them, 
and place them back in the Elasticsearch index. This feature works only in the Elasticsearch output option. 
For configuring this change, we need to add the following configuration settings in the logstash.yml file. 
Also, we can define the size of the dead letter queue by setting dead_letter_queue.max_bytes. The default value is 1gb.
 Logstash has the dead_letter_queue input plugin to handle the dead letter queue pipeline.
 
```
dead_letter_queue.enable: true
path.dead_letter_queue: "path/to/data/dead_letter_queue"
dead_letter_queue.max_bytes: 2gb
```

### What if the server memory is not big enough?

We can ensure data resilience by using Logstash persistent queuing mechanism in small environments with a low volume of
logs. But in the case of log monitoring systems that handle a large volume of logs, we need to add some message
queueing mechanism in between Beats and Logstash. Three commonly used tools for message queueing are Apache Kafka, 
Redis, and RabbitMQ.

![Alt text](images/Elastic-Stack-architecture-with-buffering-layer.png?raw=true)

#####Extra reading (https://blog.qburst.com/2020/01/a-deep-dive-into-log-monitoring-using-elastic-stack/)
* Apache Kafka: Apache Kafka is a distributed streaming platform that can publish and subscribe to streams of records. 
The components that generate streams (here logs) and send them to Kafka are the publishers (here it is Beats) and the
components that pull logs from Kafka are the subscribers (here it is Logstash). Kafka stores data in different topics.
Each topic has a unique name across the Kafka cluster. The publisher can write messages to Kafka-topics and consumers
can consume data from the Kafka-topics.

* Redis: Redis is an open-source, in-memory data store that can be used as a message broker. 
It stores data as key-value pairs. Redis is really fast as it uses in-memory data store. 
On the other hand, it becomes a weakness when memory is full. Log data will be dropped when the Redis queue is full.
Redis also offers disk-based data persistence to avoid data loss, but the performance will be reduced due to disk
latency.

* RabbitMQ: RabbitMQ is an open-source enterprise message queueing system based on Advanced Message Queueing Protocol
(AMQP) written in Erlang. In RabbitMQ, we have brokers, producers, and consumers. Brokers are responsible for tracking
which messages are delivered to which consumers. RabbitMQ is a good choice when you have less than thousands of logs
per second.

### What is filters/outputs?
Processing threads read data from the queue in micro-batches and process these through any configured filter plugins
in sequence. Logstash out-of-the-box comes with a large number of plugins targeting specific types of processing, and
this is how data is parsed, processed and enriched.
 
Once the data has been processed, the processing threads send the data to the appropriate output plugins, which
are responsible for formatting and sending data onwards, e.g. to Elasticsearch.
 
Input and output plugins can also have a codec plugin configured. This allows parsing and/or formatting of data
before it is put onto the internal queue or sent to an output plugin.


#### Filters
Filters are intermediary processing devices in the Logstash pipeline. You can combine filters with conditionals to perform an action on an event if it meets certain criteria. Some useful filters include:

* grok: parse and structure arbitrary text. Grok is currently the best way in Logstash to parse unstructured log data into something structured and queryable. With 120 patterns built-in to Logstash, it’s more than likely you’ll find one that meets your needs!
mutate: perform general transformations on event fields. You can rename, remove, replace, and modify fields in your events.
* drop: drop an event completely, for example, debug events.
* clone: make a copy of an event, possibly adding or removing fields.
* geoip: add information about geographical location of IP addresses (also displays amazing charts in Kibana!)
For more information about the available filters, see Filter Plugins https://www.elastic.co/guide/en/logstash/6.2/filter-plugins.html.

#### Outputs

Outputs are the final phase of the Logstash pipeline. An event can pass through multiple outputs, but once all output
processing is complete, the event has finished its execution. Some commonly used outputs include:

* elasticsearch: send event data to Elasticsearch. If you’re planning to save your data in an efficient, convenient, and easily queryable format…​Elasticsearch is the way to go. Period. Yes, we’re biased :)
* file: write event data to a file on disk.
* graphite: send event data to graphite, a popular open source tool for storing and graphing metrics. http://graphite.readthedocs.io/en/latest/
* statsd: send event data to statsd, a service that "listens for statistics, like counters and timers, sent over UDP and sends aggregates to one or more pluggable backend services". If you’re already using statsd, this could be useful for you!
For more information about the available outputs, see Output Plugins.


Let us look at some examples in "parsing the logs", "filters" and "transforming logs"
https://www.tutorialspoint.com/logstash/logstash_parsing_the_logs.htm

Extra Reading: 
https://www.elastic.co/guide/en/logstash/current/config-examples.html
https://sematext.com/blog/getting-started-with-logstash/


## Traditional logging 
### Linux/Unix
One of the most important logs contained within /var/log is syslog. This particular log file logs everything except auth-related messages. Say you want to view the contents of that particular log file. To do that, you could quickly issue the command less /var/log/syslog. This command will open the syslog log file to the top. You can then use the arrow keys to scroll down one line at a time, the spacebar to scroll down one page at a time, or the mouse wheel to easily scroll through the file.

```
/var/log
```
or

```
tail -f  /var/log/syslog
```

The dmesg command prints the kernel ring buffer. By default, the command will display all messages from the kernel ring buffer. From the terminal window, issue the command dmesg and the entire kernel ring buffer will print out.
```
dmesg | less
```

### Application
![Alt text](./images/linear_log.png?raw=true)

##Why log?
Analyse business metrics (e.g. DAU, MAU, SLI, Customer Behavior etc)

Identify security threats (e.g. to find out and block the attacker's source IP and user agent)

Trouble shooting
* Identify bugs and errors (e.g. application bugs/errors vs system bugs/errors)
* Incident Investigation 

##Challenges
Tens of thousands of lines are generated per minute for a software like us. The traditional logs exploring is not gonna work.


##Logging as a service

__Logging as a service (LaaS)__ is an IT architectural model for centrally ingesting and collecting any type of log files coming from any given source or location; such as servers, applications, devices etc. The files are "normalized" or filtered for reformatting and forwarding to other dependent systems to be processed as “native” data, which can then be managed, displayed and ultimately disposed of according to a predesignated retention schedule based on any number of criteria.

In an enterprise situation, the IT datacenter becomes the hub for all log files and normalization. In a managed service provider (MSP) environment, the log sources would be coming from applications outside the enterprise but still hosted and managed by the MSP as needed.

Under this model, the IT datacenter acts as the "private cloud" under the concept of cloud computing to provision the logs to various stakeholders within the organization for future forensics or analysis to identify risks, patterns of activity or predict behaviors based on the data collected within the logs. Just as IT becomes the "hub" of the service, the stakeholders become the beneficiaries of the centralized data in the form of alerts, reports or any periphery applications for predictive analysis or insight from big data through graphical display.

![Alt text](./images/Logging_as_a_Service_Architectural_Model.jpg?raw=true)

## Splunk

Check out what splunk can do
![Alt text](./images/splunk_capability.png?raw=true)

##What to log?

### Authentication, Authorization, and Access:
These events include things such as 
- successful and failed authentication
- authorizations 
- system access 
- data access
- application access.

Example of Authentication logs:
 - TIMESTAMP
 - ACCESS_METHOD
 - STATUS_CODE
 - RESPONSE_TIME
 
Example of Authorization logs:
 - User ID
 - Role
 - Authorized

Example of Nginx access logs:
 - TIMESTAMP
 - SOURCE_IP
 - URL
 - HTTP_USER_AGENT
 - STATUS_CODE
 - URL_PATH
 - RESPONSE_TIME
 
 

 
### Changes

These events include changes to systems or applications, changes to data (creation and destruction) and application installation and changes.

Example of changes to systems
* os image version update
* network/interface update
* kernel update

Example of changes to applications 
* application image version update
* application components update

Example of changes to data
* column update
* data upgrade

### Availability

Availability events include startup and shutdown of systems and applications, faults and errors that affect application availability and backup successes and failures.

Example of availability events
* healthcheck
* startup/shutdown system logs
* startup/shutdown component logs
* startup/shutdown application logs
* cronman job/backup failures or errors

### Resource 

Resource issues to log include exhausted resources, exceeded capacities and connectivity issues.

Example of resource logs
* exhausted threads/connections of each component (e.g. EC2, Docker, RDS etc...)
* exhausted cpu/disk/memory log (better to auto trigger threaddump, heapdump, flamechart or other info) 
* over capacity queues

### Threats

Some common threats to logs include invalid inputs and security issues known to affect the application.

* Hacker input
* Common User Behaviour

## Nginx logs

Let us firstly create a folder and two files for logging
```
sudo mkdir /usr/share/nginx/logs
sudo touch /usr/share/nginx/logs/error.log
sudo touch /usr/share/nginx/logs/access.log
```
My default folder is under `/usr/share/nginx/`, you can check yours by running
```
nginx -V
```
and see what is under `--prefix`

Now, Let us check what is the new in nginx.conf

Let us replace the nginx.conf 
```
cp nginx.conf /etc/nginx/nginx.conf
```

Restart nginx
```
sudo systemctl reload nginx 
```


Try access now; 

```
localhost:8081
```

You should see a 502 error, because we haven't set up the app yet

```
cd ../WK4_WebApp_Basics
python3 app.py
```
try access few endpoints now
```
localhost:8081/abc
localhost:8081/upload
localhost:8081/signin
localhost:8081/signup
localhost:8081/login
```
Now let us check the logs
```
less /usr/share/nginx/logs/error.log
less /usr/share/nginx/logs/access.log
```
oh, by the way, don't forget to check
```
tail -f  /var/log/syslog
```

## Fluentd
Fluentd collects events from various data sources and writes them to files, RDBMS, NoSQL, IaaS, SaaS, Hadoop and so on. Fluentd helps you unify your logging infrastructure (Learn more about the Unified Logging Layer).

An event consists of tag, time and record. Tag is a string separated with '.' (e.g. myapp.access). It is used to categorize events. Time is a UNIX time recorded at occurrence of an event. Record is a JSON object.

### Use case
* Centralizing Apache/Nginx Server Logs
* Syslog Alerting	
* Mobile/Web Application Logging


### Installation
Install ruby
```
sudo apt-get install ruby-full
```

Install fluentd https://github.com/fluent/fluentd
```
$ gem install fluentd
$ fluentd -s conf
$ fluentd -c conf/fluent.conf &
$ echo '{"json":"message"}' | fluent-cat debug.test
```
![Alt text](./images/fluentd-architecture.png?raw=true)

Install fluentd-ui https://github.com/fluent/fluentd-ui

You may need to use `sudo`
```
$ gem install fluentd-ui
$ fluentd-ui setup
$ fluentd-ui start --daemonize
```
The default account is username="admin" and password="changeme".

### Collect logs

Now, let us install a multiline parser and hook the nginx logs to fluentd
```
fluent-gem install fluent-plugin-multi-format-parser
```
https://docs.fluentd.org/parser/nginx
```
cp fluent.conf /conf/fluent.conf
sudo pkill -f fluentd
fluentd -c conf/fluent.conf -vv &
```
Try hit the endpoints again.

#### Homework
In `nginx.conf`, use the following to log the message:
```
    log_format   main '$remote_addr - $remote_user [$time_local]  $status '
                      '"$request" $body_bytes_sent "$http_referer" '
                      '"$http_user_agent"';
    access_log   logs/access.log  main;
```

How would you configure fluent.conf to print out the following message?
```
time:
1362020400 (28/Feb/2013:12:00:00 +0900)

record:
{
  "remote" : "127.0.0.1",
  "host"   : "192.168.0.1",
  "user"   : "-",
  "method" : "GET",
  "path"   : "/",
  "code"   : "200",
  "size"   : "777",
  "referer": "-",
  "agent"  : "Opera/12.0"
}
```
### How does fluentd work?
https://www.fluentd.org/blog/unified-logging-layer

More examples: https://docs.fluentd.org/deployment/fluentd-ui


# A taste of Splunk 
## Step1. Download
Fill in the following details and download
![Alt text](./images/splunk_download.png?raw=true)

## Step2. Install
If there is a GUI, click install; otherwise, run
```
sudo dpkg -i splunk_xxx.deb
```

## Step3. Run splunk
At the first time running splunk, it will ask for the initial setup for the admin account.
![Alt text](./images/splunk_installation.png?raw=true)
```
cd /opt/splunk/bin/
sudo ./splunk start --accept-license
```

## Step4. Login to splunk
Use the username and password that you set in the last step to login

## Step5. Download the data
Let us download some sample data
https://data.montgomerycountymd.gov/
https://data.montgomerycountymd.gov/Finance-Tax-Property/County-Spending/vpf9-6irq

## Step6. Add the data to splunk
Add Data -> upload from my computer -> select *.csv from ~/Download

## Step7. A Few Selection

__Source type__: this helps Splunk to learn what data you have got. 
    * You specify a directory as a data source.
    * You specify a network input as a data source.
    * You specify a data source that has been forwarded from another Splunk instance.

Let us select "Automatic" in this short example.

__Host__: When the Splunk platform indexes data, each event receives a "host" value. The host value should be the name of the machine from which the event originates. The type of input you choose determines the available configuration options.

__Index__:The Splunk platform stores incoming data as events in the selected index. Consider using a "sandbox" index as a destination if you have problems determining a source type for your data. A sandbox index lets you troubleshoot your configuration without impacting production indexes. You can always change this setting later.

Let us use the default.

Now choose review

## Step9. You should see an error
The error is about Splunk cannot recognise the time format
go to `/opt/splunk/etc/system/local/props.conf`

add the following lines

```
TIMESTAMP_FIELDS = Invoice Date
TIME_FORMAT=%m/%d/%Y
```
and restart Splunk

```
cd /opt/splunk/bin/
./splunk restart
```

### Step10. Reload the data
Now there should be no error. Let us submit the data and start searching!


