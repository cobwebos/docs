---
title: 将云应用安全数据连接到 Azure 哨兵*微软文档
description: 了解如何将云应用安全数据连接到 Azure 哨兵。
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
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 266d97e834247088d40837cbec1436e00d0f4be2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422145"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>连接来自微软云应用安全的数据 



[Microsoft 云应用安全](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)（MCAS） 连接器允许您将警报和[云发现日志](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)从 MCAS 流式传输到 Azure Sentinel 中。 这将使您能够深入了解云应用，获得复杂的分析，以识别和打击网络威胁，并控制数据传输方式。

## <a name="prerequisites"></a>先决条件

- 您的用户必须具有工作区的读取和写入权限。
- 您的用户必须对工作区的租户具有全局管理员或安全管理员权限。
- 要将云发现日志流式传输到 Azure Sentinel 中，[请启用 Azure Sentinel 作为 Microsoft 云应用安全中的 SIEM。](https://aka.ms/AzureSentinelMCAS)

> [!IMPORTANT]
> 云发现日志的引入当前处于公共预览版中。
> 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
## <a name="connect-to-cloud-app-security"></a>连接到 Cloud App Security

如果您已经拥有云应用安全性，请确保它在[您的网络上已启用](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)。
如果部署云应用安全性并引入数据，则警报数据可以轻松地流式传输到 Azure Sentinel 中。


1. 在 Azure Sentinel 导航菜单中，选择 **"数据连接器**"。 从连接器列表中，单击 Microsoft**云应用安全**磁贴，然后单击右下角的 **"打开连接器"页面**按钮。

1. 选择要流式传输到 Azure Sentinel 的日志;您可以选择 **"警报**"和"**云发现日志**"（预览）。 

1. 单击“应用更改”****。

1. 要在日志分析中使用云应用安全警报的日志分析中的相关架构`SecurityAlert`，请键入查询窗口。 对于云发现日志架构，键入`McasShadowItReporting`。

> [!NOTE]
> 云发现通过聚合用户与云应用连接的基础数据，帮助检测和识别趋势。
>
> 由于云发现数据是每天聚合的，因此请注意，Azure Sentinel 中不会反映最多 24 小时的最新数据。 如果低级调查需要更直接的数据，则应直接在原始数据所在的源设备或服务中进行。

## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 Microsoft 云应用安全性连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 使用[内置](tutorial-detect-threats.md)或[自定义](tutorial-detect-threats-custom.md)规则开始使用 Azure Sentinel 检测威胁。
