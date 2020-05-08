---
title: Azure 流分析中的数据保护
description: 本文介绍如何加密 Azure 流分析作业使用的专用数据。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 8d201beb2ff4aba815749b12a506d2292779cb82
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857292"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Azure 流分析中的数据保护 

Azure 流分析是一种完全托管的平台即服务，可用于构建实时分析管道。 所有繁重的提升（如群集预配、调整节点以适应你的使用情况和管理内部检查点）都在幕后进行管理。

## <a name="encrypt-your-data"></a>加密数据

流分析自动在其基础结构中使用同类最佳的加密标准来加密和保护数据。 您可以简单地信任流分析来安全地存储所有数据，这样就无需担心如何管理基础结构。

如果要使用客户托管的密钥（CMK）对数据进行加密，可以使用自己的存储帐户（常规用途 V1 或 V2）来存储流分析运行时所需的任何专用数据资产。 可以根据需要加密存储帐户。 流分析基础结构不会永久存储任何专用数据资产。 

此设置必须在流分析作业创建时配置，并且不能在作业的整个生命周期内修改。 不建议修改或删除流分析正在使用的存储。 如果删除存储帐户，则将永久删除所有专用数据资产，这将导致作业失败。 

不能使用流分析门户将密钥更新或轮换到存储帐户。 可以使用 REST Api 更新密钥。


## <a name="configure-storage-account-for-private-data"></a>为专用数据配置存储帐户 

使用以下步骤配置专用数据资产的存储帐户。 此配置是从流分析作业而不是从存储帐户进行的。

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 在 Azure 门户的左上角选择“创建资源”。  

1. 从 "结果" 列表中选择 " **分析** > **流分析作业** "。 

1. 在 "流分析作业" 页中填写必要的详细信息，如名称、区域和缩放。 

1. 选中 "*保护我的存储帐户中此作业所需的所有专用数据资产*" 的复选框。

1. 从订阅中选择一个存储帐户。 请注意，在作业的整个生命周期内不能修改此设置。 

   ![专用数据存储帐户设置](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>存储的专用数据资产

流分析需要保存的任何专用数据存储在存储帐户中。 专用数据资产的示例包括： 

* 已创作的查询及其相关配置  

* 用户定义的函数 

* 流分析运行时所需的检查点

* 引用数据的快照 

还将存储你的流分析作业使用的资源的连接详细信息。 加密存储帐户以保护你的所有数据。 

为了帮助你满足任何管控行业或环境中的符合性义务，你可以阅读有关[Microsoft 的符合性产品/服务的](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)详细信息。 

## <a name="known-issues"></a>已知问题
存在一个已知问题，即使用客户托管密钥的作业在使用托管标识向任何输入或输出进行身份验证时出现故障。 正在处理此问题的修补程序，并将在不久的将来推出。 

## <a name="next-steps"></a>后续步骤

* [创建 Azure 存储帐户](../storage/common/storage-account-create.md)
* [理解 Azure 流分析的输入](stream-analytics-add-inputs.md)
* [Azure 流分析作业中的检查点和重播概念](stream-analytics-concepts-checkpoint-replay.md)
* [使用参考数据在流分析中查找](stream-analytics-use-reference-data.md)
