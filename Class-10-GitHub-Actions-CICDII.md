# Lesson 10 GitHub Actions CI/CD II
# Lecturer: Ray Ma

ref link:<https://github.com/JiangRenDevOps/DevOpsNotes/tree/master/WK4_Jenkins_and_GitHub_Actions/Wk4.3_GitHub_Actions>

# GitHub Actions

GitHub Actions is a continuous integration and continuous delivery (CI/CD) platform that allows you to automate your build, test, and deployment pipeline. You can create workflows that build and test every pull request to your repository, or deploy merged pull requests to production.

## TLDR;

Please visit [GitHub Actions](https://github.com/features/actions) to understand more.

![Alt Text](images/actions-workflow-2.png?raw=true)

## Run a workflow on any GitHub event

Kick off workflows with GitHub events like push, issue creation, or a new release. Combine and configure actions for the services you use, built and maintained by the community.

Whether you want to build a container, deploy a web service, or automate welcoming new users to your open source projects—there's an action for that. Pair GitHub Packages with Actions to simplify package management, including version updates, fast distribution with our global CDN, and dependency resolution, using your existing GITHUB_TOKEN.

![Alt text](images/actions-workflow.svg?raw=true)

## Features

### Environment

**Linux, macOS, Windows, ARM, and containers:** Hosted runners for every major OS make it easy to build and test all your projects. Run directly on a VM or inside a container. Use your own VMs, in the cloud or on-prem, with self-hosted runners.

**Matrix builds:** Save time with matrix workflows that simultaneously test across multiple operating systems and versions of your runtime.

**Any language:** GitHub Actions supports Node.js, Python, Java, Ruby, PHP, Go, Rust, .NET, and more. Build, test, and deploy applications in your language of choice.

**Live logs:** See your workflow run in realtime with color and emoji. It’s one click to copy a link that highlights a specific line number to share a CI/CD failure.

**Built in secret store:** Automate your software development practices with workflow files embracing the Git flow by codifying it in your repository.

**Multi-container testing:** Test your web service and its DB in your workflow by simply adding some docker-compose to your workflow file.

**Community-powered workflows:** GitHub Actions connects all of your tools to automate every step of your development workflow. Easily deploy to any cloud, create tickets in Jira, or publish a package to npm.

Want to venture off the beaten path? Use the millions of open source libraries available on GitHub to create your own actions. Write them in JavaScript or create a container action—both can interact with the full GitHub API and any other public API.

## Pricing

Public repositories: **FREE**

![Alt Text](images/actions-pricing.png?raw=true)


# GitHub Actions Basic

Quickstart doc: https://docs.github.com/en/actions/quickstart

You only need a GitHub repository to create and run a GitHub Actions workflow. In this guide, you'll add a workflow that demonstrates some of the essential features of GitHub Actions.

The following example shows you how GitHub Actions jobs can be automatically triggered, where they run, and how they can interact with the code in your repository.

## Creating your first workflow

1. Create a `.github/workflows` directory in your repository on GitHub if this directory does not already exist.

2. In the `.github/workflows` directory, create a file named `github-actions-demo.yml`. For more information, see "Creating new files."

3. Copy the following YAML contents into the `github-actions-demo.yml` file:

    ```yaml
    name: GitHub Actions Demo
    run-name: ${{ github.actor }} is testing out GitHub Actions 🚀
    on: [push]
    jobs:
      Explore-GitHub-Actions:
        runs-on: ubuntu-latest
        steps:
          - run: echo "🎉 The job was automatically triggered by a ${{ github.event_name }} event."
          - run: echo "🐧 This job is now running on a ${{ runner.os }} server hosted by GitHub!"
          - run: echo "🔎 The name of your branch is ${{ github.ref }} and your repository is ${{ github.repository }}."
          - name: Check out repository code
            uses: actions/checkout@v3
          - run: echo "💡 The ${{ github.repository }} repository has been cloned to the runner."
          - run: echo "🖥️ The workflow is now ready to test your code on the runner."
          - name: List files in the repository
            run: |
              ls ${{ github.workspace }}
          - run: echo "🍏 This job's status is ${{ job.status }}."
    ```

4. Scroll to the bottom of the page and select **Create a new branch for this commit and start a pull request**. Then, to create a pull request, click **Propose new file**.

    ![Alt Text](https://docs.github.com/assets/cb-67235/images/help/repository/actions-quickstart-commit-new-file.png)

    Committing the workflow file to a branch in your repository triggers the push event and runs your workflow.

## Understanding the workflow file

https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions#understanding-the-workflow-file

## Encrypted secrets

https://docs.github.com/en/actions/security-guides/encrypted-secrets

## Replace environment variables

https://github.com/danielr1996/envsubst-action

```yml
      - name: Replace Environment Variables
        uses: danielr1996/envsubst-action@1.0.0
        env:
          PORT: ${{ secrets.PORT }}
          DB_HOST: ${{ secrets.DB_HOST }}
          DB_USERNAME: ${{ secrets.DB_USERNAME }}
          DB_PORT: ${{ secrets.DB_PORT }}
          DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
          DB_NAME: ${{ secrets.DB_NAME }}
        with:
          input: deployment.yml
          output: deploy.yml
```

## Manually triggered workflows

https://docs.github.com/en/actions/using-workflows/triggering-a-workflow#defining-inputs-for-manually-triggered-workflows

```yml
on:
  workflow_dispatch:
```


# Basic Knowledge

## Kubernetes

Source: [Google](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)

### What is Kubernetes?

Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation. It has a large, rapidly growing ecosystem. Kubernetes services, support, and tools are widely available.

The name Kubernetes originates from Greek, meaning helmsman or pilot. K8s as an abbreviation results from counting the eight letters between the "K" and the "s". Google open-sourced the Kubernetes project in 2014. Kubernetes combines over 15 years of Google's experience running production workloads at scale with best-of-breed ideas and practices from the community.

### Kubernetes Components

When you deploy Kubernetes, you get a **cluster**.

A Kubernetes cluster consists of a set of worker machines, called nodes, that run containerized applications. Every cluster has at least one worker node.

The worker node(s) host the Pods that are the components of the application workload. The control plane manages the worker nodes and the Pods in the cluster. In production environments, the control plane usually runs across multiple computers and a cluster usually runs multiple nodes, providing fault-tolerance and high availability.

This document outlines the various components you need to have for a complete and working Kubernetes cluster.

![Alt text](images/components-of-kubernetes.svg?raw=true)

### Workload - Pods

*Pods* are the smallest deployable units of computing that you can create and manage in Kubernetes.

A Pod (as in a pod of whales or pea pod) is a group of one or more containers, with shared storage and network resources, and a specification for how to run the containers. A Pod's contents are always co-located and co-scheduled, and run in a shared context. A Pod models an application-specific "logical host": it contains one or more application containers which are relatively tightly coupled. In non-cloud contexts, applications executed on the same physical or virtual machine are analogous to cloud applications executed on the same logical host.

As well as application containers, a Pod can contain init containers that run during Pod startup. You can also inject ephemeral containers for debugging if your cluster offers this.

#### What is a Pod?

> **Note:** While Kubernetes supports more container runtimes than just Docker, Docker is the most commonly known runtime, and it helps to describe Pods using some terminology from Docker.

The shared context of a Pod is a set of Linux namespaces, cgroups, and potentially other facets of isolation - the same things that isolate a Docker container. Within a Pod's context, the individual applications may have further sub-isolations applied.

![Alt text](images/k8s-namespace.jpeg?raw=true)

In terms of Docker concepts, a Pod is similar to a group of Docker containers with shared namespaces and shared filesystem volumes.

**Using Pods**

The following is an example of a Pod which consists of a container running the image ``nginx:1.14.2``.

```yaml
pods/simple-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

To create the Pod shown above, run the following command:

```sh
kubectl apply -f https://k8s.io/examples/pods/simple-pod.yaml
```

Pods are generally not created directly and are created using workload resources. See Working with Pods for more information on how Pods are used with workload resources.

### Services

An abstract way to expose an application running on a set of Pods as a network service.

With Kubernetes you don't need to modify your application to use an unfamiliar service discovery mechanism. Kubernetes gives Pods their own IP addresses and a single DNS name for a set of Pods, and can load-balance across them.

![Alt text](images/k8s-pod-service.jpg?raw=true)

#### Defining a Service

A Service in Kubernetes is a REST object, similar to a Pod. Like all of the REST objects, you can POST a Service definition to the API server to create a new instance. The name of a Service object must be a valid RFC 1035 label name.

For example, suppose you have a set of Pods where each listens on TCP port `9376` and contains a label ``app=MyApp``:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```

This specification creates a new Service object named "my-service", which targets TCP port 9376 on any Pod with the app=MyApp label.

Kubernetes assigns this Service an IP address (sometimes called the "cluster IP"), which is used by the Service proxies (see Virtual IPs and service proxies below).

The controller for the Service selector continuously scans for Pods that match its selector, and then POSTs any updates to an Endpoint object also named "my-service".

> **Note:** A Service can map any incoming port to a targetPort. By default and for convenience, the targetPort is set to the same value as the port field.

Port definitions in Pods have names, and you can reference these names in the targetPort attribute of a Service. This works even if there is a mixture of Pods in the Service using a single configured name, with the same network protocol available via different port numbers. This offers a lot of flexibility for deploying and evolving your Services. For example, you can change the port numbers that Pods expose in the next version of your backend software, without breaking clients.

The default protocol for Services is TCP; you can also use any other supported protocol.

As many Services need to expose more than one port, Kubernetes supports multiple port definitions on a Service object. Each port definition can have the same protocol, or a different one.

#### Namespaces of Services

A DNS query may return different results based on the namespace of the pod making it. DNS queries that don't specify a namespace are limited to the pod's namespace. Access services in other namespaces by specifying it in the DNS query.

For example, consider a pod in a ``test`` namespace. A ``data`` service is in the ``prod`` namespace.

A query for ``data`` returns no results, because it uses the pod's test namespace.

A query for ``data.prod`` returns the intended result, because it specifies the namespace.

DNS queries may be expanded using the pod's /etc/resolv.conf. Kubelet sets this file for each pod. For example, a query for just data may be expanded to data.test.svc.cluster.local. The values of the search option are used to expand queries. To learn more about DNS queries, see the resolv.conf manual page.

```sh
nameserver 10.32.0.10
search <namespace>.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```

In summary, a pod in the test namespace can successfully resolve either ``data.prod`` or ``data.prod.svc.cluster.local``.

#### DNS Records

What objects get DNS records?

* Services
* Pods

![Alt text](images/k8s-ingress.jpg?raw=true)

## Azure Kubernetes Service

Source: [Azure](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes)

Azure Kubernetes Service (AKS) simplifies deploying a managed Kubernetes cluster in Azure by offloading the operational overhead to Azure. As a hosted Kubernetes service, Azure handles critical tasks, like health monitoring and maintenance. Since Kubernetes masters are managed by Azure, you only manage and maintain the agent nodes. Thus, AKS is free; you only pay for the agent nodes within your clusters, not for the masters.

![Alt text](images/aks.png?raw=true)

## Azure Cosmos DB

Source: [Azure](https://docs.microsoft.com/en-us/azure/cosmos-db/introduction)

Azure Cosmos DB is a fully managed NoSQL database for modern app development. Single-digit millisecond response times, and automatic and instant scalability, guarantee speed at any scale. Business continuity is assured with SLA-backed availability and enterprise-grade security. App development is faster and more productive thanks to turnkey multi region data distribution anywhere in the world, open source APIs and SDKs for popular languages. As a fully managed service, Azure Cosmos DB takes database administration off your hands with automatic management, updates and patching. It also handles capacity management with cost-effective serverless and automatic scaling options that respond to application needs to match capacity with demand.

![Alt text](images/azure-cosmos-db.png?raw=true)


# A Simple App - Hello World

In this session, we are going to use GitHub Actions to implement a CI/CD pipeline to build an App and deploy it on Azure Kubernetes cluster.

## Source Code

[Simple App - GitHub Repo](https://github.com/RayMaAU/github-actions-workflow-simple-app)

## Architecture

![Architecture diagram](images/architecture-github-actions.png?raw=true)

## App Running Status

![Architecture diagram](images/app-running.png?raw=true)

## Prerequisite

- Microsoft Azure
- GitHub
- Docker
- Visual Studio Code

## Cloud Services

- Azure Container Registry
- Azure Kubernetes Service
- Azure Cosmos DB
- Azure Monitor
- Grafana (VM based)

## Language

- NodeJS

## Dependency

- Mongo DB Driver

## Disucssion

1. What's the first thing to do?
2. What do you need?
3. How to test?
4. How to troubleshoot?
5. What else?

# Container CI/CD using GitHub Actions and Kubernetes on Azure Container Service (AKS)

## Architecture overview

Containers make it very easy for you to continuously build and deploy your applications. By orchestrating deployment of those containers using Kubernetes in Azure Container Service, you can achieve replicable, manageable clusters of containers.

By setting up a continuous build to produce your container images and orchestration, you can increase the speed and reliability of your deployment.

![Architecture diagram](images/architecture-github-actions.png?raw=true)

1. Change application source code.
2. Commit code to GitHub.
3. Continuous Integration Trigger to GitHub Actions.
4. GitHub Actions workflow triggers a build job using Azure Container Service (AKS) for a dynamic build agent.
5. GitHub Actions builds and pushes Docker container Azure Container Registry.
6. GitHub Actions deploys new containerized app to Kubernetes on Azure Container Service (AKS) backed by Azure Cosmos DB.
7. Grafana displays visualization of infrastructure and application metrics via Azure Monitor.
8. Monitor application and make improvements.

## Create Azure Environment

1. Create ACR - Azure Container Registry

   > Note:
   >
   > `ACR` (Azure Container Registry) names may contain alpha numeric characters only. A valid name is `acr1801301512`.

2. Create AKS - Azure Kubernetes Cluster

   > Note:
   >
   > `Dns prefix` must be unique, consider using `<service-name>-<appid>`, such as `GitHub Actions-902d2aea-74dd-4f80-a0a6-7f6a976bb9b7` or at least part of it.

3. Create a Cosmos DB with Mongo DB API

   > Note:
   >
   > The names and DNS prefixes should be unique. To avoid naming conflicting, we strongly recommend you to add some suffix. For example, the `Cosmos Db name` could be `cosmos-180130-1512`.

## GitHub Actions Workflow

### Create an Azure AD OIDC for GitHub Actions

1. On Azure AD - App Registration, create a new app for GitHub Actions

   ![Alt Text](images/aad-github-oicd.png)

2. Once the new app registered, go to **Certificates & secrets** and create a new credential:

   ![Alt Text](images/aad-github-oicd-2.png)

3. Grant permissions to your GitHub org and repo:

   ![Alt Text](images/aad-github-oicd-3.png)

4. Go to the Overview page of your Azure AD App, you will use the Application (client) ID to update your workflow:

   ![Alt Text](images/aad-app-info.png)

5. Add a Contributor role to your Azure Subscription:

   ![Alt Text](images/az-sub-rbac.png)

6. Add Mongo DB connection string as a secret:

   ![Alt Text](images/github-actions-add-secrets.png)

7. Copy the primary connection string from your Cosmos DB to GitHub Actions secret:

   ![Alt Text](images/az-cosmos-db-conn-str.png)

   ![Alt Text](images/github-actions-new-secret.png)

## Access the hello world web app

### Sign into Azure and get AKS credentials

1. Open terminal, execute:

   ```sh
   az login
   ```

   Follow the guide to sign in.

2. Execute the command below to get Kubenetes cluster credentials.

   ```sh
   aks get-credentials --resource-group <ResourceGroup> --name <KubenetesClusterName>
   ```

   > Note: please replace \<ResourceGroup> and \<KubenetesClusterName> before executing it.

   When done, you will get a prompt:

   ```Sh
   Merged "kube-180131-1520" as current context in /Users/<User>/.kube/config
   ```

### Get Kubenetes service

1. Install [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) if you have not. Or, you can install `kubectl` locally using the [az aks install-cli](https://docs.microsoft.com/en-us/cli/azure/aks#az-aks-install-cli) command:

   ```sh
   az aks install-cli
   ```

2. Execute the command below:

   ```Sh
   kubectl get service
   ```

3. You will get the response like below:

   ```Sh
   NAME                  TYPE           CLUSTER-IP   EXTERNAL-IP      PORT(S)        AGE
   hello-world-service   LoadBalancer   10.0.98.99   52.168.126.156   80:32611/TCP   1h
   kubernetes            ClusterIP      10.0.0.1     <none>           443/TCP        1h
   ```

4. Copy the **external ip** of the **hello-world-service**.

### Access from browser

1. Open the **external ip** in a browser. You will see the response:

   ```html
   Hello World!
   There are 0 request records.
   ```

2. Refresh the page, the number of request records will increase.

## Create Grafana Monitor

1. Install [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) if you have not.

   > Note: Alternatively, you can also use Azure Cloud Shell from the Azure Portal.

2. Open terminal, then execute:

   ```sh
   az login
   ```

   Follow the guide to sign in.

   > Note: If you use Azure Cloud Shell from the Azure Portal, you are automatically authenticated. Therefore there is no need to ``az login`` again.

3. Execute the command below to create service principal, with the role ``Contributor`` and under the current subscription by default.

   ```sh
   az ad sp create-for-rbac --name <AppName> --role Contributor --scopes /subscriptions/{subscriptionId}
   ```

   > Note: please replate the \<AppName> placeholder.

4. You will get a response like below.

   ```json
   {
      "appId": "8e897eb4-069d-40c2-9563-000000003c14",
      "displayName": "<AppName>",
      "password": "xxxxxvJgeKZoRAfxxxx0SitnANH8Kxxxx",
      "tenant": "000088bf-0000-0000-0000-2d7cd0100000"
   }
   ```

   Copy the values **appId** and **password**, they will be used later.

   > Note: for more details about creating an Azure service principal, please refer to [Create an Azure service principal with Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)

5. Create an Azure VM with Ubuntu OS

6. Follow this doc to install Grafana on your Azure VM: https://grafana.com/docs/grafana/latest/setup-grafana/installation/debian/

### Access the Grafana instance

1. Copy the GRAFANAURL value from the Outputs section of the deployment.

2. Open it in a browser, then log in:

   * User: admin
   * Password: *use the Linux Admin Password*

3. Click **Home**.

   ![Alt text](images/grafana-01.png)

   Then click **Hello World Overview**:

   ![Alt text](images/grafana-02.png)

4. You will see the graphs:

   ![Alt text](images/grafana-03.png)

5. If you want to SSH to Grafana host machine, you can open terminal, then paste and execute a command like below.

   ```Sh
   ssh -i <private_ssh_key>  <username>@<GRAFANAURL>
   ```

## References

* [Configure a federated identity credential on an app - GitHub Actions](https://learn.microsoft.com/en-us/azure/active-directory/develop/workload-identity-federation-create-trust?pivots=identity-wif-apps-methods-azp#github-actions)

* [GitHub Actions Billing](https://docs.github.com/en/billing)

* [GitHub Actions - Azure Login](https://github.com/marketplace/actions/azure-login#configure-a-federated-credential-to-use-oidc-based-authentication)

* [GitHub Actions - Assigning permissions to jobs](https://docs.github.com/en/actions/using-jobs/assigning-permissions-to-jobs)

* [GitHub Actions - Azure Container Registry Login](https://github.com/marketplace/actions/azure-container-registry-login)

* [GitHub Action - Azure Container Registry Build](https://github.com/marketplace/actions/azure-container-registry-build)

* [GitHub Actions for deploying to Kubernetes service](https://learn.microsoft.com/en-us/azure/aks/kubernetes-action)

* [GitHub Action - envsubst-action](https://github.com/marketplace/actions/envsubst-action)

# Monitoring with Grafana

Source: https://grafana.com/docs/grafana/latest/

## What is Grafana?

Grafana website: https://grafana.com/grafana/

Grafana is an open-source data visualization and monitoring platform. It allows users to query, visualize, and alert on metrics and logs from various data sources such as Prometheus, InfluxDB, Elasticsearch, and many others. Grafana provides a web-based interface for creating and sharing dashboards, which can be used to display real-time data, such as server and application performance metrics, and to alert on certain conditions. It is commonly used for DevOps and IoT applications, and its plugins and integrations allow it to fit into a variety of workflows.

![Alt text](images/grafana.png?raw=true)

## Observability

**Observability** is a term used in the context of monitoring systems to describe the degree to which a system's internal state can be inferred from its external outputs. In other words, it refers to the extent to which the behavior and performance of a system can be observed and analyzed. In the context of IT infrastructure and applications, observability includes the ability to collect and analyze data from various sources, such as logs, metrics, and traces, to gain insight into the state of the system. This data can then be used to identify performance issues, isolate root causes of failures, and make data-driven decisions to improve the overall health and stability of the system.

Examples of metrics and measures in the context of monitoring systems include:

- Resource utilization: CPU utilization, memory usage, disk space utilization, network bandwidth usage, etc.

- Application performance: Request latency, error rates, number of requests per second, response time, etc.

- Infrastructure performance: Server uptime, network latency, disk I/O operations per second, etc.

- Business metrics: Revenue, customer satisfaction, conversion rate, etc.

- Log data: System logs, application logs, security logs, etc.

- Tracing data: Request tracing, error tracing, distributed tracing, etc.

These metrics and measures can be collected and analyzed in real-time to monitor the performance and behavior of a system, detect issues and anomalies, and make data-driven decisions for improvement.

## Install Grafana on Ubuntu

To install the latest release:

```bash
sudo apt-get install -y apt-transport-https
sudo apt-get install -y software-properties-common wget
sudo wget -q -O /usr/share/keyrings/grafana.key https://apt.grafana.com/gpg.key
```

Add this repository for stable releases:

```bash
echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```

After you add the repository:

```bash
sudo apt-get update

# Install the latest OSS release:
sudo apt-get install grafana
```

## Sign in to Grafana

Steps

1. To sign in to Grafana for the first time, follow these steps:

2. Open your web browser and go to <http://grafana-ip:3000/>. Unless you have configured a different port, Grafana listens to port 3000 by default.

3. On the signin page, enter admin for username and password.

4. Click Sign in. If successful, you will see a prompt to change the password.

5. Click OK on the prompt and change your password.

![Alt Text](images/grafana-login.png?raw=true)

## Connect to Azure Monitor

Reference Source: https://learn.microsoft.com/en-us/azure/azure-monitor/visualize/grafana-plugin

1. Create a Service Principle for Grafana instance from Azure Cloud CLI

    ```PowerShell
    az ad sp create-for-rbac --name <sp-name> --role "Monitoring Reader" --scopes /subscriptions/<subscription-id>   
    ```

    You will get a response like below.

    ```json
    {
        "appId": "8e897eb4-069d-40c2-9563-000000003c14",
        "displayName": "<AppName>",
        "password": "xxxxxvJgeKZoRAfxxxx0SitnANH8Kxxxx",
        "tenant": "000088bf-0000-0000-0000-2d7cd0100000"
    }
    ```

    Copy all values, they will be used later.

    > Note: for more details about creating an Azure service principal, please refer to [Create an Azure service principal with Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

2. Add Azure Monitor plugin

    ![Alt Text](images/grafana-add-azure-monitor-plugin.png?raw=true)

3. Provide the Service Principle to your Grafana Azure Monitor data source 

    ![Alt Text](images/grafana-add-azure-monitor-data-source.png?raw=true)

## Creat your own Dashboard

![Alt Text](images/grafana-sample-dashboard.png?raw=true)

## Import from Grafana Dashboard Gallery

Import this Dashboard to your Grafana: <https://grafana.com/grafana/dashboards/14891-aks-monitor-container-insights-kubernetes-pod-metrics/>

![Alt Text](images/grafana-import-dashboard.png?raw=true)

## Azure Managed Grafana

Azure Managed Grafana is a data visualization platform built on top of the Grafana software by Grafana Labs. It's built as a fully managed Azure service operated and supported by Microsoft.

Source: <https://learn.microsoft.com/en-gb/azure/managed-grafana/>

### Why use Azure Managed Grafana?

Managed Grafana lets you bring together all your telemetry data into one place. It can access a wide variety of data sources supported, including your data stores in Azure and elsewhere. By combining charts, logs and alerts into one view, you can get a holistic view of your application and infrastructure, and correlate information across multiple datasets.

As a fully managed service, Azure Managed Grafana lets you deploy Grafana without having to deal with setup. The service provides high availability, SLA guarantees and automatic software updates.

You can share Grafana dashboards with people inside and outside of your organization and allow others to join in for monitoring or troubleshooting.

Managed Grafana uses Azure Active Directory (Azure AD)’s centralized identity management, which allows you to control which users can use a Grafana instance, and you can use managed identities to access Azure data stores, such as Azure Monitor.

You can create dashboards instantaneously by importing existing charts directly from the Azure portal or by using prebuilt dashboards.
