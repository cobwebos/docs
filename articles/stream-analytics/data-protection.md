---
title: Azure 流分析中的数据保护
description: 本文介绍如何加密 Azure 流分析作业使用的私有数据。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 1b3bdad0125b5bddbba20c8d807924fc3ea87e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299390"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Azure 流分析中的数据保护 

Azure 流分析是一个完全托管的平台即服务，允许您构建实时分析管道。 所有繁重的工作（如群集预配、缩放节点以适应您的使用情况和管理内部检查点）都在幕后进行管理。

## <a name="encrypt-your-data"></a>对数据进行加密

流分析可在其基础架构中自动采用一流的加密标准来加密和保护您的数据。 您可以简单地信任流分析安全地存储所有数据，这样您就不必担心管理基础结构。

如果要使用客户管理的密钥 （CMK） 加密数据，可以使用自己的存储帐户（通用 V1 或 V2）来存储流分析运行时所需的任何私有数据资产。 您的存储帐户可以根据需要进行加密。 流分析基础结构不会永久存储任何私有数据资产。 

此设置必须在创建流分析作业时进行配置，并且不能在整个作业生命周期中对其进行修改。 不建议修改或删除流分析正在使用的存储。 如果删除存储帐户，将永久删除所有私有数据资产，这将导致作业失败。 

无法使用流分析门户更新或旋转存储帐户的密钥。 您可以使用 REST API 更新密钥。


## <a name="configure-storage-account-for-private-data"></a>为私有数据配置存储帐户 

使用以下步骤为专用数据资产配置存储帐户。 此配置来自流分析作业，而不是来自存储帐户。

1. 登录到 Azure[门户](https://portal.azure.com/)。

1. 在 Azure 门户的左上角选择“创建资源”。**** 

1. 从结果列表中选择 **分析** > **流分析作业** 。 

1. 填写"流分析"作业页，并填写名称、区域和缩放等必要详细信息。 

1. 选中"*保护存储帐户中此作业所需的所有私有数据资产"复选框*。

1. 从订阅中选择存储帐户。 请注意，无法在整个作业的整个生命周期中修改此设置。 

   ![专用数据存储帐户设置](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>存储的私有数据资产

流分析需要保留的任何私有数据都存储在您的存储帐户中。 私有数据资产的示例包括： 

* 您创作的查询及其相关配置  

* 用户定义的函数 

* 流分析运行时所需的检查点

* 参考数据的快照 

资源的连接详细信息（由流分析作业使用）也会存储。 加密存储帐户以保护所有数据。 

为了帮助您在任何受监管的行业或环境中履行合规义务，您可以阅读更多有关[Microsoft 合规产品的产品](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)。 

## <a name="next-steps"></a>后续步骤

* [创建 Azure 存储帐户](../storage/common/storage-account-create.md)
* [理解 Azure 流分析的输入](stream-analytics-add-inputs.md)
* [Azure 流分析作业中的检查点和重播概念](stream-analytics-concepts-checkpoint-replay.md)
* [使用参考数据在流分析中查找](stream-analytics-use-reference-data.md)
