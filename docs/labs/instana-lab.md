# AIOps with IBM Z and LinuxONE

In this tutorial, you will become familiar with three of IBM's strategic AIOps solutions - Instana, Turbonomic, and IBM Cloud Pak for AIOps - and the capabilities they have to monitor and manage IBM Z applications and infrastructure.

<!-- In this tutorial, you will become familiar with two of IBM's strategic AIOps solutions - Instana and IBM Cloud Pak for AIOps - and the capabilities they have to monitor and manage IBM Z applications and infrastructure. -->

## Environment Overview

![aiops-arch](aiops-arch-AIOps-workshop.drawio.png)

## Connecting to the Lab Environment

Connection instructions with platform URLs and credentials are listed on the [Environment Access Page](../access.md){target=wsc_envaccess}.

## Exploring the Robot Shop Sample Application

!!! Note 

    Google Chrome has been set as the default browser in your lab environment.  Please use Google Chrome for the labs.


1. **Open the Red Hat OpenShift Container Platform web console** at [https://console-openshift-console.apps.atsocpd1.dmz](https://console-openshift-console.apps.atsocpd1.dmz){target=wsc_ocpconsole}

    For your convenience, the above link is repeated on the [Environment Access Page](../access.md){target=wsc_envaccess}, in the *Links to platforms* section.

    !!! Note "A note on abbreviations"

        For the sake of brevity, we may refer to Red Hat OpenShift Container Platform as either *OpenShift* or *OCP* throughout these labs.

    !!! Note "Click through any warnings"

        Our lab environments often use self-signed certificates, so click through any browser warnings that might arise as a result.  

    You should now see the OpenShift console login page.

    ![openshift-console-login](openshift-console-login.png)

2. **Log in to OpenShift with your credentials.** Your userid will be *usernn* where *nn* is the unique number assigned to you, and your password will be in the table in the [Environment Access Page](../access.md/#openshift-instana-turbonomic-and-cp4aiops-credentials){target=wsc_envaccess}.

3. **Under the Developer perspective (1), navigate to the Topology page (2) for the `robot-shop` project (3).**  

    !!! Note
  
        The icons on your lab system will differ in appearance from the below screen snippet, but this shouldn't affect the lab functionality.

    ![robot-shop-topology](robot-shop-topology.png)

    Robot Shop is a simulated online store where you can purchase robots and AI solutions. Robot Shop is made up of many different microservices that are written in different programming languages. There are a dozen or so different microservices written using languages and frameworks such as NodeJS, Python, Spring Boot, and Go, along with containerized databases including MongoDB, MySQL, and Redis. Each icon in the Topology represents an OpenShift Deployment for a specific microservice. Each microservice is responsible for a single function of the Robot Shop application that you will see in the following steps.

4. Within your Topology view, **Open the Robot Shop web application by clicking the small button in the top right of the `web` icon.**

    ![web-route](web-route.png)

    This is simply a hyperlink that will take you to the Robot Shop application homepage. Those microservices that are intended to be accessible externally- that is, from outside the OCP cluster-  have *routes* defined to allow these external access.  An external URL is associated with these routes, and the hyperlink you clicked invoked this URL. You may notice within your Topology view that only the *web* microservice has a route associated with it.  That is because all of the other microservices are called from other microservices within the OCP cluster- only the web page is externally available.  

    The Robot Shop homepage should appear like the screenshot below, with all of the same options in the left side menu.

    ![robot-shop-1](robot-shop-1.png)

6. **Explore the website and its functionality from the left side menu.**
   
    You can register a new user, explore the catalog of purchasable robots, give them ratings, and simulate a purchase.

    This is a sample application for demonstration purposes- don't worry if it has a few quirks that wouldn't be acceptable for a true production application.

    Although you've seen the Topology view of this application, notice that it doesn't show how all of the various microservice applications are plumbed together, or how they are performing.  As a user of the web page, you don't have this visibility either.  You don't have visibility into how these microservices may be performing, if they have the correct amount of resources, or if any issues are currently affecting the application.  In other words, there is a lack of *observability*, *application performance management*, and *proactive problem remediation*.  This lack of visibility is what the *Instana*, *Turbonomic* and *Cloud Pak for AIOps* aims to overcome, and the purpose of these labs is to give you a sample of how these products can help you.

## Instana

### Overview of Instana Observability

Instana is an enterprise observability solution that offers application performance management - no matter where the application or infrastructure resides. Instana can monitor both containerized and traditional applications, various infrastructure types including OpenShift, public clouds & other containerization platforms, native Linux, z/OS, websites, databases, and more. The current list of supported technologies can be found [in the Instana documentation](https://www.ibm.com/docs/en/instana-observability/current?topic=configuring-monitoring-supported-technologies){target=wsc_instanadoc}.

### Viewing the Instana Agent on OpenShift

1. In the OpenShift cluster, **navigate to the `instana-agent` project (1) then click the circular icon on the Topology page that is labeled `instana-agent` (2).** 

    ![instana-agent-daemonset](instana-agent-daemonset.png)

    This is the Instana agent that is collecting all the information about the containerized applications running on OpenShift and sending that information to the Instana server.
    
    The agent is deployed as a [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/){target=wsc_kubedocs}, which is a Kubernetes object that ensures one copy of the pod runs on each compute node in the cluster. Each individual Instana agent pod is responsible for the data and metrics collection for the application running on one of the compute nodes.

2. **Click the circular icon on the Topology page (1) that is labeled `instana-agent-k8sensor`.**  A sidebar will appear (2) with more information. 

    !!! Tip 

        Underneath the circular icon it is probable that the full name `instana-agent-k8sensor` is not shown in the UI unless you hover your cursor over the name.

    ![k8sensor-deployment](k8sensor-deployment.png)

    The `instana-agent-k8sensor` pods are responsible for gathering information about the OpenShift cluster itself and all of the Kubernetes objects it includes - pods, namespaces, routes, etc., and sending that information to the Instana server. 

3.  **Click the `view logs` hyperlink next to one of the `instana-agent-k8sensor` pods in the right-side menu.**

    You should be able to find messages similar to what is shown below:

    ```text
    2025/03/08 15:35:28 main.go:366: pod=instana-agent/instana-agent-k8sensor-5d9f9d984-mj2d4 shards=[02 05 08 0B 0E 11 14 17]
    2025/03/08 15:35:28 main.go:366: call=senseLoop PodsCount={344} PodsRunning={344} PodsPending={0} snitch=pod sense.min=0 sense.99.9PCTL=0 sense.max=0 apply.min=37 apply.99.9PCTL=384 apply.max=1278 http.do.min=0 http.do.99.9PCTL=62 http.do.max=101 encode.pmin=2.50KB encode.p99.9PCTL=282.58KB encode.pmax=395.03KB encode.tmin=0 encode.t99.9PCTL=40 encode.tmax=94 total.min=41 total.99.9PCTL=413 total.max=1427 send.calls=72036 send.errors=0 send.exceptions=0
    ```

    If you would like to see what data the agents are collecting or if you need to debug issues with collecting data from certain workloads running on OpenShift, these pod logs are a good first place to look.

    In the next section, you will take a look at the other side of the OpenShift Instana agent - the Instana server that is receiving the data.

### Navigating the Instana Platform

1. **Open the web console of your Instana server at [https://unit0-wsc.lcsins01.dmz](https://unit0-wsc.lcsins01.dmz){target=wsc_instanaui}.** 

2. **Log in to Instana with your credentials.**

    When you first log in to Instana, you will be taken to the Home Page. This is a customizable summary page that shows the key metrics for any component of your environment in the timeframe specified in the top right of the screen.

    !!! Note

        You won't see the "Time range" popup in the upper right of the below screen snippet when you first log in, but you will see it when performing the next step.

    ![instana-timeframe](instana-timeframe.png)

3. **Use the button in the top right to set the time frame to 'Last Hour' and click the Live button.** 

    Those two buttons should now appear as shown below, with the current date. When the the 'Live' button shows a square, or "stop" button, this indicates that you are seeing live data.  When the 'Live' button shows a triangle, or "play" button, this indicates that the UI will not update with live data.

    ![instana-livebutton](instana-livebutton.png)

    You are now seeing metrics for the environment over the previous hour, and it is updating in real time with up to 1 second granularity. Instana captures 100% of application calls and traces with no sampling, ensuring you never miss any critical data or insights into your application's performance.

    The next thing to notice is the menu on the left side of the page. If you hover over the left-side panel, you will see a menu of links that will let you dive into different sections of the Instana dashboard, rather than seeing every option.

4. **Hover (click if necessary) over the left-side panel so the menu appears.**

    ![instana-menu](instana-menu.png)

    Next, you will go through each section in the menu.

5. **Click the _Websites & Mobile Apps_ option in the left-side menu.**

    You can see that Instana is monitoring a website named `Robot Shop Website`. This is the set of webpages associated with the Robot Shop sample application that is deployed on OpenShift on IBM Z. Instana supports [website monitoring](https://www.ibm.com/docs/en/instana-observability/current?topic=instana-monitoring-websites){target=wsc_instanadoc} by analyzing browser request times and route loading times. It allows detailed insights into the web browsing experience of users, and deep visibility into application call paths. The Instana website monitoring solution works by using a lightweight JavaScript agent, which is embedded into the monitored website.

    Your screen should look similar to this screen snippet:

    ![instana-website](instana-website.png)

6. **Click the _Robot Shop Website_ hyperlink under the _Name_ column.** This will bring you to a screen similar to what is shown below:

    ![instana-website-metrics](instana-website-metrics.png)

    With website monitoring, there are numerous filters and tabs with more information.

7. **You can navigate through the various tabs to show more data - _Speed_, _Resources_, _HTTP requests_, and _Pages_.**

    Spend a moment looking at what is shown in these tabs.
    
    In the next section, you'll explore the application metrics for the Robot Shop resources running on OpenShift.

8. **Click the Applications option in the left-side menu.**

    This Instana instance has many applications configured. We will be exploring a few of them during this lab, primarily the *robot-shop* application.

    The _robot-shop_ application should be easy to find under the *Name* column, but if not you can scroll through the table to find it or you can click on the 'magnifying glass' icon on the right side of the search bar at the top of the application list and enter a search string.

9. **Click the robot-shop application hyperlink under the _Name_ column.**

    An application perspective represents a set of services and endpoints that are defined by a shared context and is declared using tags. For example, the robot-shop application perspective encompasses all services and endpoints that meet the tag `kubernetes-namespace=robot-shop`.

    !!! Tip

        If you click on the _Configuration_ tab, you can see that this application is definited simply as those resources that reside in the _robot-shop_ Kubernetes namespace. Return to the _Summary_ tab after viewing this configuration information.

    Alongside the Robot Shop application running in OpenShift, there is a container running a Python application that generates load to each microservice. The metrics you see now in the application perspective are coming from that load generator. At the top of the page, you can see the total number of calls, the number of erroneous calls, and the mean latency for each call over the past hour.

    As with the _Websites & Mobile Apps_ section, the _Application_ perspective has various tabs that contain different information. When it comes to microservices, one of the most helpful tabs in the _Application_ perspective is the _Dependency_ graph.

10. **Click the Dependencies tab.**

    ![instana-dependencies](instana-dependencies.png)

    You will be shown the dependency graph, which offers a visualization of each service in the _Application_ perspective, which services interact with each other, and visual representations of errors, high latency, or erroneous calls.

    ![robot-shop-dependencies](robot-shop-dependencies.png)

    There are more tabs in the _Application_ perspective related to each individual service, error and log messages, the infrastructure stack related to the tag, and options to configure the _Application_ perspective.

11. **Click through each of the tabs.**

    If you pay attention while clicking through these tabs, you may notice that the *payments* service has an unusually high number of erroneous calls, and you could dig into the specific calls to start debugging these errors. You'll do that soon in later steps of the lab.

    Next, you'll take a look at Instana's Kubernetes monitoring capabilities.

12. **Click the Platforms -> Kubernetes option in the left side menu.**

    You should see a screen similar to this:

    ![instana-kubernetes](instana-kubernetes.png)

    !!! Info

        *atsocpd1* is an OCP cluster running on an IBM z16 in the Washington Systems Center data center in Herndon, Virginia, USA.  The _Robot Shop_ application that you are working with in this lab runs on this cluster.

        *atsocpd3* is another OCP cluster running on the same IBM z16 as *atsocpd1*, but is not used in this lab.

        *instana* is the Instana server itself, which has been installed as a [Self-hosted Standard Edition single-node cluster](https://www.ibm.com/docs/en/instana-observability/current?topic=backend-installing-standard-edition){target=wsc_instanadoc} on a server with the *x86_64* architecture.

13. **Click the _atsocpd1_  hyperlink in the _Names_ column.**

    You should see a screen that looks like this:

    ![instana-openshift](instana-openshift.png)

    OpenShift clusters are monitored by simply deploying a containerized Instana agent onto the cluster. Once deployed, the agent will report detailed data about the cluster and the resources deployed on it. Instana automatically discovers and monitors clusters, CronJobs, Nodes, Namespaces, Deployments, DaemonSets, StatefulSets, Services, and Pods.

    The _Summary_ page shows the most relevant information for the cluster as a whole. The CPU, Memory, and Pod usage information are shown. The other sections, such as "Top Nodes" and "Top Deployments", show potential hotspots which you might want to have a look at.

14. **Click the _Nodes_ tab.**

    The _Nodes_ tab shows all of the Kubernetes nodes in the cluster in real time.

    Your screen should look like this now:

    ![instana-kubernetes-nodes](instana-kubernetes-nodes.png)
 
    An interesting thing to note on this screen is that by observing the *Monitored By Instana* column, you can see that only the *compute* nodes, and not the *control* nodes, are being monitored by an Instana agent.  This is because application workloads run only on compute nodes.

15. **Click the _Namespace_ tab, then search for _robot-shop_ and select it.**.  

    !!! Tip

        Clicking the 'magnifying glass' icon at the right of the search bar may be necessary in order to enable you to enter text in the search field.

    This will bring you to a page similar to what is shown below:

    ![instana-ocp-ns](instana-ocp-ns.png)

    In the _robot-shop_ namespace page, you can see details for all of the Kubernetes resources that are deployed in the _robot-shop_ namespace, including Deployments, Services, Pods, and the relevant metrics for each object.

16. **Expand _Platforms_ and then click _IBM Z HMC_ from the left-side menu.**

    You should see something like this:

    ![instana-zhmc-1](instana-zhmc-1.png)

    Instana supports monitoring IBM Z and LinuxONE hardware metrics and messages via the [IBM Z Hardware Management Console](https://www.ibm.com/docs/en/instana-observability/current?topic=technologies-monitoring-z-hmc){target=wsc_instanadoc}, or _HMC_. 

    In the Instana console (and in the screenshot above), you should see two separate HMCs. *hsyshma1.dmz* is managing a single DPM-mode IBM Z machine, while *wschmc.dmz* is managing three classic-mode (i.e., PR/SM) IBM Z machines.
  
17. **Click the hyperlink for _wschmc.dmz_**

    You will then see a screen like this:

    ![instana-zhmc-2](instana-zhmc-2.png)

    This page shows you high level information about the IBM Z or LinuxONE machines themselves, including number of partitions, adapters, IP addresses, Machine Type Models and Machine Serial numbers.

    ??? Question "Pop Quiz - Which system is a z14, which is a z15, and which is a z16? Click to reveal the answer."
        - QSYS: **z14** (machine type 3906)
        - FSYS: **z15** (machine type 8561)
        - KSYS: **z16** (machine type 3931)

    You may notice that there are only a small number of partitions and adapters listed for each system. This is due to the fact that Instana only displays the objects (LPARs, adapters, channels, etc) that a specific zHMC userid has access to. If you want to add or remove visibility to certain objects in Instana, you do so by managing the zHMC userid just as system administrators already do.

18. **Click the hyperlink for _KSYS_**

    This is the z16 on which the OCP clusters run.

    ![instana-zhmc-3](instana-zhmc-3.png)

    You can now see the overall system utilization as well as that of individual processors. 

19. **Navigate to the _Environmental And Power_ tab**

    ![instana-zhmc-4](instana-zhmc-4.png)

    Instana provides the ability to monitor your environmental and power metrics, including Temperature and Power Consumption. Many clients migrate workloads from distributed platforms to IBM Z in order to reduce power consumption and thus their carbon footprint. With the metrics on this page you can measure the impact that these migrations have.

20. **Navigate to the _Partition_ tab and expand the dropdown next to _KOSP3A_ in the _Name_ column.**

    ![instana-zhmc-5](instana-zhmc-5.png)

    KOSP3A and KOSP3B are the names of the Logical Partitions (LPARs) where our OpenShift on IBM Z clusters are running. With this page, you can monitor the power consumption and processor usage for specific LPARs. This is useful for organizations who would like to see which applications or environments most contribute to power consumption. 

    In the next section, you will look at infrastructure metrics. In our case, the metrics related to a Linux server with an Oracle Database installed on it.

21. **Click the _Infrastructure_ option in the left-side menu.**

    You should see a page similar to this:

    ![instana-infrastructure](instana-infrastructure.png)

    The Infrastructure map provides an overview of all monitored systems. Within each group are pillars comprised of opaque blocks. Each pillar as a whole represents one agent running on the respective system. Each block within a pillar represents the software components running on that system.

    !!! Tip
    
        At first glance it may seem difficult to distinguish between the opaque blocks within each pillar.  Magnifying the view may help, and you can also hover your cursor over a pillar and slowly move your cursor up or down the pillar to see information about each block.

22. **In the Infrastructure search bar, enter `oracledb`.** Two pillars will be returned, representing two different Oracle Databases running on Linux on IBM Z. **Select the pillar for OracleDB @acmeair.**

    You should see something similar to this:

    ![infrastructure-oracledb](infrastructure-oracledb.png)

    You can now see information about the Oracle on IBM Z database monitored by Instana including its version, SID, and more.  

23. **Click the _Open Dashboard_ button for the OracleDB.**

    You should see a screen similar to this:

    ![infrastructure-oracledb2](infrastructure-oracledb2.png)

    Now you see metrics specific to Oracle that a database administrator might be interested in.

    There are many IBM Z technologies supported by Instana, including z/OS. See the list of supported technologies [here](https://www.ibm.com/docs/en/instana-observability/current?topic=configuring-monitoring-supported-technologies){target=wsc_instanadoc}, [here](https://www.ibm.com/docs/en/iooz/1.2.x?topic=installing-configuring-z-apm-connect-components){target=wsc_instanadoc}, and [here](https://www.ibm.com/docs/en/iooz/1.2.x?topic=integrating-omegamon){target=wsc_instanadoc}.

24. **Click the _Analytics_ option in the left-side menu.**

    Instana analytics are integrated into each of the panels you've looked at so far, but you can also directly access them from the left-side menu.

    By default, you're taken to a built-in dashboard for analytics related to Application calls. You can filter by using the left-side options, or by creating filters at the top.

34. **Expand the left-side options and select _Only erroneous_ (1)  and the _robot-shop_ application (2).**

    Once you select these two options, your screen should look similar to this:

    ![instana-analytics-errors-robotshop](instana-analytics-errors-robotshop.png)

    In the screenshot above, notice that near the _Chart_ heading there is a box that indicates that your are being shown _Calls, Erroneous Call Rate, Latency_. Suppose that you wanted to see only _Erroneous Calls_.   

35. **Click in the box (1) and when the dropdown appears, click on _Erroneous Calls_ (2).**

    ![instana-analytics-erroneous-only](instana-analytics-erroneous-only.png)
    
    Your screen should now look like this:

    ![instana-analytics-erroneous-only-2](instana-analytics-erroneous-only-2.png)

    The primary source of errors is starting to become apparent.

35. **Click the _Events_ option in the left-side menu. Click the _Incidents_ tab if you aren't automatically taken there.**

    You will see something similar to this:

    ![instana-incidents](instana-incidents.png)

    Instana can parse all of the requests, calls, traces, and other information into a stream of events and then classify and group them. Instana includes built-in events, predefined health signatures based on integrated algorithms which help you to understand the health of your monitored system in real-time. If a built-in event is not relevant for the monitored system, it can be disabled. Conversely, you can create a custom event in Instana if it does not already exist. These events can then be sent as an alert to a channel of your choice, such as email, Slack, AIOps, Splunk, PagerDuty, Prometheus, a generic webhook, or one of many more supported technologies.

    Right now you're looking at Incidents that Instana has identified during the timeframe. Incidents are created when a key performance indicator such as load, latency, or error rate changes over a certain threshold.

36. **Select the _Issues_ tab.**

    Your screen will look like this:

    ![instana-events-2](instana-events-2.png)

    An issue is an event that is triggered if something out of the ordinary happens. You can think of Incidents as Issues that Instana has correlated with each other to form a cohesive event.

### Using Instana to Identify a Problem

As you looked through the various sections of the Instana dashboard, a few errors kept popping up. In this section, you will use Instana to pinpoint the root cause of the errors. When debugging with Instana, a good place to start is Events.

!!! Note 

    In the following steps you'll get enough information to know what to fix, but in our lab environment we have a load generator continually running that intentionally has errors, so you won't actually make a fix here. 

37. **Go back to the _Incidents_ tab, and click one of the Incidents that says  _Erroneous call rate is too high_ in the _Title_ column and says _payment_ in the _on_ column.**

    ![instana-events-1](instana-events-1.png)

    The _Incidents_ page shows a dynamic graph of all associated issues, events, and correlates it with other incidents to provide a comprehensive overview of the situation regarding service and event impact. It should look like this:

    ![instana-events-3](instana-events-3.png)

38. **Click the _View triggering event_ button**

    You will see a page like this:

    ![instana-events-trigger](instana-events-trigger.png)

    Instana automatically displays a relevant dynamic graph. In this case, it is the erroneous call rate for the payment service. Instana also provides a link to the analysis page for these calls.

39. **Click the "Analyze Calls" button.**

    Now you're back on the Analysis page you looked at previously, but with some filters automatically applied. You can see that the `POST /pay/{id}` endpoint has 100% erroneous calls. Your screen will look like this:

    ![instana-events-analyze-calls](instana-events-analyze-calls.png)

40. **Click to expand the dropdown**:

    ![instana-analytics-0](instana-analytics-0.png)

    Notice how the same information about the erroneous calls for `POST /pay/partner-57` is displayed as it was previously, but Instana did all of the filtering. Because of the 100% error rate, it's clear that this endpoint is having an issue. Instana also provided links to the specific calls that failed.

    ![instana-analytics-1](instana-analytics-1.png)

40. **Click one of the "POST /pay/partner-57" links.**

    ![instana-analytics-2](instana-analytics-2.png)

    On the call page, you see how many instances of the erroneous call there are, a trace of the call and at which point the error occurred, the status code and error messages received, and more. From reading through the information on this page, it's apparent that the source of the error is the `payment` service in Kubernetes. The related endpoints and infrastructure such as the MongoDB and the `user` service look healthy.

41. **Click the "payment" link under "Service Endpoint List".**

    ![instana-analytics-3](instana-analytics-3.png)

    Again you can confirm that the payment service in OpenShift is the cause of these Incidents. At this point, a site reliability engineer or application owner would want to look at the Kubernetes YAML definitions and the python code that was containerized and is running this microservice. For the sake of this demonstration, the error is caused by an intentional bug built into the load generator which is attempting to access a payment endpoint that does not exist.

### Instana Wrap-up

You should now have a better understanding of Instana observability, how to use the platform, and the IBM Z data and metrics it can observe. The observability provided by Instana sets the stage for other IBM solutions to **use that data** to make AI-driven insights around application performance and problem remediation.

## References

- [Instana Product Page](https://www.ibm.com/products/instana){target=wsc_instanadoc}
- [Instana Documentation](https://www.ibm.com/docs/en/instana-observability/current){target=wsc_instanadoc}
- [Instana Supported Technologies](https://www.ibm.com/docs/en/instana-observability/current?topic=configuring-monitoring-supported-technologies){target=wsc_instanadoc}
