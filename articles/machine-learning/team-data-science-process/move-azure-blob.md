---
title: 将数据移入和移出 Azure Blob 存储 - Team Data Science Process
description: 将数据移入和移出 Azure Blob 存储
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ca5a75ec61a0f75b3a008762561fed8231fce33d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453750"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>将数据移入和移出 Azure Blob 存储

团队数据科学过程要求引入或载入各种不同存储环境中的数据在过程的每个阶段中都以最合适的方式进行处理或分析。

## <a name="different-technologies-for-moving-data"></a>用于移动数据的不同技术

以下文章介绍了如何使用不同技术将数据移入和移出 Azure Blob 存储。

* [Azure 存储资源管理器](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](move-data-to-azure-blob-using-azcopy.md)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

最合适的方法取决于具体的方案。 [用于 Azure 机器学习中高级分析的方案](plan-sample-scenarios.md)有助于确定用于高级分析过程的各种数据科学工作流所需的资源。

> [!NOTE]
> 有关 Azure Blob 存储的完整介绍，请参阅 [Azure Blob 基本知识](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)和 [Azure Blob 服务](https://msdn.microsoft.com/library/azure/dd179376.aspx)。
> 
> 

## <a name="using-azure-data-factory"></a>使用 Azure 数据工厂

或者，可使用 [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)来执行以下操作： 

* 创建和计划从 Azure Blob 存储下载数据的管道， 
* 将其传递到已发布的 Azure 机器学习 Web 服务， 
* 接收预测分析结果，然后 
* 将结果上传到存储。 

有关详细信息，请参阅[使用 Azure 数据工厂和 Azure 机器学习创建预测管道](../../data-factory/transform-data-using-machine-learning.md)。

## <a name="prerequisites"></a>先决条件
本文假定已有 Azure 订阅、存储帐户，以及该帐户对应的存储密钥。 上传/下载数据之前，必须知道 Azure 存储帐户名和帐户密钥。

* 若要设置 Azure 订阅，请参阅[免费试用一个月版](https://azure.microsoft.com/pricing/free-trial/)。
* 有关创建存储帐户的说明和有关获取帐户和密钥的信息，请参阅[关于 Azure 存储帐户](../../storage/common/storage-create-storage-account.md)。

