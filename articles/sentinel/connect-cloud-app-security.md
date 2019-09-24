---
title: 将 Cloud App Security 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Cloud App Security 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6e71028855344d083dedf3493682e1e27685de48
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240122"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>连接 Microsoft Cloud App Security 的数据 



只需要单击一次, 即可将日志从[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)流式传输到 Azure Sentinel。 通过此连接, 你可以将警报从 Cloud App Security 流式传输到 Azure Sentinel。 

## <a name="prerequisites"></a>先决条件

- 具有全局管理员或安全管理员权限的用户

## <a name="connect-to-cloud-app-security"></a>连接到 Cloud App Security

如果已有 Cloud App Security, 请确保已[在网络上启用](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)它。
如果 Cloud App Security 已部署并引入数据, 则可以轻松地将警报数据流式传输到 Azure Sentinel。


1. 在 Azure Sentinel 中, 选择 "**数据连接器**", 然后单击 " **Cloud App Security** " 磁贴。

1. 选择要流式传输到 Azure Sentinel 的日志，可以选择 "**警报**"。 

1. 你可以选择是否希望警报 Microsoft Cloud App Security 自动在 Azure Sentinel 中自动生成事件。 在 "**创建事件**" 下，选择 "**启用**" 以启用从连接的安全服务中生成的警报自动创建事件的默认分析规则。 然后，你可以在 "**分析**" 和 "**活动规则**" 下编辑此规则。

1. 单击“连接”。

1. 若要在 Cloud App Security 警报 Log Analytics 中使用相关架构, 请搜索**SecurityAlert**。




## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Microsoft Cloud App Security 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
