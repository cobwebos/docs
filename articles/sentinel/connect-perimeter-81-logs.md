---
title: 将外围81数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将外围81数据连接到 Azure Sentinel。
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
ms.openlocfilehash: 8c9a7a09d3085b1ec67bf29f142c6e7b205561ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87021743"
---
# <a name="connect-your-perimeter-81-activity-logs-to-azure-sentinel"></a>将外围81活动日志连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的外围81数据连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍如何将 [外围81活动日志](https://www.perimeter81.com/) 设备连接到 Azure Sentinel。 外围81活动日志连接器可让你轻松地将外围81数据引入 Azure Sentinel，使你能够在工作簿中查看它、使用它创建自定义警报，并将其合并以改进调查。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 你必须具有 Azure Sentinel 工作区的读取和写入权限。

- 您必须对工作区的共享密钥具有读取权限。

## <a name="configure-and-connect-perimeter-81-activity-logs"></a>配置和连接外围81活动日志

外围81活动日志可以将日志直接集成到 Azure Sentinel 并将其导出。

1. 在 Azure Sentinel 门户中，单击导航菜单上的 " **数据连接器** "。

1. 从库中选择 " **外围81活动日志** "，然后单击 " **打开连接器页** " 按钮。

1. 在 "外围81活动日志连接器" 页面上，复制 " **工作区 ID** **" 和 "主密钥"** 并将其粘贴到外围81， [如此处所示](https://support.perimeter81.com/hc/en-us/articles/360012680780)。

1. 完成说明后，可以在 Azure Sentinel 连接器页中看到连接的数据类型。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据会显示在 " **CustomLogs**Perimeter81_CL 下的"**日志**"中  -  **Perimeter81_CL**。

可能需要长达20分钟的时间才能开始显示日志。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将外围81活动日志连接到 Azure Sentinel。 若要充分利用此数据连接器内置的功能，请单击 "数据连接器" 页上的 " **后续步骤** " 选项卡。 您可以在其中找到现成的工作簿和一些示例查询，以便您可以开始查找有用的信息。

要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
