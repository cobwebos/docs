---
title: "教程 - 使用用于 Node.js 的 Azure Batch 客户端库 | Microsoft Docs"
description: "了解 Azure Batch 的基本概念，并使用 Node.js 构建简单的解决方案。"
services: batch
author: shwetams
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: nodejs
ms.topic: hero-article
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: shwetams
ms.openlocfilehash: c48171d8634a651718a0775183414f463c6a468c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-batch-sdk-for-nodejs"></a>用于 Node.js 的批处理 SDK 入门

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
> * [Node.js](batch-nodejs-get-started.md)
>
>

了解使用 [Azure Batch Node.js SDK](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/) 在 Node.js 中生成批处理客户端的基础知识。 我们采用分步方式来了解一个 Batch 应用程序的方案，然后通过 Node.js 客户端设置该方案。  

## <a name="prerequisites"></a>先决条件
本文假设你有 Node.js 的实践知识并熟悉 Linux， 同时还假设你已设置 Azure 帐户并具有创建 Batch 和存储服务所需的访问权限。

我们建议你在完成本文概述的步骤之前，先阅读 [Azure Batch 技术概述](batch-technical-overview.md)。

## <a name="the-tutorial-scenario"></a>教程方案
让我们了解批处理工作流方案。 我们有一个简单的以 Python 编写的脚本，该脚本从 Azure Blob 存储容器下载所有 csv 文件，并将其转换为 JSON。 若要并行处理多个存储帐户容器，可将脚本部署为 Azure Batch 作业。

## <a name="azure-batch-architecture"></a>Azure Batch 体系结构
下图描绘了如何使用 Azure Batch 和 Node.js 客户端来伸缩 Python 脚本。

![Azure Batch 方案](./media/batch-nodejs-get-started/BatchScenario.png)

node.js 客户端通过一个准备任务（稍后详细介绍）和一系列其他任务部署批处理作业，具体取决于存储帐户中的容器数。 可以从 GitHub 存储库下载脚本。

* [Node.js 客户端](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/nodejs_batch_client_sample.js)
* [准备任务 shell 脚本](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/startup_prereq.sh)
* [将 Python csv 转换为 JSON 的处理程序](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/processcsv.py)

> [!TIP]
> 指定链接中的 Node.js 客户端不包含可部署为 Azure Function App 的特定代码。 如需创建该应用的说明，可参阅以下链接。
> - [创建 Function App](../azure-functions/functions-create-first-azure-function.md)
> - [创建计时器触发器函数](../azure-functions/functions-bindings-timer.md)
>
>

## <a name="build-the-application"></a>构建应用程序

现在，让我们一步步按过程来构建 Node.js 客户端：

### <a name="step-1-install-azure-batch-sdk"></a>步骤 1：安装 Azure Batch SDK

可以使用 npm install 命令安装用于 Node.js 的 Azure Batch SDK。

`npm install azure-batch`

该命令安装最新版的 azure-batch Node SDK。

>[!Tip]
> 在 Azure Function App 中，若要运行 npm install 命令，可以转到 Azure Function 的“设置”选项卡中的“Kudu 控制台”。 在此示例中，目的是安装用于 Node.js 的 Azure Batch SDK。
>
>

### <a name="step-2-create-an-azure-batch-account"></a>步骤 2：创建 Azure Batch 帐户

