---
title: 在 Azure Sentinel Preview 中加入Microsoft Docs
description: 了解如何在 Azure Sentinel 中收集数据。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2019
ms.author: rkarlin
ms.openlocfilehash: 0c37d6167012af46204cbca29397f5d226b9649b
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69611900"
---
# <a name="on-board-azure-sentinel-preview"></a>机载 Azure Sentinel 预览

> [!IMPORTANT]
> Azure Sentinel 当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在本快速入门教程中, 你将了解如何在 Azure 上操作。 

若要使用板载 Azure Sentinel, 首先需要启用 Azure Sentinel, 然后连接数据源。 Azure Sentinel 附带了许多用于 Microsoft 解决方案的连接器, 可用于提供实时集成, 包括 Microsoft 威胁防护解决方案、Microsoft 365 源 (包括 Office 365、Azure AD、Azure ATP 和Microsoft Cloud App Security 等。 此外，内置的连接器可以拓宽非 Microsoft 解决方案的安全生态系统。 你还可以使用通用事件格式、Syslog 或 REST API 将你的数据源与 Azure Sentinel 连接起来。  

连接数据源后, 从熟练地创建的仪表板库中进行选择, 这些仪表板基于你的数据进行深入了解。 可以轻松地根据需要对这些仪表板进行定制。


## <a name="global-prerequisites"></a>全局必备组件

- 有效的 Azure 订阅: 如果没有订阅, 请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- Log Analytics 工作区。 了解如何[创建 Log Analytics 工作区](../log-analytics/log-analytics-quick-create-workspace.md)

-  若要启用 Azure Sentinel, 需要具有对 Azure Sentinel 工作区所在订阅的参与者权限。 
- 若要使用 Azure Sentinel, 需要对工作区所属的资源组具有 "参与者" 或 "读取者" 权限
- 连接特定数据源可能需要其他权限
 
## 启用 Azure Sentinel<a name="enable"></a>

1. 进入 Azure 门户。
2. 请确保已选择在其中创建 Azure Sentinel 的订阅。 
3. 搜索 Azure Sentinel。 
   ![search](./media/quickstart-onboard/search-product.png)

1. 单击“+添加”。
1. 选择要使用的工作区, 或创建一个新的工作区。 可以在多个工作区上运行 Azure Sentinel, 但将数据隔离到单个工作区。

   ![搜索](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Azure 安全中心创建的默认工作区将不会显示在列表中;无法在其上安装 Azure Sentinel。
   > - Azure Sentinel 可以在部署在以下任何区域中的工作区上运行:澳大利亚东南部、加拿大中部、印度中部、美国东部、美国东部 2 EUAP (未)、日本东部、东南亚、英国南部、西欧、美国西部2。

6. 单击 "**添加 Azure Sentinel**"。
  

## <a name="connect-data-sources"></a>连接数据源

Azure Sentinel 通过连接到服务并将事件和日志转发到 Azure Sentinel 来创建与服务和应用的连接。 对于计算机和虚拟机, 可以安装用于收集日志并将其转发到 Azure Sentinel 的 Azure Sentinel 代理。 对于防火墙和代理, Azure Sentinel 利用 Linux Syslog 服务器。 代理安装在其上, 代理从中收集日志文件并将其转发到 Azure Sentinel。 
 
1. 单击 "**数据收集**"。
2. 可以连接的每个数据源都有一个磁贴。<br>
例如, 单击 " **Azure Active Directory**"。 如果连接此数据源, 则会将 Azure AD 中的所有日志流式传输到 Azure Sentinel。 你可以选择 wan 用于获取登录日志和/或审核日志的日志类型。 <br>
在底部, Azure Sentinel 提供了有关应为每个连接器安装哪些仪表板的建议, 以便您可以立即获取数据中的有趣见解。 <br> 按照安装说明进行操作, 或[参阅相关的连接指南](connect-data-sources.md)以获取详细信息。 有关数据连接器的信息, 请参阅[连接 Microsoft 服务](connect-data-sources.md)。

连接数据源后, 数据开始流式传输到 Azure Sentinel, 并准备好开始使用。 您可以查看[内置仪表板](quickstart-get-visibility.md)中的日志, 并开始在 Log Analytics 中生成查询来[调查数据](tutorial-investigate-cases.md)。



## <a name="next-steps"></a>后续步骤
本文档介绍了如何将数据源连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
- 将数据从[常见错误格式设备](connect-common-event-format.md)流式传输到 Azure Sentinel。
