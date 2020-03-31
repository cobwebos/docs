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
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588359"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>连接来自微软云应用安全的数据 



只需单击一下，即可将日志从[云应用安全流式传输](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)到 Azure Sentinel 中。 此连接使您能够将警报从云应用安全流式传输到 Azure Sentinel 中。 

## <a name="prerequisites"></a>先决条件

- 具有全局管理员或安全管理员权限的用户
- 要将云发现日志流式传输到 Azure Sentinel 中，[请启用 Azure Sentinel 作为 Microsoft 云应用安全中的 SIEM。](https://aka.ms/AzureSentinelMCAS)

> [!IMPORTANT]
> 云发现日志的引入当前处于公共预览版中。
> 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
## <a name="connect-to-cloud-app-security"></a>连接到 Cloud App Security

如果您已经拥有云应用安全性，请确保它在[您的网络上已启用](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)。
如果部署云应用安全性并引入数据，则警报数据可以轻松地流式传输到 Azure Sentinel 中。


1. 在 Azure 哨兵中，选择 **"数据连接器**"，单击 **"云应用安全**"磁贴并选择 **"打开连接器"页**。

1. 选择要流式传输到 Azure Sentinel 的日志，您可以选择 **"警报**"和"**云发现"日志**（预览）。 

1. 单击“连接”。

1. 要在日志分析中为云应用安全警报使用相关架构，请搜索**安全警报**。




## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 Microsoft 云应用安全性连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
