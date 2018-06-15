---
title: 机器学习批处理执行服务作业的专用容量 | Microsoft Docs
description: 适用于机器学习作业的 Azure Batch 服务概述。
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.service: machine-learning
ms.component: studio
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 1e4cf34582e28e00108e280d928eea8a8134699a
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834519"
---
# <a name="azure-batch-service-for-machine-learning-jobs"></a>适用于机器学习作业的 Azure Batch 服务

机器学习批处理池处理为 Azure 机器学习批处理执行服务提供客户管理的缩放。 适用于机器学习的经典批处理发生在多租户环境中，限制了可以提交的并发作业的数目，作业按先进先出的原则排队处理。 这种不确定性意味着无法准确地预测作业何时会运行。

可以通过批处理池处理创建池，在其中提交批处理作业。 可以控制池的大小以及作业所提交到的具体池。 BES 作业在自己的处理空间运行，因此处理性能可以预测，并且可以创建与所提交的处理负载相对应的资源池。

## <a name="how-to-use-batch-pool-processing"></a>如何使用批处理池处理

当前不可通过 Azure 门户对批处理池处理进行配置。 若要使用批处理池处理，必须：

-   调用 CSS 来创建批处理池帐户，并获取池服务 URL 和授权密钥
-   创建基于新 Resource Manager 的 Web 服务和计费计划

若要创建帐户，请致电 Microsoft 客户服务和支持 (CSS) 并提供订阅 ID。 CSS 会与你一起确定适合你方案的容量。 然后，CSS 会使用可以创建的最大数目的池以及可以置于每个池中的最大数目的虚拟机 (VM) 配置帐户。 配置帐户以后，会提供池服务 URL 和授权密钥。

帐户创建好以后，即可使用池服务 URL 和授权密钥对批处理池执行池管理操作。

![批处理池服务体系结构。](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

可以针对 CSS 提供给池服务 URL 调用“创建池”操作来创建池。 创建池时，请指定 VM 的数目以及 swagger.json（属于基于新建 Resource Manager 的机器学习 Web 服务）的 URL。 提供此 Web 服务是为了进行计费关联。 批处理池服务使用 swagger.json 将池与计费计划相关联。 可以运行在池中选择的任何 BES Web 服务，包括基于新建 Resource Manager 的服务和经典服务。

可以使用任何基于新建 Resource Manager 的 Web 服务，但请注意，作业的计费取决于与该服务关联的计费计划。 可能需要特意为运行批处理池作业而创建 Web 服务和新的计费计划。

有关如何创建 Web 服务的详细信息，请参阅[部署 Azure 机器学习 Web 服务](publish-a-machine-learning-web-service.md)。

创建池后，即可使用 Web 服务的批处理请求 URL 提交 BES 作业。 可以选择将作业提交到池，或者提交到经典批处理。 要将作业提交到批处理池处理，请将以下参数添加到作业提交请求正文：

"AzureBatchPoolId":"&lt;池 ID&gt;"

如果不添加该参数，作业会在经典批处理环境中运行。 如果该池有可用资源，作业会立即开始运行。 如果该池没有可用资源，则会将作业排队，直至有资源可用。

如果发现自己经常达到池的容量限制，因而需要增大容量，则可致电 CSS，客户服务代表会帮助你提高配额。

示例请求：

https://ussouthcentral.services.azureml.net/subscriptions/80c77c7674ba4c8c82294c3b2957990c/services/9fe659022c9747e3b9b7b923c3830623/jobs?api-version=2.0

```json
{

    "Input":{
    
        "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpoint
        =https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://zhguim
        l.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;;",
        
        "BaseLocation":null,
        
        "RelativeLocation":"testint/AdultCensusIncomeBinaryClassificationDataset.csv",
        
        "SasBlobToken":null
    
    },
    
    "GlobalParameters":{ },
    
    "Outputs":{
    
        "output1":{
        
            "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpo
            int=https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://sampleaccount.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;",
            "BaseLocation":null,
            "RelativeLocation":"testintoutput/testint\_results.csv",
            
            "SasBlobToken":null
        
        }
    
    },
    
    "AzureBatchPoolId":"8dfc151b0d3e446497b845f3b29ef53b"

}
```

## <a name="considerations-when-using-batch-pool-processing"></a>使用批处理池处理时的注意事项

批处理池处理是一项始终开启的计费服务，需要将其与基于 Resource Manager 的计费计划相关联。 计费基于运行池时的计算小时数，与该段时间在池中运行的作业数无关。 如果创建了池，则在删除池之前，系统会针对池中每个虚拟机的计算小时数计费，即使池中没有批处理作业运行。 对虚拟机的计费在虚拟机完成预配后开始，在删除虚拟机后停止。 可以使用[机器学习定价页](https://azure.microsoft.com/pricing/details/machine-learning/)中提供的任何计划。

计费示例：

如果创建了一个包含 2 个虚拟机的批处理池，在 24 小时后将该池删除，则计费计划会将计算小时数定为 48 小时，不管在该时段内运行了多少作业。

如果创建了一个包含 4 个虚拟机的批处理池，在 12 小时后将该池删除，则计费计划也会将计算小时数定为 48 小时。

建议通过轮询作业状态来确定作业完成时间。 所有作业都运行完以后，请调用“调整池大小”操作，将池中的虚拟机数设置为零。 如果池资源不足且因某种目的（例如，需要更改计费计划）而需要创建新池，则可在所有作业都运行完以后删除该池。


| **使用批处理池处理的时机**    | **使用经典批处理的时机**  |
|---|---|
|需要运行大量作业<br>或<br/>需要确定作业将立即运行<br/>或<br/>需要吞吐量保证。 例如，需要在给定时段内运行多项作业，且需按需求扩大计算资源。    | 只运行数项作业<br/>And<br/> 不需立即运行作业 |
