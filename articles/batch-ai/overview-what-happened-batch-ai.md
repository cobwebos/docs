---
title: Azure Batch AI 将发生什么情况？ | Microsoft Docs
description: 了解 Azure Batch AI 和 Azure 机器学习服务计算选项发生的情况。
ms.service: batch-ai
services: batch-ai
ms.topic: overview
ms.author: jmartens
author: j-martens
ms.date: 2/28/2019
ms.openlocfilehash: edd6a7e5f385d766d51631d77f5889233af2469a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194496"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Azure Batch AI 将发生什么情况？

Azure Batch AI 服务即将在 3 月停用。 Batch AI 的大规模培训和评分功能现在可在 [Azure 机器学习服务](../machine-learning/service/overview-what-is-azure-ml.md)中获取，该服务已于 2018 年 12 月 4 日正式发布。

除了许多其他机器学习功能外，Azure 机器学习服务还包括基于云的托管计算目标，用于培训、部署和对机器学习模型评分。 此计算目标称为 [Azure 机器学习计算](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute)。 [开始迁移并立即使用](#migrate)。 可以通过 Azure 机器学习服务 [Python SDK](../machine-learning/service/quickstart-create-workspace-with-python.md)、命令行界面和 [Azure 门户](../machine-learning/service/quickstart-get-started.md)与 Azure 机器学习服务进行交互。

## <a name="support-timeline"></a>支持时间线

现在可以像以前一样继续使用现有的 Azure Batch AI 订阅。 但是，无法注册**新的订阅**，并且我们不会对此服务进行新的投资。

从 2019 年 3 月 31 日开始，未使用的 Batch AI 订阅不再有效。

## <a name="compare-to-azure-machine-learning"></a>与 Azure 机器学习比较
Azure 机器学习服务是一项云服务，可以使用它来训练、部署、自动执行以及管理机器学习模型，所有这些都是在云提供的广泛范围内进行的。 [在此概述文章中大致了解 Azure 机器学习服务](../machine-learning/service/overview-what-is-azure-ml.md)。
 

典型的模型开发生命周期涉及到数据准备、训练和试验以及部署阶段。 可以使用机器学习管道协调此端到端周期。

![流程图](./media/overview-what-happened-batch-ai/lifecycle.png)


[详细了解该服务的工作原理及其主要概念](../machine-learning/service/concept-azure-machine-learning-architecture.md)。 模型训练工作流中的许多概念类似于 Batch AI 中的现有概念。 

具体而言，下面是两者之间的对应关系：
 
|Batch AI 服务|  Azure 机器学习服务|
|:--:|:---:|
|工作区|工作区|
|群集|   `AmlCompute` 类型的计算|
|文件服务器|数据存储|
|试验|试验|
|作业|运行（允许嵌套运行）|
 
下面是上表的另一个视图，可帮助你直观地进一步了解这两个服务：
 
### <a name="batch-ai-hierarchy"></a>Batch AI 层次结构
![流程图](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
### <a name="azure-machine-learning-service-hierarchy"></a>Azure 机器学习服务层次结构
![流程图](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

## <a name="platform-capabilities"></a>平台功能
Azure 机器学习服务引入了一套强大的新功能，包括从训练到堆栈部署的端到端流程，可用于进行 AI 开发，而无需管理任何 Azure 资源。 下表比较了这两个服务的训练功能支持。

|Feature|Batch AI 服务|Azure 机器学习服务|
|-------|:-------:|:-------:|
|VM 大小选项 |CPU/GPU    |CPU/GPU。 此外支持用于推断的 FPGA|
|AI 就绪的群集（驱动程序、Docker 等等。）|  是 |是|
|节点准备| 是|    否|
|OS 系列选项   |部分    |否|
|专用和低优先级 VM  |是    |是|
|自动缩放   |是    |是（默认支持）|
|自动缩放等待时间  |否 |是|
|SSH    |是|   是|
|群集级装载 |是（文件共享、Blob、NFS、自定义）   |是（装载或下载数据存储）|
|分布式训练|  是 |是|
|作业执行模式|    VM 或容器|    容器|
|自定义容器映像|    是 |是|
|任何工具包    |是    |是（运行 Python 脚本）|
|作业准备|    是 |尚不支持|
|作业级装载 |是（文件共享、Blob、NFS、自定义）   |是（文件共享、Blob）|
|作业监视     |通过 GetJob|    通过运行历史记录（更丰富的信息、自定义运行时可推送更多指标）|
|检索作业日志和文件/模型 |   通过列表文件和存储 API  |通过项目服务|
 |Tensorboard 支持   |否|    是|
|VM 系列级配额 |是    |是（使用以前余留的容量）|
 
除了上表中列出的功能以外，Azure 机器学习服务中的某些功能在传统上不受 Batch AI 的支持。

|Feature|Batch AI 服务|Azure 机器学习服务|
|-------|:-------:|:-------:|
|环境准备    |否 |是（Conda 准备和上传到 ACR）|
|超参数优化  |否|    是|
|模型管理   |否 |是|
|操作化/部署| 否  |通过 AKS 和 ACI|
|数据准备   |否 |是|
|计算目标    |Azure VM  |本地、Batch AI（作为 AmlCompute）、DataBricks、HDInsight|
|自动化机器学习 |否|    是|
|管道  |否 |是|
|批量评分  |是    |是|
|门户/CLI 支持|    是 |是|


## <a name="programming-interfaces"></a>编程接口

下表列出了适用于每个服务的各种编程接口。
    
|Feature|Batch AI 服务|Azure 机器学习服务|
|-------|:-------:|:-------:|
|SDK 中 IsInRole 中的声明    |Java、C#、Python、Nodejs   |Python（两个服务都对常用框架运行基于配置和基于估算器的接口）|
|CLI    |是    |尚不支持|
|Azure 门户   |是    |是（作业提交除外）|
|REST API   |是    |是，但跨微服务分布|


从 Batch AI 预览版升级到 Azure 机器学习服务正式版后，可以通过估算器和数据存储等更易用的概念来获得更好的体验。 此外，还能保证获得正式版 Azure 服务的 SLA 保障和客户支持。

Azure 机器学习服务还引入了自动化机器学习、超参数优化和机器学习管道等新功能，在大部分的大规模 AI 工作负荷中，这些功能非常有用。 无需切换到单独的服务即可部署训练的模型，这有助于完成整个数据科学循环：从准备数据（使用数据准备 SDK），到操作化和模型监视。

<a name="migrate"></a>
## <a name="migrate"></a>迁移

在[迁移到 Azure 机器学习服务](how-to-migrate.md)一文中了解如何进行迁移，以及如何将使用的代码映射到 Azure 机器学习服务中的代码。

## <a name="get-support"></a>获取支持

我们已计划在 3 月 31 日停用 Batch AI，并且已开始阻止针对该服务注册新的订阅，除非通过支持部门提出例外处理，将相应订阅列入允许列表。  如有任何疑问，或者在迁移到 Azure 机器学习服务的过程中想要提供反馈，请通过 [Azure Batch AI 训练预览版](mailto:AzureBatchAITrainingPreview@service.microsoft.com)联系我们。

Azure 机器学习服务现已推出正式版。 这意味着，它附带了经过承诺的 SLA，并且有多种支持计划可供选择。

通过 Batch AI 服务或 Azure 机器学习服务使用 Azure 基础结构的价格应该是相同的，因为在这两种情况下，我们只收取基础计算的费用。 有关详细详细，请参阅[定价计算器](https://azure.microsoft.com/pricing/details/machine-learning-service/)。

在 [Azure 门户](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all)中查看这两个服务的可用区域。


## <a name="next-steps"></a>后续步骤

+ 了解[如何进行迁移](how-to-migrate.md)，以及如何将现在使用的代码映射到 Azure 机器学习服务中的代码。

+ 请阅读 [Azure 机器学习服务概述](../machine-learning/service/overview-what-is-azure-ml.md)。

+ 请使用 Azure 机器学习服务[为模型定型配置计算目标](../machine-learning/service/how-to-set-up-training-targets.md)。

+ 请查看 [Azure 路线图](https://azure.microsoft.com/updates/)了解其他 Azure 服务更新。
