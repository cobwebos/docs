---
title: 将 Cloud App Security 数据连接到 Azure Sentinel Preview |Microsoft Docs
description: 了解如何将 Cloud App Security 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: rkarlin
ms.openlocfilehash: 92de4120d73af70c5de013488344a15ba46513c7
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881083"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>连接 Microsoft Cloud App Security 的数据 

> [!IMPORTANT]
> Azure Sentinel 当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

只需要单击一次, 即可将日志从[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)流式传输到 Azure Sentinel。 通过此连接, 你可以将警报从 Cloud App Security 流式传输到 Azure Sentinel。 

## <a name="prerequisites"></a>先决条件

- 具有全局管理员或安全管理员权限的用户

## <a name="connect-to-cloud-app-security"></a>连接到 Cloud App Security

如果已有 Cloud App Security, 请确保已[在网络上启用](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)它。
如果 Cloud App Security 已部署并引入数据, 则可以轻松地将警报数据流式传输到 Azure Sentinel。


1. 在 Azure Sentinel 中, 选择 "**数据连接器**", 然后单击 " **Cloud App Security** " 磁贴。

2. 单击“连接”  。

3. 若要在 Cloud App Security 警报 Log Analytics 中使用相关架构, 请搜索**SecurityAlert**。


## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Microsoft Cloud App Security 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
