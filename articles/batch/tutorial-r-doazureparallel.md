---
title: 使用 Azure Batch 进行并行 R 模拟
description: 教程 - 分步说明如何在 Azure Batch 中使用 R doAzureParallel 包运行 Monte Carlo 财务模拟
ms.devlang: r
ms.topic: tutorial
ms.date: 01/23/2018
ms.custom: mvc
ms.openlocfilehash: c9708360df4a7fb711a3d57b39f33c576c75a0d5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82117091"
---
# <a name="tutorial-run-a-parallel-r-simulation-with-azure-batch"></a>教程：使用 Azure Batch 运行并行 R 模拟 

使用 [doAzureParallel](https://www.github.com/Azure/doAzureParallel) 包大规模运行并行 R 工作负荷。该包是一种轻型 R 包，允许直接从 R 会话使用 Azure Batch。 doAzureParallel 包在常用 [foreach](https://cran.r-project.org/web/packages/foreach/index.html) R 包的基础上生成。 doAzureParallel 执行 foreach 循环的每个迭代，将其作为 Azure Batch 任务提交。

本教程介绍如何部署 Batch 池，然后直接在 RStudio 中通过 Azure Batch 运行并行 R 作业。 学习如何：
 

> [!div class="checklist"]
> * 安装 doAzureParallel 并将其配置为访问 Batch 帐户和存储帐户
> * 创建一个 Batch 池，作为 R 会话的并行后端
> * 在池中运行示例并行模拟

## <a name="prerequisites"></a>必备条件

* 已安装的 [R](https://www.r-project.org/) 发行版，例如 [Microsoft R Open](https://mran.microsoft.com/open)。 使用 R 3.3.1 或更高版。

* [RStudio](https://www.rstudio.com/)：商业版或开源 [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop)。 

* Azure Batch 帐户和 Azure 存储帐户。 若要创建这些帐户，请参阅 Batch 快速入门（使用 [Azure 门户](quick-create-portal.md)或 [Azure CLI](quick-create-cli.md)）。 

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 
## <a name="install-doazureparallel"></a>安装 doAzureParallel

在 RStudio 控制台中安装 [doAzureParallel GitHub 包](https://www.github.com/Azure/doAzureParallel)。 以下命令在当前 R 会话中下载并安装该包及其依赖项： 

```R
# Install the devtools package  
install.packages("devtools") 

# Install rAzureBatch package
devtools::install_github("Azure/rAzureBatch") 

# Install the doAzureParallel package 
devtools::install_github("Azure/doAzureParallel") 
 
# Load the doAzureParallel library 
library(doAzureParallel) 
```
安装可能需要数分钟。

若要使用以前获得的帐户凭据来配置 doAzureParallel，请在工作目录中生成名为 *credentials.json* 的配置文件： 

```R
generateCredentialsConfig("credentials.json") 
``` 

使用 Batch 帐户和存储帐户的名称和密钥来填充此文件。 保留 `githubAuthenticationToken` 设置不变。

完成后，凭据文件如下所示： 

```json
{
  "batchAccount": {
    "name": "mybatchaccount",
    "key": "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==",
    "url": "https://mybatchaccount.mybatchregion.batch.azure.com"
  },
  "storageAccount": {
    "name": "mystorageaccount",
    "key": "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=="
  },
  "githubAuthenticationToken": ""
}
```

保存文件。 然后运行以下命令，设置当前 R 会话的凭据： 

```R
setCredentials("credentials.json") 
```

## <a name="create-a-batch-pool"></a>创建 Batch 池 

doAzureParallel 包括一个函数，用于生成运行并行 R 作业所需的 Azure Batch 池（群集）。 这些节点运行基于 Ubuntu 的 [Azure 数据科学虚拟机](../machine-learning/data-science-virtual-machine/overview.md)。 Microsoft R Open 和常用 R 包已预装在此映像上。 可以查看或自定义某些群集设置，例如节点的数量和大小。 

若要在工作目录中生成群集配置 JSON 文件，请执行以下操作： 
 
```R
generateClusterConfig("cluster.json")
``` 
 
打开要查看默认配置的文件，其中包括 3 个专用节点和 3 个[低优先级](batch-low-pri-vms.md)节点。 这些设置只是示例，可以进行试验或修改。 专用节点为池保留。 低优先级节点在 Azure 有剩余 VM 容量时以优惠价提供。 如果 Azure 没有足够的容量，低优先级节点会变得不可用。 

对于本教程，请将配置更改如下：

* 将 `maxTasksPerNode` 增加到 *2*，以便充分利用每个节点上的两个核心
* 将 `dedicatedNodes` 设置为 *0*，以便尝试适用于 Batch 的低优先级 VM。 将 `lowPriorityNodes` 的 `min` 设置为 *5*， 并将 `max` 设置为 *10*，或者根据需要选择更小的数字。 

其余设置保留默认值，然后保存文件。 该属性应与下面类似：

```json
{
  "name": "myPoolName",
  "vmSize": "Standard_D2_v2",
  "maxTasksPerNode": 2,
  "poolSize": {
    "dedicatedNodes": {
      "min": 0,
      "max": 0
    },
    "lowPriorityNodes": {
      "min": 5,
      "max": 10
    },
    "autoscaleFormula": "QUEUE"
  },
  "containerImage": "rocker/tidyverse:latest",
  "rPackages": {
    "cran": [],
    "github": [],
    "bioconductor": []
  },
  "commandLine": []
}
```

现在创建群集。 Batch 会立即创建池，但分配和启动计算节点则需要数分钟。 在群集可用以后，将其注册为 R 会话的并行后端。 

```R
# Create your cluster if it does not exist; this takes a few minutes
cluster <- makeCluster("cluster.json") 
  
# Register your parallel backend 
registerDoAzureParallel(cluster) 
  
# Check that the nodes are running 
getDoParWorkers() 
```

输出显示 doAzureParallel 的“执行辅助角色”数。 此数是节点数乘以 `maxTasksPerNode` 值。 如果已如前所述修改群集配置，则此数为 *10*。 
 
## <a name="run-a-parallel-simulation"></a>运行并行模拟

创建群集以后，即可使用注册的并行后端（Azure Batch 池）运行 foreach 循环。 例如，可以运行 Monte Carlo 财务模拟，先在本地使用标准的 foreach 循环，然后使用 Batch 运行 foreach。 此示例为简化版，可以通过模拟 5 年后的大量不同结果来预测股票价格。

假设 Contoso Corporation 的股票以开盘价为基础，每天的价格平均起来是上一天的 1.001 倍，但其波动性（标准方差）为 0.01。 假设起始价为 $100，使用 Monte Carlo 定价模拟来计算出 5 年后 Contoso 的股票价格。

Monte Carlo 模拟的参数：

```R
mean_change = 1.001 
volatility = 0.01 
opening_price = 100 
```

若要模拟收盘价，请定义以下函数：

```R
getClosingPrice <- function() { 
  days <- 1825 # ~ 5 years 
  movement <- rnorm(days, mean=mean_change, sd=volatility) 
  path <- cumprod(c(opening_price, movement)) 
  closingPrice <- path[days] 
  return(closingPrice) 
} 
```

首先，使用标准的 foreach 循环与 `%do%` 关键字在本地运行 10,000 次模拟：

```R
start_s <- Sys.time() 
# Run 10,000 simulations in series 
closingPrices_s <- foreach(i = 1:10, .combine='c') %do% { 
  replicate(1000, getClosingPrice()) 
} 
end_s <- Sys.time() 
```


绘制收盘价直方图，显示结果分布情况：

```R
hist(closingPrices_s)
``` 

输出与下面类似：

![收盘价分布情况](media/tutorial-r-doazureparallel/closing-prices-local.png)
  
一次本地模拟最多几秒钟即可完成：

```R
difftime(end_s, start_s) 
```

使用线性估算，1 千万个结果在本地的估计运行时间大约为 30 分钟：

```R 
1000 * difftime(end_s, start_s, unit = "min") 
```


现在使用 `foreach` 和 `%dopar%` 关键字运行代码，比较一下在 Azure 中运行 1 千万次模拟需要多长时间。 若要使用 Batch 进行并行模拟，请针对这 100,000 次模拟运行 100 次迭代：

```R
# Optimize runtime. Chunking allows running multiple iterations on a single R instance.
opt <- list(chunkSize = 10) 
start_p <- Sys.time()  
closingPrices_p <- foreach(i = 1:100, .combine='c', .options.azure = opt) %dopar% { 
  replicate(100000, getClosingPrice()) 
} 
end_p <- Sys.time() 
```

此模拟将任务分发到 Batch 池中的节点。 在 Azure 门户中，可以查看池的热度地图中的活动。 转到“Batch 帐户”   >   “myBatchAccount”。 单击“池”   >   “myPoolName”。 

![运行并行 R 任务的池的热度地图](media/tutorial-r-doazureparallel/pool.png)

几分钟后，模拟完成。 包会自动合并结果，并将它们从节点向下拉取。 然后，你就可以在 R 会话中使用结果。 

```R
hist(closingPrices_p) 
```

输出与下面类似：

![收盘价分布情况](media/tutorial-r-doazureparallel/closing-prices.png)

并行模拟耗时多久？ 

```R
difftime(end_p, start_p, unit = "min")  
```

可以看到，与在本地运行模拟预计需要的时间相比，在 Batch 池中运行模拟可以大幅提高性能。 

## <a name="clean-up-resources"></a>清理资源

作业在完成后自动删除。 如果不再需要群集，请调用 doAzureParallel 包中的 `stopCluster` 函数将其删除：

```R
stopCluster(cluster)
```

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> 安装 doAzureParallel 并将其配置为访问 Batch 帐户和存储帐户
> * 创建一个 Batch 池，作为 R 会话的并行后端
> * 在池中运行示例并行模拟


有关 doAzureParallel 的详细信息，请查看 GitHub 上的文档和示例。

> [!div class="nextstepaction"]
> [doAzureParallel 包](https://github.com/Azure/doAzureParallel/)




