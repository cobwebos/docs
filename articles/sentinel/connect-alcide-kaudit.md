---
title: 将 Alcide kAudit 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Alcide kAudit 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: cf8da1d88529a823ff4399fb955c8a5e0abbd20e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87038233"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>将 Alcide kAudit 连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Alcide kAudit 数据连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) 可帮助识别异常 Kubernetes 行为，并关注 Kubernetes 违规和事件，同时缩短检测时间。 本文介绍了如何将 Alcide kAudit 解决方案连接到 Azure Sentinel。 Alcide kAudit 数据连接器可让你轻松地将 kAudit 日志数据引入 Azure Sentinel，使你能够在工作簿中查看它、使用它创建自定义警报，并将其合并以改进调查。 Alcide kAudit 与 Azure Sentinel 之间的集成利用 REST API。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 你必须具有 Azure Sentinel 工作区的读取和写入权限。

- 您必须对工作区的共享密钥具有读取权限。

## <a name="configure-and-connect-alcide-kaudit"></a>配置并连接 Alcide kAudit

Alcide kAudit 可以将日志直接导出到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击导航菜单上的 " **数据连接器** "。

1. 从库中选择 " **Alcide kAudit** "，然后单击 " **打开连接器页** " 按钮。

1. 按照 [Alcide KAudit 安装指南](https://get.alcide.io/hubfs/Azure%20Sentinel%20Integration%20with%20kAudit.pdf)中提供的分步说明进行操作。

1. 当要求提供工作区 ID 和主密钥时，可以从 Alcide kAudit 数据连接器页复制它们。

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="工作区 ID 和主键":::

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据会显示在**CustomLogs**中以下数据类型下的**日志**中：

- **alcide_kaudit_detections_1_CL** alcide kaudit 检测 
- **alcide_kaudit_activity_1_CL** alcide kaudit 活动日志
- **alcide_kaudit_selections_count_1_CL** -alcide kaudit 活动计数
- **alcide_kaudit_selections_details_1_CL** alcide kaudit 活动详细信息

若要在 Alcide kAudit 的日志中使用相关架构，请搜索上面提到的数据类型。

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Alcide kAudit 连接到 Azure Sentinel。 若要充分利用此数据连接器内置的功能，请单击 "数据连接器" 页上的 " **后续步骤** " 选项卡。 您可以在其中找到一些现成的示例查询，以便您可以开始查找有用的信息。

要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
