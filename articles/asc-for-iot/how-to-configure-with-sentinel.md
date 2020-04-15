---
title: 配置哨兵（预览）
description: 说明如何配置 Azure Sentinel 以从 Azure 安全中心接收 IoT 解决方案的数据。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: fe8e4c1b08f96e5f6b2fc7649f7a4361616b7c87
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311314"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>将数据从 IoT 的 Azure 安全中心连接到 Azure 哨兵（预览）

> [!IMPORTANT]
> Azure Sentinel 中 IoT 数据连接器的 Azure 安全中心当前处于公共预览版中。
> 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在本指南中，了解如何将 IoT 数据的 Azure 安全中心连接到 Azure Sentinel。

> [!div class="checklist"]
> * 先决条件
> * 连接设置
> * 日志分析警报视图

连接来自 IoT 的 Azure 安全中心的警报，并将它们直接流式传输到 Azure Sentinel 中。

## <a name="prerequisites"></a>先决条件

- 您必须具有工作区**读取**和**写入**权限。
- **必须在相关 IoT**中心上**启用**IoT 的 Azure 安全中心。
- 您必须同时具有要连接的**Azure IoT 中心的****读取**和**写入**权限。
- 还必须对**Azure IoT 中心资源组**具有**读取**和**写入**权限。

> [!NOTE]
> 您必须在订阅上运行 Azure 安全中心标准层许可才能发送常规 Azure 资源警报。 由于 IoT 的 Azure 安全中心需要免费的层许可，因此只有针对 IoT 相关警报的 Azure 安全中心将转发到 Azure Sentinel。

## <a name="connect-to-azure-security-center-for-iot"></a>连接到适用于 IoT 的 Azure 安全中心

1. 在 Azure 哨兵中，选择**数据连接器**，然后单击 IoT 的**Azure 安全中心**磁贴。
1. 从右侧窗格的底部，单击 **"打开连接器"页**。
1. 单击 **"连接**"，单击每个 IoT 中心订阅旁边的，其警报和设备警报要流式传输到 Azure Sentinel 中。
    - 如果未在该集线器上启用 IoT 的 Azure 安全中心，您将看到启用警告消息。 单击 **"启用**"链接以启动和启用服务。
1. 您可以决定是否希望来自 IoT 的 Azure 安全中心的警报在 Azure Sentinel 中自动生成事件。 在 **"创建事件****"下，选择"启用**"以使规则能够自动从生成的警报创建事件。  此规则可以在 **"分析** > **活动"** 规则下更改或编辑。

> [!NOTE]
>更改连接后，刷新中心列表可能需要 10 秒或更长时间。

## <a name="log-analytics-alert-display"></a>日志分析警报显示

要使用日志分析中的相关架构来显示 IoT 警报的 Azure 安全中心：：

1. 打开**日志** > **安全见解** > **安全警报**， 或搜索**安全警报**.
1. 筛选器，以便仅查看使用以下 kql 筛选器生成 IoT 警报的 Azure 安全中心：

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>服务说明

连接 IoT 中心后，中心数据大约在 15 分钟后在 Azure Sentinel 中可用。

## <a name="next-steps"></a>后续步骤

在本文档中，您学习了如何将 IoT 的 Azure 安全中心连接到 Azure Sentinel。 要了解有关威胁检测和安全数据访问的更多详细信息，请参阅以下文章：

- 了解如何使用 Azure Sentinel[来查看数据以及潜在威胁](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)。

- 了解如何访问[IoT 安全数据](how-to-security-data-access.md)
