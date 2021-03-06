---

copyright:
  years: 2017,2018
lastupdated: "2018-03-07"

---

<!-- Attribute definitions -->
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Provisioning an {{site.data.keyword.iae_full_notm}} service instance

You can create an {{site.data.keyword.iae_full_notm}} service instance through one of the following ways:

* [From the {{site.data.keyword.Bluemix_short}} console](#creating-a-service-instance-from-the-ibm-cloud-console)
* [Using the {{site.data.keyword.Bluemix_short}}  command-line interface (CLI)](#creating-a-service-instance-using-the-ibm-cloud-command-line-interface)
* [Using the Resource Controller REST API](#creating-a-service-instance-using-the-resource-controller-rest-api)

**Prerequisite**: You must have access to the {{site.data.keyword.Bluemix_short}} US-South or United Kingdom  region.

## Creating a service instance from the IBM Cloud console

To create an {{site.data.keyword.iae_full_notm}} instance:
1. Log into the [{{site.data.keyword.Bluemix_short}} console]( https://console.bluemix.net).
1. Click **Create resource**, search for `{{site.data.keyword.iae_short}}` and then click on the tile to open the service instance creation page.
1. On the catalog page, choose the region in which you want the service instance to be deployed. {{site.data.keyword.iae_short}}  deployments are available in US South and United Kingdom.
1. Choose the resource group under which you want to create the service instance. Select a plan and click **Configure**.
1. On the configuration page, choose the hardware configuration, number of compute nodes and software package of your choice. Click **Create**.
1. The service instance is provisioned in the background and can take anywhere between 10 to 20 minutes to provision depending on the hardware type and software package you chose. Visit the {{site.data.keyword.Bluemix_short}} page after some time to check the status of the provisioned instance.

### Supported plans

{{site.data.keyword.iae_full_notm}} offers three plans: **Lite**, **Standard-Hourly**, and **Standard-Monthly**.  

An {{site.data.keyword.iae_short}} service instance comprises one cluster made up of one management node and N compute nodes, where N is the number of compute nodes that you specify when creating the cluster. A cluster with 3 compute nodes, for example, has 4 nodes in total and all 4 nodes are billed on an hourly basis.

| Plan | Hardware types | Software packages | Restrictions |
|------|----------------|-------------------|------------- |
| **Lite** | Default | AE 1.0 Spark, AE 1.0 Spark and Hadoop, AE 1.0 Spark and Hive  | 1.	Maximum of one tile per IBM Cloud account every 30 days. </br> 2.	Maximum of one cluster with up to 3 compute nodes. </br> 3.	Free usage limit is 50 node hours. After 50 node hours, the cluster will be disabled. This means, for example, that a cluster with 4 nodes (3 compute node and 1 management node) will be disabled after 12.5 hours. While the cluster is disabled, it cannot be scaled up or customized. </br> A grace period of 24 hours is given to upgrade your user account to a paid account, and to upgrade the service instance to the Standard-Hourly plan. </br> If the service instance is not upgraded, then it will expire and be deleted. </br> **Note:** You are entitled to one service instance per month. If you delete the service instance or it expires after the free 50 node hours, you will not be able to create a new one until after the month has passed.|
| **Standard-Hourly** | Default, memory intensive |	AE 1.0 Spark, AE 1.0 Spark and Hadoop, AE 1.0 Spark and Hive  | NA |
| **Standard-Monthly** | Default, memory intensive | AE 1.0 Spark, AE 1.0 Spark and Hadoop, AE 1.0 Spark and Hive  | NA |

Hardware specifications:

 - 	**Default**: 4 vCPU, 16 GB RAM, 2 x 300 GB HDFS disk on each compute node
 -	**Memory intensive**: 32 vCPU, 128 GB RAM, 3 x 300 GB HDFS disk on each compute node

Software packages:

 - Choose **AE 1.0 Spark** if you are planning to run only Spark workloads.
 - Choose **AE 1.0 Spark and Hive** if you are planning to run Hive and/or Spark workloads. In addition to the components you get with the Spark package, you also get Hive, as part of the components of the Hive package.
 - Choose **AE 1.0 Spark and Hadoop** if you are planning to run Hadoop workloads in addition to Spark workloads. In addition to the components you get with the Spark package, you also get Oozie, HBase and Hive, as part of the components of the Hadoop package.


## Creating a service instance using the IBM Cloud command-line interface

Download and configure the {{site.data.keyword.Bluemix_short}} CLI. Follow the instructions [here](https://console.bluemix.net/docs/cli/reference/bluemix_cli/download_cli.html#download_install).

### First-time setup:

After you have downloaded and configured the CLI, set the API end point and log in:
```
bx api https://api.ng.bluemix.net
bx login
```
{: codeblock}

The API end points for the supported regions are as follows:

 - US South: https://api.ng.bluemix.net
 - United Kingdom: https://api.eu-gb.bluemix.net

### Creating a service instance:
```
bx resource service-instance-create <service instance name> ibmanalyticsengine <Plan name> <region> -p <path to JSON file with cluster parameters> ```

{: codeblock}

For example:
```
bx resource service-instance-create MyServiceInstance ibmanalyticsengine lite us-south -p /usr/testuser/cluster_specification.json
```
Supported plan names are **lite**, **standard-hourly**, and **standard-monthly**.
Supported regions are: **us-south** and **eu-gb**.

Sample cluster specification JSON file  
```
{
  "num_compute_nodes": 1,
  "hardware_config": "default",
  "software_package": "ae-1.0-spark"
}
```
{: codeblock}

### Brief description of cluster specification parameters

1. **`num_compute_nodes`** (Required): Number of compute nodes required in the cluster.
2. **`hardware_config`** (Required): Represents the instance size of the cluster. Accepted value: _`default`_ and _`memory-intensive`_  
3. **`software_package`** (Required): Determines set of services to be installed on the cluster. Accepted value: _`ae-1.0-spark`_,  _`ae-1.0-hive-spark`_ and _`ae-1.0-hadoop-spark`_
4. **`customization`** (Optional): Array of customization actions to be run on all nodes of the cluster once it is created. At the moment, only one customization action can be specified. The various types of customization actions that can be specified are discussed in detail in [Customizing clusters](./customizing-cluster.html).
<br>

Sample bx CLI response for the create instance command example previously shown: <br>

```
Creating service instance MyServiceInstance in resource group Default of account <account details>...
OK
Service instance MyServiceInstance was created.
Name              Location   State     Type              Tags   
MyServiceInstance us-south   inactive  service_instance   
```
Service provisioning happens asynchronously. Successful response just means that the provisioning request has been accepted. Service provisioning is considered complete only when the associated cluster is created and is `active`.

## Provisioning overview

For an overview of how a cluster is provisioned and how to check your cluster provisioning state, see [Track cluster provisioning](./track-instance-provisioning.html).


## Querying for service provisioning status

To query the service provisioning status, enter the following command:
```
bx resource service-instance MyServiceInstance ```

{: codeblock}

Sample response:
```
Name: MyServiceInstance
Id: crn:v1:bluemix:public:ibmanalyticsengine:us-south:a/XXXXX::
Location:              us-south   
Service Name:          ibmanalyticsengine
Service Plan Name:     lite   
Resource Group Name:   default   
State:                 active   
Type:                  service_instance   
Tags:
```

Note: The state indicates the service provisioning status. When provisioning is ongoing, it's `inactive`. When provisioning has completed, it will be `active`.

## Creating a service instance using the Resource Controller REST API

To invoke the Resource Controller REST API to create a service instance, you need a resource group ID.

To get the resource group ID, log into the {{site.data.keyword.Bluemix_short}} CLI and run the following command:
```
bx resource groups ```

{: codeblock}

Sample result:
```
Retrieving all resource groups under account <Account details..>
OK
Name      ID      Default Group   State
Default   XXXXX   true            ACTIVE ```

You also need the `resource_plan_id` to create a service instance. The following resource plan IDs are supported:

- For Lite use: `7715aa8d-fb59-42e8-951e-5f1103d8285e`
- For Standard-Hourly use: `3175a5cf-61e3-4e79-aa2a-dff9a4e1f0ae`
- For Standard-Monthly use: `34594484-afda-40e6-b93b-341fbbaed242`

To create an instance via the Resource Controller REST API enter:
```
curl \
  --request POST \
  --url 'https://resource-controller.ng.bluemix.net/v1/resource_instances'   \
  --header 'accept: application/json'   \
  --header 'authorization: Bearer <IAM token>'   \
  --data  @provision.json

cat provision.json
{
    "name": "MyServiceInstance",
    "resource_plan_id": "7715aa8d-fb59-42e8-951e-5f1103d8285e",
    "resource_group_id": "XXXXX",
    "region_id": "us-south",
    "parameters": {
        "hardware_config": "default",
        "num_compute_nodes": "1",
        "software_package": "ae-1.0-spark"
    }    
}

```
{: codeblock}

For the United Kingdom region, use the end point `https://resource-controller.eu-gb.bluemix.net`.

To get the IAM token, perform the following [steps](./Retrieve-IAM-access-token.html).


## Plan upgrading

You can only upgrade from a Lite plan to a Standard-Hourly plan. You can upgrade by using the {{site.data.keyword.iae_full_notm}} UI or by using the {{site.data.keyword.Bluemix_short}} CLI.

To upgrade a Lite plan using the {{site.data.keyword.iae_short}}Analytics Engine dashboard in IBM Cloud:

1. Open your {{site.data.keyword.iae_full_notm}} service dashboard page.

1. Select the **Plan** tab and then select the Standard-Hourly plan.

1. Click Save.

To upgrade using the {{site.data.keyword.Bluemix_short}} CLI enter the following command:

```
bx resource service-instance-update <your service instance name> --service-plan-id <resource_plan_id>
```

{: codeblock}


`resource_plan_id` is the `plan_id` of the target plan that you want to upgrade your service instance to.
