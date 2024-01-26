

Lecturer: William Wang

# Review homework and Docker&Git related stuff
- [TOPIC 1.Docker install](#topic-1docker-install)
- [TOPIC 2.Homework](#topic-2homework)
- [TOPIC 3.Popular Linux CLI](#topic-3popular-linux-cli)
- [TOPIC 4.VSCODE plugin](#topic-4vscode-plugin)
- [TOPIC 5.Git](#topic-5git)
- [TOPIC 6.Troubleshooting](#topic-6troubleshooting)



# TOPIC 1.Docker install
https://github.com/AutomationLover/Devops/blob/main/t02/docker_install.md


https://docs.docker.com/get-docker/


https://docs.docker.com/desktop/faqs/linuxfaqs/#what-is-the-difference-between-docker-desktop-for-linux-and-docker-engine

https://docs.docker.com/desktop/install/linux-install/
https://docs.docker.com/engine/install/ubuntu/


Key differences summarized based on the provided information:

Installation Compatibility:

Docker Desktop for Linux is intended for Linux-based systems.
Docker Engine is also compatible with Linux but doesn't provide a dedicated graphical interface like Docker Desktop.
Storage Isolation:

Docker Desktop for Linux isolates containers and images within a virtual machine (VM). This isolation prevents interference with other Docker Engine installations on the same machine.
Docker Engine for Linux runs containers natively on the host system and stores them in the host's file system.
Resource Controls:

Docker Desktop for Linux offers controls to restrict the resources allocated to it within the VM.
Docker Engine for Linux can have resource controls configured at the container level but typically doesn't have inherent resource controls for the Docker daemon itself.
This information clarifies that Docker Desktop for Linux is designed to provide a user-friendly interface and isolation for containers on Linux systems, making it suitable for development and testing environments. In contrast, Docker Engine for Linux is often used in production environments and may require manual resource management.




# TOPIC 2.Homework
## CDN
Q: https://github.com/JiangRenDevOps/DevOpsNotes/blob/master/WK1_CDN_DNS/homework.md 
A: https://github.com/AutomationLover/Devops/blob/main/t02/Homework_DNS_CDN.md


## Question
### Draw a infrastructure architecture diagram

Draw a infrastructure architecture diagram for the hands-on that you just did for CDN and DNS. It should include 3 components:

1. the jiangren.com.au website
2. the CloudFront CDN component
3. the Route53 hosted zone

Please reflect following in the diagram:

1. the relationship between above components
2. the traffic flow when a user requests your "your_name.jiangrendevops.com" website. i.e. what happens after a user type in "your_name.jiangrendevops.com" website in a browser?

You can use [draw.io](https://app.diagrams.net/) to draw this diagram easily. Make sure you save both XML file(.drawio) and the diagram picture file (.png) when you finish, so that you can edit your diagram again in the future.
(source from 
https://github.com/JiangRenDevOps/DevOpsNotes/blob/master/WK1_CDN_DNS/homework.md)

## Answer
### Diagram

```
User (Browser) ---> Route53 (DNS Service) ---> CloudFront (CDN)
   ^                                               |
   |                                               |
   +-----------------------------------------------+
                                (if necessary)
                                jiangren.com.au (Origin Server)
```

### Explain
Let's break down the diagram and explain each step in a simple way:

1. **User (Browser)**: This is the person visiting your website. They type in your website's URL (in this case, "your_name.jiangrendevops.com") into their web browser.

2. **Route 53 (DNS Service)**: DNS stands for Domain Name System, and it's essentially the phone book of the internet. When the user types in your website's URL, that request goes to Route 53. Route 53 looks up the URL and finds out where the website's content is stored. In this case, it's stored on a CDN network (CloudFront).

3. **CloudFront (CDN)**: CDN stands for Content Delivery Network. This is a network of servers that store copies of your website's content in different locations around the world to deliver it faster to users. When Route 53 directs the user's request to CloudFront, the CDN checks if it has a recent copy of the requested content. If it does, it sends that content directly to the user's browser.

4. **jiangren.com.au (Origin Server)**: This is where the original, master copy of your website is stored. If CloudFront doesn't have a recent copy of the requested content, it goes back to the origin server to fetch the latest version. After fetching the content, CloudFront delivers it to the user and also stores a copy for future requests.

5. **Back to User**: Regardless of whether the content is served directly from the CDN or after a round trip to the origin server, the final content is delivered back to the user's browser for them to view.

The main goal of this setup is to make your website load faster for users. By storing copies of your website at different locations closer to users (CDN), and by using a smart lookup system (DNS), you can serve your website content more quickly and efficiently.



## Docker
Q: https://github.com/JiangRenDevOps/DevOpsNotes/blob/master/WK3_Dockerisation/5.homework.md
Task1: Try to read and understand the docker-cli, dockerfile and docker-compose file.
A: https://github.com/AutomationLover/Devops/blob/main/t02/HOMEWORK_DOCKER_2.md

Task2: Explain line-by-line to your team member.
A: https://github.com/AutomationLover/Devops/blob/main/t02/HOMEWORK_DOCKER_1.md

Task3: Try spin up the service follow the README file.
A: https://github.com/AutomationLover/Devops/blob/main/t02/HOMEWORK_DOCKER_3.md
A: https://github.com/AutomationLover/Devops/blob/main/t02/HOMEWORK_DOCKER_extend.md
A: https://github.com/AutomationLover/Devops/blob/main/t02/TS_port.md
-------------------------------------------------------------------------------------------------
For https://github.com/HenryQW/Awesome-TTRSS/blob/main/Dockerfile

This Dockerfile appears to be used for building a Docker image, likely for running an instance of Tiny Tiny RSS (TT-RSS), a web-based RSS feed reader. Let me break down each section of this Dockerfile and explain its purpose:

Builder Stage (First FROM Block):

FROM docker.io/alpine:3 AS builder: This sets the base image for the build process. It uses the Alpine Linux 3 image as a starting point and assigns it the name 'builder' for this build stage.

WORKDIR /var/www: Sets the working directory for subsequent commands to /var/www.

RUN apk add --update tar curl git: Installs the necessary packages (tar, curl, git) to facilitate the build process.

RUN rm -rf /var/www/*: Clears the contents of the /var/www directory.

RUN git clone ...: Clones the TT-RSS repository from Git and places it in the /var/www directory.

Several RUN mkdir and curl commands download various TT-RSS plugins and place them in specific directories within /var/www/plugins.local.

ADD https://raw.githubusercontent.com/jangernert/FeedReader/master/data/tt-rss-feedreader-plugin/api_feedreader/init.php api_feedreader/: Downloads a file from a URL and places it in the api_feedreader directory.

More plugins and themes are downloaded and configured in this stage.

Main Image Stage (Second FROM Block):

FROM docker.io/alpine:3: Sets a new base image for the final image that will be created.

LABEL maintainer="Henry<hi@henry.wang>": Adds a label to the image indicating the maintainer's contact information.

WORKDIR /var/www: Sets the working directory for subsequent commands to /var/www.

Several COPY commands copy various configuration files and scripts from the local filesystem into the image.

ENV commands set environment variables that configure various aspects of the TT-RSS application and its behavior.

RUN commands install necessary packages and dependencies using the Alpine package manager (apk). These include PHP and related extensions, Nginx, S6 process manager, and other utilities.

ENV LANG=C.UTF-8: Sets the locale to UTF-8.

RUN commands install the GNU C Library (glibc) to support certain applications that require it, such as PHP.

COPY --from=builder /var/www /var/www: Copies the contents of the /var/www directory from the builder stage into the final image. This includes TT-RSS and the plugins.

EXPOSE 80: Exposes port 80 for incoming HTTP traffic.

Several ENV commands set environment variables that configure the TT-RSS application, including database connection settings and various options.

ENTRYPOINT ["sh", "/docker-entrypoint.sh"]: Specifies the entry point for the container. When the container starts, it will execute the /docker-entrypoint.sh script.

In summary, this Dockerfile is used to create a Docker image that sets up a TT-RSS instance with various plugins and themes. It also includes the necessary dependencies such as PHP, Nginx, and glibc. When you run a container from this image, it should start a TT-RSS instance that you can access via a web browser. The docker-entrypoint.sh script likely handles the startup and configuration of the TT-RSS application within the container.


## Question

 Try to read and understand the docker-cli, dockerfile and docker-compose file


## Answer
Docker CLI, Dockerfile, and Docker Compose files are essential components of Docker. Here's how to read and understand each of them:

1. **Docker CLI (Command Line Interface)**: This is the main way users interact with Docker. By typing commands in your terminal or command prompt, you can build, run, and manage your Docker images and containers. Docker CLI commands are well-documented in the Docker documentation. The basic syntax is `docker <command> <sub-command> (options)`. For instance, `docker container run -it ubuntu bash` runs an interactive bash shell in the Ubuntu container.

2. **Dockerfile**: A Dockerfile is a text file that contains a list of commands that the Docker daemon uses to build an image. Dockerfile uses a simple DSL (Domain-specific Language) that lets you automate the steps you would otherwise perform manually to create an image. 

   Here's an example of a simple Dockerfile:
   ```
   # Use an official Python runtime as a base image
   FROM python:2.7-slim

   # Set the working directory in the container to /app
   WORKDIR /app

   # Add the current directory contents into the container at /app
   ADD . /app

   # Install any needed packages specified in requirements.txt
   RUN pip install --trusted-host pypi.python.org -r requirements.txt

   # Make port 80 available to the world outside this container
   EXPOSE 80

   # Run app.py when the container launches
   CMD ["python", "app.py"]
   ```
   `FROM` sets the base image for the Dockerfile, `WORKDIR` sets the working directory, `ADD` adds the files from your Docker client's current directory, `RUN` installs any necessary dependencies, `EXPOSE` tells Docker to listen on the specified network ports at runtime, and `CMD` provides defaults for an executing container.

3. **Docker Compose file (docker-compose.yml)**: Docker Compose allows you to define and manage multi-container Docker applications. It uses YAML files to configure application services (like linking a database to a web server). Here's an example of a basic docker-compose.yml file:

   ```
   version: '3'
   services:
     web:
       build: .
       ports:
         - "5000:5000"
     redis:
       image: "redis:alpine"
   ```
   `version` specifies the Docker Compose version, `services` lists your app's services (in this case, web and redis), the `build` directive tells Docker to build the Dockerfile in the current directory, and `ports` maps the host's port to the container's port. `image` specifies the image to start the container from.

Reading and understanding these components are essential to using Docker effectively. If you are stuck or confused, the Docker documentation is a comprehensive and helpful resource.


### Docker Compose vs. Kubernetes
Some frequently asked questions related to Docker Compose and Kubernetes (k8s):

1. **What is the difference between Docker Compose and Kubernetes?**

Docker Compose and Kubernetes both allow you to define and manage multi-container applications, but they differ in their complexity and use cases. Docker Compose is a simpler tool that's great for development environments and small scale applications. It allows you to define services, networks, and volumes in a single file, and manage them with simple commands. 

On the other hand, Kubernetes is a production-grade container orchestration tool that provides advanced features like auto-scaling, load balancing, rolling updates, and self-healing (automatic restarts). It's more complex and has a steeper learning curve, but it's capable of managing very large applications.

2. **When should I use Docker Compose and when should I use Kubernetes?**

Docker Compose is ideal for local development and testing, as well as small production deployments. If you need to quickly define and run a multi-container application on a single host, Docker Compose is a good choice.

Kubernetes, on the other hand, is designed for managing complex applications across multiple hosts in production. If you need advanced features like horizontal scaling, loadbalancing, automated rollouts and rollbacks, service discovery, and secrets management, then Kubernetes would be a more suitable choice.

3. **Can I use Docker Compose and Kubernetes together?**

Yes, you can. In fact, Docker Compose can be a useful tool in a development environment even if the application will be deployed on Kubernetes in production. Developers can use Docker Compose to define and run their services locally, then use Kubernetes for deployment in the testing, staging, and production environments.

4. **How do I convert my Docker Compose file to Kubernetes manifests?**

There are tools available that can help with this conversion. One such tool is Kompose, which is an official open-source project from Kubernetes. With Kompose, you can convert a Docker Compose file into Kubernetes deployments and services with a simple command: `kompose convert -f docker-compose.yml`

5. **What is the equivalent of Docker Compose in Kubernetes?**

Kubernetes doesn't have a built-in equivalent to Docker Compose, but there are third-party tools like Helm that provide similar functionality. Helm allows you to define, install, and upgrade even complex Kubernetes applications. It uses a packaging format called charts, which are collections of files that describe a related set of Kubernetes resources.

6. **Can Docker Compose scale applications like Kubernetes?**

Docker Compose does have a `scale` command that allows you to scale services up or down. However, this is a manual process and doesn't provide the automatic scaling features of Kubernetes. Kubernetes can monitor the load on your services and adjust the number of running instances automatically to maintain performance and availability.

7. **Does Kubernetes support Docker Compose files?**

Kubernetes doesn't natively support Docker Compose files. However, with the Kompose tool, you can convert Docker Compose files into Kubernetes objects. Also, Docker has introduced a feature called Docker Compose on Kubernetes which enables deploying Docker stacks on Kubernetes clusters, but this is still experimental and not recommended for production use.


## Question

Try spin up the service follow the README file. repo from docker related repo in https://github.com/search?o=desc&q=dockerised&s=stars&type=Repositories

## Answer
### General Steps
On the general steps to do it:

1. **Clone the repository**: Find the repository you are interested in and clone it onto your local machine using `git clone <repository url>`.

2. **Read the README file**: The README file usually contains specific instructions on how to set up and run the project. It's important to read this thoroughly.

3. **Install Docker**: Make sure Docker is installed and running on your machine. If it's not, you can download it from the Docker official website and follow the installation guide.

4. **Build the Docker image**: Most Docker-based projects contain a Dockerfile, you can use this to build a Docker image using the command `docker build -t <your-image-name> .`

5. **Run the Docker container**: After building the image, you can run the container using `docker run -d -p <host-port>:<container-port> <your-image-name>`

6. **Use Docker Compose**: If the project contains a `docker-compose.yml` file, you can use Docker Compose to manage the application. Use the command `docker-compose up` to start the services defined in the file.

Remember, the exact commands and steps can vary depending on the specifics of the repository and the application. Always refer to the README or other provided documentation for the most accurate instructions.

### A Case to practice
content management system https://github.com/django-cms/django-cms-quickstart

It may fail on 
```
git clone git@github.com:django-cms/django-cms-quickstart.git
```

#### Reason why fail with git@
Notes
The difference between `git@` and `https://` is the method used to connect to the repository. 

- `git@github.com:<username>/<repository>.git`: This is an SSH URL. SSH URLs provide access to a Git repository via SSH, a secure protocol. To use an SSH URL, you will need to have an SSH key set up on your local machine and added to your GitHub account. The error you're seeing (`Permission denied (publickey)`) suggests that you either don't have an SSH key set up on your machine, or the key hasn't been associated with your GitHub account.

- `https://github.com/<username>/<repository>.git`: This is an HTTPS URL. HTTPS URLs connect to GitHub repositories over HTTPS, a secure protocol that doesn't require any special setup. When you clone a repository with an HTTPS URL, you'll be asked for your GitHub username and password (or a personal access token if you have two-factor authentication enabled). The clone operation you did using the HTTPS URL worked because it doesn't require any additional setup.

In summary, the reason the first clone attempt failed is likely because your local machine's SSH key wasn't set up correctly with GitHub. The second attempt succeeded because HTTPS doesn't require any special setup



In order to search for repositories in Github for docker installation practice, you can follow the steps below:

1. Go to the main Github website: https://github.com/
2. Use the search bar at the top of the page. You can input the keywords relevant to what you're looking for, for example, "Docker" or "Dockerfile" or "Docker Compose".
3. Press Enter to search. On the results page, you can filter the search results by clicking on 'Repositories' tab.
4. You can further refine the search by using the options on the left - filter by language, sort by stars, etc.



Here's an example repository that you can use for your docker lesson:

`https://github.com/docker/example-voting-app`

This example voting app consists of:

- A Python webapp which lets you vote between two options
- A Redis queue which collects new votes
- A .NET worker which consumes votes and stores them in…
- A Postgres database backed by a Docker volume
- A Node.js webapp which shows the results of the voting in real time

It's even got a diagram that shows the whole system architecture, and it has a simple 'Getting started' section with easy-to-follow installation instructions. 

To get this repository, you can clone it to your local machine using the command:

```
git clone https://github.com/docker/example-voting-app.git
```
Then, navigate to the cloned repository:
```
cd example-voting-app
```
There, you will find a `README.md` file with detailed instructions on how to get the app up and running. It uses Docker Compose, so your students will learn how to use a `docker-compose.yml` file to define and run multi-container Docker applications. 

By following the instructions in the README file, you should be able to install the app in Docker. This would be a practical and interesting exercise for your students, as it involves multiple components working together in a Dockerized environment.


## FAQ
if you get error below, please make sure you login docker well.
```
 % docker compose up

[+] Running 0/2
 ⠿ db Error                                                                                                                                    2.7s
 ⠿ redis Error                                                                                                                                 2.7s
Error response from daemon: Head "https://registry-1.docker.io/v2/library/redis/manifests/alpine": unauthorized: incorrect username or password
```

# TOPIC 3.Popular Linux CLI
https://github.com/AutomationLover/Devops/blob/main/t02/PopularLinuxCommands.md


Mastering Linux commands is crucial for SRE (Site Reliability Engineering) or DevOps roles. 
Here are some examples: 

```
- sed 
    ```
    sed 's/foo/bar/g' filename # This replaces all occurrences of 'foo' with 'bar' in the file
    ```

- awk
    ```
    awk '{print $1}' filename # This prints the first field in each line of the file
    ```

- grep
    ```
    grep 'pattern' filename # This searches for the pattern in the file
    ```

- find
    ```
    find /home/user -name 'file.txt' # This searches for 'file.txt' in '/home/user' directory
    ```

- curl
    ```
    curl https://www.google.com # This fetches the HTML of Google's homepage
    ```

- ps
    ```
    ps aux # This displays the currently active processes
    ```

- netstat
    ```
    netstat -tuln # This displays network connections, listening ports, and the protocol used
    ```

- iptables
    ```
    iptables -L # This lists all the rules in the selected chain
    ```

- ssh
    ```
    ssh user@hostname # This connects to the hostas the specified user
    ```

- scp
    ```
    scp sourcefile user@hostname:destination # This copies the sourcefile to the destination directory on the remote host
    ```

- rsync
    ```
    rsync -av source_directory destination_directory # This syncs the source directory to the destination directory
    ```
```

# TOPIC 4.VSCODE plugin
https://github.com/AutomationLover/Devops/blob/main/t02/VSCode_extensions.md

## VSCode_extensions.md

For VS Code, some popular and useful extensions include:

1. Python
2. Visual Studio IntelliCode: Offers AI-assisted development features for Python, TypeScript/JavaScript and Java developers.
3. Prettier: A code formatter that supports many languages.
4. GitLens: Provides seamless Git integration.
5. Docker: Simplifies working with Docker.





# TOPIC 5.Git
https://github.com/AutomationLover/Devops/blob/main/t02/git.md




First, let's start with the basic operations of GIT:

1. **Git Clone**: This operation creates a copy of a repository.

   Example: `git clone https://github.com/username/repository.git`

2. **Git Add**: This operation adds a change in the working directory to the staging area.

   Example: `git add filename` or `git add .` to add all changes

3. **Git Commit**: This operation saves your changes to the local repository.

   Example: `git commit -m "Commit message"`

4. **Git Push**: This operation sends the committed changes of the local repository to a remote repository.

   Example: `git push origin master`

5. **Git Pull**: This operation fetches and merges changes on the remote server to your working directory.

   Example: `git pull origin master`

6. **Git Branch**: This operation allows you to create, list and delete branches.

   Examples: 
   - Create a new branch: `git branch new_branch`
   - List all branches: `git branch`
   - Delete a branch: `git branch -d branch_name`

7. **Git Checkout**: This operation switches branches or restoresworking tree files.

   Examples: 
   - Switch to an existing branch: `git checkout branch_name`
   - Create a new branch and switch to it: `git checkout -b new_branch`

8. **Git Merge**: This operation merges the specified branch’s history into the current branch.

   Example: `git merge branch_name`

Now, let's take a look at how you might handle a conflict during a merge:

1. First, let's create a new branch and make a change:

   ```
   git checkout -b feature_branch
   echo "This is some new feature." > feature.txt
   git add feature.txt
   git commit -m "Adding new feature"
   ```

2. Now switch back to master and make a conflicting change:

   ```
   git checkout master
   echo "This is a change on master." > feature.txt
   git add feature.txt
   git commit -m "Changing feature on master"
   ```

3. Try to merge feature_branch into master:

   ```
   git merge feature_branch
   ```

   You'll see a message that there's a conflict.

4. Open feature.txt and you'll see something like this:

   ```
   <<<<<<< HEAD
   This is achange on master.
   =======
   This is some new feature.
   >>>>>>> feature_branch
   ```

5. The part between `<<<<<<< HEAD` and `=======` is what's on the current branch (master), and the part between `=======` and `>>>>>>> feature_branch` is what's on the branch you're trying to merge (feature_branch).

6. To resolve the conflict, decide which part to keep, remove the other part along with the `<<<<<<< HEAD`, `=======`, and `>>>>>>> feature_branch` markers, and save the file. 

   For example, you decide to keep the change on master:

   ```
   This is a change on master.
   ```

7. After resolving the conflict, commit the changes:

   ```
   git add feature.txt
   git commit -m "Resolved merge conflict"
   ```

8. Now the merge conflict is resolved and you can push your changes to the remote repository:

   ```
   git push origin master
   ```

This is a very basic demonstration and in real projects, conflicts might be more complex. But the general process to resolve them is the same.



# TOPIC 6.Troubleshooting


## Error
```
Error response from daemon: Ports are not available: exposing port TCP 0.0.0.0:5000 -> 0.0.0.0:0: listen tcp 0.0.0.0:5000: bind: address already in use
```

## TS Steps

This error is occurring because the port 5000 on your machine is already in use by another application. Here are a few ways to solve it:

1. **Stop the application using the port**: If you know which application is using the port and it's not necessary for it to be running, you can stop it to free the port.

2. **Change the port in the Docker Compose file**: If you can't stop the application using the port, or you don't know what it is, you can change the port that Docker is trying to use in your `docker-compose.yml` file.

Here's how to do it: You will find a line in your `docker-compose.yml` file that looks like this:

```yaml
ports:
  - "5000:5000"
```

This line is telling Docker to map port 5000 inside the Docker container to port 5000 on your host machine. You can change the host port to something else, like 5001:

```yaml
ports:
  - "5001:5000"
```

This way, your application inside Docker will still be running on port 5000, but it will be accessible on port 5001 on your host machine.

Don't forget to save the changes. Now, re-run the `docker compose up` command. Your application should now be accessible at `localhost:5001`.

If you still face issues, you might want to check which application is using port 5000. You can do this with the following command in your terminal:

For Linux/Mac:
```bash
sudo lsof -i :5000
```

For Windows:
```bash
netstat -ano | findstr :5000
```

This will give you the PID (process ID) of the application that's using the port. If the application is not needed, you can kill it using `kill <PID>` on Linux/Mac or `taskkill /PID <PID>` on Windows. Then try running `docker compose up` again.
