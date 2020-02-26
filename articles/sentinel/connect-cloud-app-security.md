---
title: 将 Cloud App Security 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Cloud App Security 数据连接到 Azure Sentinel。
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
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588359"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>连接 Microsoft Cloud App Security 的数据 



只需要单击一次，即可将日志从[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)流式传输到 Azure Sentinel。 通过此连接，你可以将警报从 Cloud App Security 流式传输到 Azure Sentinel。 

## <a name="prerequisites"></a>必备条件

- 具有全局管理员或安全管理员权限的用户
- 若要将 Cloud Discovery 日志流式传输到 Azure Sentinel，请[在 Microsoft Cloud App Security 中启用 Azure sentinel 作为你的 SIEM](https://aka.ms/AzureSentinelMCAS)。

> [!IMPORTANT]
> 引入 Cloud Discovery 日志当前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
## <a name="connect-to-cloud-app-security"></a>连接到 Cloud App Security

如果已有 Cloud App Security，请确保已[在网络上启用](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)它。
如果 Cloud App Security 已部署并引入数据，则可以轻松地将警报数据流式传输到 Azure Sentinel。


1. 在 Azure Sentinel 中，选择 "**数据连接器**"，单击 " **Cloud App Security** " 磁贴，然后选择 "**打开连接器" 页面**。

1. 选择要流式传输到 Azure Sentinel 的日志，可以选择 "**警报**" 和 " **Cloud Discovery 日志**（预览）"。 

1. 单击“连接”。

1. 若要在 Cloud App Security 警报 Log Analytics 中使用相关架构，请搜索**SecurityAlert**。




## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Microsoft Cloud App Security 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
