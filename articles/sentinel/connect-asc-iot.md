---
title: 将 IoT 的 Azure 安全中心连接到 Azure 哨兵 |微软文档
description: 了解如何将 IoT 数据的 Azure 安全中心连接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 3af51110a4c4604444573f62be65077c786db606
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588631"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>将数据从 IoT 的 Azure 安全中心连接到 Azure 哨兵 


> [!IMPORTANT]
> IoT 数据连接器的 Azure 安全中心当前处于公共预览版中。 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 IoT 连接器的 Azure 安全中心将所有用于 IoT 事件的 Azure 安全中心流式传输到 Azure Sentinel 中。 

## <a name="prerequisites"></a>先决条件

- **对**Azure Sentinel 部署到的工作区的读取和**写入**权限
- **必须在相关 IoT**中心**上启用**IoT 的 Azure 安全中心
- 要连接的 Azure **IoT 中心**上的**读取**和**写入**权限
- **Azure** **IoT 中心资源组的**读取和**写入**权限

> [!NOTE]
> 虽然必须在订阅上启用 Azure 安全中心**标准**层许可证，以便将 IoT 资源警报流式传输到 Azure Sentinel，但只需在订阅中启用 Azure 安全中心**免费**层许可证，即查看 Azure Sentinel 中针对 IoT 警报的 Azure 安全中心。 

## <a name="connect-to-azure-security-center-for-iot"></a>连接到适用于 IoT 的 Azure 安全中心

1. 在 Azure 哨兵中，选择**数据连接器**，然后单击 IoT 的**Azure 安全中心**磁贴。
1. 从右下角窗格中，单击 **"打开连接器"页**。 
1. 单击 **"连接**"，单击每个 IoT 中心订阅旁边的，其警报和设备警报要流式传输到 Azure Sentinel 中。 
    - 如果未启用该集线器上的 IoT Azure 安全中心，您将看到**启用**警告消息。 单击 **"启用**"链接以启动服务。 
1. 您可以决定是否希望来自 IoT 的 Azure 安全中心的警报在 Azure Sentinel 中自动生成事件。 在 **"创建事件****"下，选择"启用**"以启用默认分析规则，以便从连接的安全服务中生成的警报自动创建事件。此规则可以在 **"分析** > **活动"** 规则下更改或编辑。

> [!NOTE]
> 在进行连接更改后，中心列表可能需要一些时间才能刷新。 

## <a name="log-analytics-alert-display"></a>日志分析警报显示

要使用日志分析中的相关架构来显示 IoT 警报的 Azure 安全中心：：

1. 打开**日志** > **安全见解** > **安全警报**， 或搜索**安全警报**. 
2. 筛选器，以便仅查看使用以下 kql 筛选器生成 IoT 警报的 Azure 安全中心：

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>服务说明

连接 IoT 中心后，中心数据大约在 15 分钟后在 Azure Sentinel 中可用。


## <a name="next-steps"></a>后续步骤

在本文档中，您学习了如何将 IoT 数据的 Azure 安全中心连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
