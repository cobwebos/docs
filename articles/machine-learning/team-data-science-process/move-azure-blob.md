---
title: 将数据移入和移出 Azure Blob 存储 | Microsoft Docs
description: 将数据移入和移出 Azure Blob 存储
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: d6681e30-ab45-45ea-a9fb-ac8acefe544d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 424216226ca4b92f17cec78dadbb0643defa8d0c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>将数据移入和移出 Azure Blob 存储
[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

<!-- just in case, adding this to separate these two include references -->

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

最合适的方法取决于具体的方案。 [用于 Azure 机器学习中高级分析的方案](plan-sample-scenarios.md)有助于确定用于高级分析过程的各种数据科学工作流所需的资源。

> [!NOTE]
> 有关 Azure Blob 存储的完整介绍，请参阅 [Azure Blob 基本知识](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)和 [Azure Blob 服务](https://msdn.microsoft.com/library/azure/dd179376.aspx)。
> 
> 

或者，可使用 [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)来执行以下操作： 

* 创建和计划从 Azure Blob 存储下载数据的管道， 
* 将其传递到已发布的 Azure 机器学习 Web 服务， 
* 接收预测分析结果，然后 
* 将结果上传到存储。 

有关详细信息，请参阅[使用 Azure 数据工厂和 Azure 机器学习创建预测管道](../../data-factory/v1/data-factory-azure-ml-batch-execution-activity.md)。

## <a name="prerequisites"></a>先决条件
本文档假定已有 Azure 订阅、存储帐户，以及该帐户对应的存储密钥。 上传/下载数据之前，必须知道 Azure 存储帐户名和帐户密钥。

* 若要设置 Azure 订阅，请参阅[免费试用一个月版](https://azure.microsoft.com/pricing/free-trial/)。
* 有关创建存储帐户的说明和有关获取帐户和密钥的信息，请参阅[关于 Azure 存储帐户](../../storage/common/storage-create-storage-account.md)。