可以通过 [Azure 门户](batch-account-create-portal.md)或命令行 ([PowerShell](batch-powershell-cmdlets-get-started.md) /[Azure CLI](https://docs.microsoft.com/cli/azure/overview)) 创建该帐户。

下面是通过 Azure CLI 创建该帐户的命令。

创建一个资源组。如果你已经有一个需要在其中创建 Batch 帐户的资源组，则请跳过此步骤：

`az group create -n "<resource-group-name>" -l "<location>"`

接下来，创建 Azure Batch 帐户。

`az batch account create -l "<location>"  -g "<resource-group-name>" -n "<batch-account-name>"`

每个批处理帐户都有其相应的访问密钥。 需要使用这些密钥才能在 Azure Batch 帐户中创建更多的资源。 对生产环境有利的做法是使用 Azure Key Vault 来存储这些密钥。 然后即可为应用程序创建服务主体。 应用程序可以使用该服务主体创建一个 OAuth 令牌，以便访问密钥保管库中的密钥。

`az batch account keys list -g "<resource-group-name>" -n "<batch-account-name>"`

复制并存储可在后续步骤中使用的密钥。

### <a name="step-3-create-an-azure-batch-service-client"></a>步骤 3：创建 Azure Batch 服务客户端
以下代码片段首先导入 azure-batch Node.js 模块，然后创建 Batch 服务客户端。 需先使用从前一步骤复制的批处理帐户密钥创建 SharedKeyCredentials 对象。

```nodejs
// Initializing Azure Batch variables

var batch = require('azure-batch');

var accountName = '<azure-batch-account-name>';

var accountKey = '<account-key-downloaded>';

var accountUrl = '<account-url>'

// Create Batch credentials object using account name and account key

var credentials = new batch.SharedKeyCredentials(accountName,accountKey);

// Create Batch service client

var batch_client = new batch.ServiceClient(credentials,accountUrl);

```

Azure Batch URI 可以在 Azure 门户的“概览”选项卡中找到。 它的格式为：

`https://accountname.location.batch.azure.com`

请参阅屏幕截图：

![Azure Batch URI](./media/batch-nodejs-get-started/azurebatchuri.png)



### <a name="step-4-create-an-azure-batch-pool"></a>步骤 4：创建 Azure Batch 池
Azure Batch 池包含多个 VM（也称批处理节点）。 Azure Batch 服务将任务部署在这些节点上并对其进行管理。 可以为池定义以下配置参数。

* 虚拟机映像类型
* 虚拟机节点大小
* 虚拟机节点数目

> [!Tip]
> 虚拟机节点的大小和数目主要取决于需要并行运行的任务数以及任务本身。 建议通过测试来确定理想的数目和大小。
>
>

以下代码片段创建配置参数对象。

```nodejs
// Creating Image reference configuration for Ubuntu Linux VM
var imgRef = {publisher:"Canonical",offer:"UbuntuServer",sku:"14.04.2-LTS",version:"latest"}

// Creating the VM configuration object with the SKUID
var vmconfig = {imageReference:imgRef,nodeAgentSKUId:"batch.node.ubuntu 14.04"}

// Setting the VM size to Standard F4
var vmSize = "STANDARD_F4"

//Setting number of VMs in the pool to 4
var numVMs = 4
```

> [!Tip]
> 如需可供 Azure Batch 使用的 Linux VM 映像及其 SKU ID 的列表，请参阅[虚拟机映像列表](batch-linux-nodes.md#list-of-virtual-machine-images)。
>
>

定义池配置后，可以创建 Azure Batch 池。 批处理池命令可创建 Azure 虚拟机节点并对其进行准备，使之能够接收要执行的任务。 每个池都应有一个可在后续步骤中引用的唯一 ID。

以下代码片段可创建 Azure Batch 池。

```nodejs
// Create a unique Azure Batch pool ID
var poolid = "pool" + customerDetails.customerid;
var poolConfig = {id:poolid, displayName:poolid,vmSize:vmSize,virtualMachineConfiguration:vmconfig,targetDedicatedComputeNodes:numVms,enableAutoScale:false };
// Creating the Pool for the specific customer
var pool = batch_client.pool.add(poolConfig,function(error,result){
    if(error!=null){console.log(error.response)};
});
```

你可以检查所创建池的状态，确保状态为“活动”，然后再继续操作，将作业提交到该池。

```nodejs
var cloudPool = batch_client.pool.get(poolid,function(error,result,request,response){
        if(error == null)
        {

            if(result.state == "active")
            {
                console.log("Pool is active");
            }
        }
        else
        {
            if(error.statusCode==404)
            {
                console.log("Pool not found yet returned 404...");    

            }
            else
            {
                console.log("Error occurred while retrieving pool data");
            }
        }
        });
```

下面是由 pool.get 函数返回的结果对象示例。

```
{ id: 'processcsv_201721152',
  displayName: 'processcsv_201721152',
  url: 'https://<batch-account-name>.centralus.batch.azure.com/pools/processcsv_201721152',
  eTag: '<eTag>',
  lastModified: 2017-03-27T10:28:02.398Z,
  creationTime: 2017-03-27T10:28:02.398Z,
  state: 'active',
  stateTransitionTime: 2017-03-27T10:28:02.398Z,
  allocationState: 'resizing',
  allocationStateTransitionTime: 2017-03-27T10:28:02.398Z,
  vmSize: 'standard_a1',
  virtualMachineConfiguration:
   { imageReference:
      { publisher: 'Canonical',
        offer: 'UbuntuServer',
        sku: '14.04.2-LTS',
        version: 'latest' },
     nodeAgentSKUId: 'batch.node.ubuntu 14.04' },
  resizeTimeout:
   { [Number: 900000]
     _milliseconds: 900000,
     _days: 0,
     _months: 0,
     _data:
      { milliseconds: 0,
        seconds: 0,
        minutes: 15,
        hours: 0,
        days: 0,
        months: 0,
        years: 0 },
     _locale:
      Locale {
        _calendar: [Object],
        _longDateFormat: [Object],
        _invalidDate: 'Invalid date',
        ordinal: [Function: ordinal],
        _ordinalParse: /\d{1,2}(th|st|nd|rd)/,
        _relativeTime: [Object],
        _months: [Object],
        _monthsShort: [Object],
        _week: [Object],
        _weekdays: [Object],
        _weekdaysMin: [Object],
        _weekdaysShort: [Object],
        _meridiemParse: /[ap]\.?m?\.?/i,
        _abbr: 'en',
        _config: [Object],
        _ordinalParseLenient: /\d{1,2}(th|st|nd|rd)|\d{1,2}/ } },
  currentDedicated: 0,
  targetDedicated: 4,
  enableAutoScale: false,
  enableInterNodeCommunication: false,
  maxTasksPerNode: 1,
  taskSchedulingPolicy: { nodeFillType: 'Spread' } }
```


### <a name="step-4-submit-an-azure-batch-job"></a>步骤 5：提交 Azure Batch 作业
Azure Batch 作业是包含相似任务的逻辑组。 在我们的方案中，它是指“将 csv 处理成 JSON”。 这里的每个任务可能都在处理每个 Azure 存储容器中存在的 csv 文件。

这些任务会并行运行，并且跨多个节点部署，由 Azure Batch 服务进行协调。

> [!Tip]
> 可以使用 [maxTasksPerNode](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) 属性指定能够在单个节点上同时运行的最大任务数。
>
>

#### <a name="preparation-task"></a>准备任务

所创建的 VM 节点是空白 Ubuntu 节点。 通常需安装一组程序作为必备组件。
对于 Linux 节点，通常可在实际任务运行之前使用 shell 脚本安装必备组件。 不过，也可通过任何可编程的可执行文件来完成该操作。
在此示例中，[shell 脚本](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/startup_prereq.sh)安装 Python-pip 以及用于 Python 的 Azure 存储 SDK。

可以将脚本上传到 Azure 存储帐户，并生成用于访问脚本的 SAS URI。 还可使用 Azure 存储 Node.js SDK 自动执行此过程。

> [!Tip]
> 作业的准备任务仅在需要运行特定任务的 VM 节点上运行。 如果需要在所有节点上安装必备组件，而不管在其上运行的任务是什么，则可在添加池时使用 [startTask](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) 属性。 可以使用以下准备任务定义作为参考。
>
>

准备任务在提交 Azure Batch 作业时指定。 以下是准备任务配置参数：

* **ID**：准备任务的唯一标识符
* **commandLine**：用于执行任务可执行文件的命令行
* **resourceFiles**：对象数组，提供运行此任务时需下载的文件的详细信息。  下面是其选项
    - blobSource：文件的 SAS URI
    - filePath：下载并保存文件所需的本地路径
    - fileMode：仅适用于 Linux 节点。fileMode 采用八进制格式，默认值为 0770
* **waitForSuccess**：如果设置为 true，该任务不会在准备任务失败的情况下运行
* **runElevated**：如果需要提升权限才能运行该任务，则设置为 true。

以下代码片段显示了准备任务脚本配置示例：

```nodejs
var job_prep_task_config = {id:"installprereq",commandLine:"sudo sh startup_prereq.sh > startup.log",resourceFiles:[{'blobSource':'Blob SAS URI','filePath':'startup_prereq.sh'}],waitForSuccess:true,runElevated:true}
```

如果不需安装任何必备组件即可运行任务，则可跳过准备任务。 以下代码创建显示名称为“process csv files”的作业。

 ```nodejs
 // Setting up Batch pool configuration
 var pool_config = {poolId:poolid}
 // Setting up Job configuration along with preparation task
 var jobId = "processcsvjob"
 var job_config = {id:jobId,displayName:"process csv files",jobPreparationTask:job_prep_task_config,poolInfo:pool_config}
 // Adding Azure batch job to the pool
 var job = batch_client.job.add(job_config,function(error,result){
     if(error != null)
     {
         console.log("Error submitting job : " + error.response);
     }});
```


### <a name="step-5-submit-azure-batch-tasks-for-a-job"></a>步骤 6：为作业提交 Azure Batch 任务

创建“process csv”作业以后，让我们创建该作业的任务。 假设我们有四个容器，则必须创建四个任务，一个容器一个任务。

如果我们查看 [Python 脚本](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/processcsv.py)，可以看到它接受两个参数：

* container name：要从其中下载文件的存储容器
* pattern：文件名称模式的可选参数

假设我们有四个容器，分别为“con1”、“con2”、“con3”、“con4”。以下代码显示了如何将任务提交到我们此前创建的 Azure Batch 作业“process csv”。

```nodejs
// storing container names in an array
var container_list = ["con1","con2","con3","con4"]
    container_list.forEach(function(val,index){           

           var container_name = val;
           var taskID = container_name + "_process";
           var task_config = {id:taskID,displayName:'process csv in ' + container_name,commandLine:'python processcsv.py --container ' + container_name,resourceFiles:[{'blobSource':'<blob SAS URI>','filePath':'processcsv.py'}]}
           var task = batch_client.task.add(poolid,task_config,function(error,result){
                if(error != null)
                {
                    console.log(error.response);     
                }
                else
                {
                    console.log("Task for container : " + container_name + "submitted successfully");
                }



           });

    });
```

该代码将多个任务添加到池。 每个任务在所创建的 VM 池中的一个节点上执行。 如果任务数超出池中的 VM 数或 maxTasksPerNode 属性，则任务会等待节点可用。 此业务流程由 Azure Batch 自动处理。

门户提供了有关任务和作业状态的详细视图。 也可使用列表，获取 Azure Node SDK 中的函数。 文档[链接](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/Job.html)中提供了详细信息。

## <a name="next-steps"></a>后续步骤

- 如果对 Batch 服务不熟悉，建议查看 [Azure Batch 功能概述](batch-api-basics.md) 一文。
- 请查看 [Batch Node.js reference](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/)（批处理 Node.js 参考），了解批处理 API。

