# AIOps with IBM Z and LinuxONE
## Turbonomic

### Overview of Turbonomic Application Resource Management 

IBM Turbonomic is an Application Resource Management (ARM) solution for both cloud and on-premises environments.

Application Resource Management platforms continuously analyze applications' resource needs and generate fully automatable actions to ensure applications always get the resources (CPU, memory, etc.) they need to perform.

Turbonomic enables environments to achieve the following conflicting goals at the same time:

- *Assured application performance*: Prevent bottlenecks, upsize containers/VMs, prioritize workload, and reduce storage latency.

- *Efficient use of resources*: Consolidate workloads to reduce infrastructure usage to the minimum, downsize containers, prevent sprawl, and use the most economical cloud offerings.

You configure managed environments as [Turbonomic targets](https://www.ibm.com/docs/en/tarm/8.15.3?topic=documentation-target-configuration){target=wsc_turbodoc}. Turbonomic discovers the entities (physical devices, virtual components and software components) that each target manages, and then performs analysis, anticipates risks to performance or efficiency, and recommends actions you can take to avoid problems before they occur.

[Source and more information](https://www.ibm.com/docs/en/tarm/8.15.3?topic=documentation-product-overview){target=wsc_turbodoc}

### Navigating the Turbonomic Dashboard

#### Homepage

1. **Log in to Turbonomic at [https://nginx-turbonomic.apps.x2pn.dmz/app/](https://nginx-turbonomic.apps.x2pn.dmz/app/){target=wsc_turboconsole}** 

    This link is also available at the [Environment Access Page](../access.md){target=wsc_envaccess} file.

    Your _userNN_ lab userid has been granted the [_Advisor_ role](https://www.ibm.com/docs/en/tarm/8.15.3?topic=tasks-managing-user-accounts){target=wsc_turbodoc}. This means that you can see everything that Turbonomic is managing, but you cannot take actions against the target environments or modify the Turbonomic server itself. Users can be set up either locally on the Turbonomic server or with an authentication service such as an LDAP server. Users can have their access scoped to certain environments, applications, or specific entities on the Turbonomic server so that people can only access what they need to.

    Upon successful login, you should see a page like this:

    ![turbo-supply-chain](turbo-supply-chain.png)

    The first thing to notice is the supply chain on the left side of the page. This is the section with circles connected with arrrows. Turbonomic uses the concept of a supply chain made up of buyers and sellers all with the goal of meeting application resource demand. The supply chain shown on the homepage includes all the entities that Turbonomic identified based on deployment of the KubeTurbo operator on the OpenShift on IBM Z cluster. The relationships and interdependencies between each entity were automatically identified and provide an overview of how each object relates to one another. Because an OpenShift cluster is being monitored, you can see that application and cluster infrastructure components were found, including containers, namespaces, persistent volumes, and the single virtual machines running the OpenShift nodes. This is an interactable chart so you can click on any of the entity types to drill down directly from the homepage.

    ![turbo-business-applications](turbo-business-applications.png)

    In the screenshot above, on the right side of the page, you can see there is a _Top Business Applications_ section. A Business Application in Turbonomic is defined as a group of related objects of your choosing. In the case of this installation, Business Applications are imported from the Instana Application Perspectives, as well as all the related entities that Turbonomic correlated with them, such as the virtual machine the pods are running on, the namespace (project) they're running in, the persistent volumes where they store data, and the Kubernetes cluster itself. This is one example of the integration between Instana and Turbonomic.

    Instana working alongside Turbonomic brings other benefits as well, such as letting Turbonomic see the application response times and transaction speeds and then using that data to scale pod counts up or down to meet defined Service-Level Objectives (SLOs) at minimal cost. Without Instana or another Application Performance Monitoring (APM) solution in place, you would not have any visibility into application response times or metrics, and this Turbonomic functionality would be inaccessible.

    ![turbo-pending-actions-home](turbo-pending-actions-home.png)

    Near the middle of the page, you will see a section titled "Pending Actions". These are all the actions that Turbonomic is recommending you take to make sure applications get the resources they need without over-provisioning. You will learn more about actions later in this tutorial.

#### Search

2. **In the left-side menu, select the _Search_ option.**

    On the search page, you can filter down to specific types of entities that Turbonomic has identified. For example, if you look at Namespaces, it will return all the namespaces, or projects in OpenShift nomenclature, in the target cluster.

3.  **Select the Namespaces option in the list.**

    ![turbo-search-namespaces](turbo-search-namespaces.png)

    Next you will dig into the namespace that contains the Robot Shop sample application.

4.  **Type `robot-shop` (1) in the Search field and then click on the _robot-shop_ link under the _Name_ column (2)**.

    ![turbo-namespace-robotshop](turbo-namespace-robotshop.png)

    You should see a page similar to the following:

    ![turbo-search-namespaces-2](turbo-search-namespaces-2.png)

    You now have a view that is scoped to only the components running in the robot-shop namespace, as well as any related components that those pods interact with, such as virtual machines, storage volumes, etc. You are provided all the actions against these components, the top services and workload controllers by CPU and memory for the robot-shop components, information about any quotas assigned to the project, and more. If you want to scope down an individual's access to a single application, only workloads in a specific datacenter, or a custom group of workloads and components you define, this is an example of what that could look like.

### Turbonomic Actions

#### What are Actions?

After you deploy your targets, Turbonomic starts to perform analysis as part of its Application Resource Management process. This holistic analysis identifies problems in your environment and the actions you can take to resolve and avoid these problems. Turbonomic then generates a set of actions for that particular analysis and displays it in the Pending Actions charts.

[Source and more information](https://www.ibm.com/docs/en/tarm/8.15.3?topic=reference-turbonomic-actions){target=wsc_turbodoc}

#### What Actions are Available for OpenShift on IBM Z Targets?

For OpenShift on IBM Z, Turbonomic can generate the following actions:

<details>
  <summary>Vertically Scale Containers - resize container spec sizes (click to expand)</summary>
  
If a containerized application needs more CPU or memory to ensure it's running with desired performance, Turbonomic can scale up the container spec. In OpenShift terms, this means adjusting the resource requests and/or resource limits applied to the application pods. The reverse is also important - if a container is overprovisioned - it has too much CPU or memory assigned to it - these resources are going to waste. Overprovisioning is a common issue in the containerized world, and it leads to inefficient use of resources, lack of resources for other applications that need it, and wasted money.

</details>

<details>
  <summary>Horizontally Scale Containers - Scale up the number of pods for a microservice (click to expand)</summary>
  
If there is a Service Level Objective (SLO) configured for a containerized application, Turbonomic can scale the number of pods up or down in order to meet the demand at any given moment.

An APM solution such as Instana is required for this action type. Turbonomic needs metric data from the APM about response time and transactions speeds in order to enforce SLOs.

</details>

<details>
  <summary>Pod Moves - move a pod from one node to another (click to expand)</summary>
  
Turbonomic continuously moves pods based on node resources available. The moves are performed in a way that keeps the application available throughout the move. Once executed, Turbonomic will start a new pod on the destination node -> ensure that it is running and ready -> deletes the original pod. This way, there is no perceived downtime to the application end user.

</details>
    
<details>
  <summary>Cluster Scaling - Provisioning or Suspending OpenShift Nodes (click to expand)</summary>
  
Turbonomic will also generate actions to create new nodes in the OpenShift cluster or suspend existing nodes based on its analysis of efficiency (consolidating workloads onto fewer nodes) and performance (avoiding node congestion).

For OpenShift on IBM Z, Turbonomic will only *recommend* actions related to cluster scaling. Turbonomic relies on OpenShift machine autoscaling which is not supported on IBM Z. Therefore, if a node provision/suspension action is generated for an IBM Z cluster, administrators will need to perform that action themselves with manual methods and then Turbonomic will see that change.

</details>

#### Manually Executing Actions

As you have seen, Turbonomic has suggested some pending actions related to some of the sample applications running on our OpenShift on IBM Z cluster.

5. **In the OpenShift console, navigate to your _userNN-project_ (1), click the icon for the NodeJS application (2), click the _Actions_ dropdown (3), and then select _Edit Resource Limits_ (4).**

    !!! Tip

        You may still have your OpenShift Web Console tab open from the Instana lab.  If not, you can find the URL again on the [Environment Access](../access.md){target=wsc_envaccess} page.

    ![usernn-resource-limits](usernn-resource-limits.png)

    These are the default NodeJS application resources. *Requests* are the amount of CPU and memory a container is guaranteed to get. *Limits* are the maximum amount of resources a container can use before they are restricted.

    ![resource-limits-before](resource-limits-before.png)

    The defaults for the NodeJS application have been intentionally set to be very low, in order to generate recommended actions in Turbonomic.

6.  **In the Turbonomic console, log out of your _userNN_ username using the button in the bottom-left corner of the menu.**
    
7.  **Log back in to Turbonomic with the userid _userNN-actions_ and the same password that you've been using for _userNN_.**

    This userid has permissions to execute actions against a sample application in your _userNN-project_ namespace. 

    Because this userid is scoped to only your _userNN-project_ namespace, that is all you can see.

8.  **From the home page, click the _userNN-project_ Business Application.**

9.  **Click the _Scaling Actions_ link in the _Pending Actions_ section, just to the right of the supply chain chart.**

    ![turbo-scaling-actions](turbo-scaling-actions.png)

    The action(s) here are related to the microservices in the _userNN-project_. You should see an action related to the memory resizing for the NodeJS frontend application .

    !!! Note

        The lab is designed for you to work with the memory resizing action against the _nodejs-userNN_ application.  If there are other scaling actions, you may ignore those.  If the memory resizing action for _nodejs-userNN_ also includes a CPU resizing action, that's fine too as it won't impact the lab instructions.

10. **Click the details button to far right of the _nodejs-userNN_ action to show details about the proposed action.**

    ![turbo-scaling-actions-2](turbo-scaling-actions-2.png)

    Here you see more details about exactly what Turbonomic is recommending you do via the action. It wants to resize the memory limits for the pod, and you can see the result that Turbonomic expects in terms of resource utilization as a percentage of changing the memory limits. In the screen snippet below Turbonomic expects to go from using 100% of the memory limit to 38.46% of the memory limit. The percentages shown to you may vary slightly from the 100% and 38.46% shown in the screen snippet.

    ![turbo-scaling-actions-3](turbo-scaling-actions-3.png)

    Because this is a manual action (rather than automatic), you are provided with a button in the user interface to directly execute the action and make the proposed changes. Because you are logged in with credentials scoped to this namespace, you can execute the action. If you were logged in with your _userNN_ userid with the Advisor role, you would not be able to execute the action.

11. **Click the green _Execute Action_ button.**

    Turbonomic will apply the recommended changes to the NodeJS application running on OpenShift. You can see the executed action in the _userNN-project_ business application "All actions" panel. **Tip:** You may need to scroll to the bottom of the `userNN-project` business application page in order to see the "All actions" panel.

    !!! Tip

        After clicking your _userNN-project_ in the _Top Business Applications_ list, you may need to scroll to the bottom of the page in order to see the "All actions" panel.  If you still do not see your completed action, you may need to click the _Show All_ button and page through the table to see your completed action.  Ask an instructor for help if you have trouble finding your completed action.

    ![executed-actions](executed-actions.png)

12. **In the OpenShift console, navigate back to the _nodejs-userNN_ resource limits to see the new values.** You should notice that the new memory limit is 208MB instead of the value of 80MB that it was set to before you executed the Turbonomic action. You may already be in the right place in your OpenShift Web Console, but if not, refer to step 1 in this section if you need a refresher on how to find this information, or ask an instructor for help.

    ![resource-limits-after](resource-limits-after.png)

    Although this type of manual action with human review and execution is extremely helpful for reducing the amount of time and thought put into container resizing, the goal of AIOps solutions is to *automate* as many of these processes as possible. Turbonomic supports automatic execution of actions. We could have made this resizing action automatic, but then that would have defeated the purpose of giving you hands-on experience with this lab.

    As an example of an automatic action, the Robot Shop application (which you worked with in the Instana lab) containers are resized on a daily basis. The schedule for automated actions can be determined by operations teams. As Turbonomic learns more about the application, its performance, and the impact of the actions it executes, it will adjust accordingly to ensure that each pod has enough CPU and memory to perform well, but not so much that the resources are going to waste.  

### Turbonomic Wrap-up

In this section, you have seen some of the capabilities of Turbonomic Application Resource Management of an OpenShift on IBM Z cluster. Turbonomic has many more capabilities that were not covered in this lab, which you can read more about in the <a href="https://www.ibm.com/docs/en/tarm/8.10.3?topic=documentation-getting-started" target="wsc_turbodoc">Turbonomic Documentation</a> as well as in this <a href="https://developer.ibm.com/articles/understanding-application-resource-management-using-turbonomic/" target="_blank">IBM article</a>.

## References
- <a href="https://www.ibm.com/products/turbonomic" target="wsc_turbodoc">Turbonomic Product Page</a>
- <a href="https://www.ibm.com/docs/en/tarm" target="wsc_turbodoc">Turbonomic Documentation</a>
- <a href="https://www.ibm.com/products/turbonomic/integrations" target="wsc_turbodoc">Turbonomic Integrations</a>

