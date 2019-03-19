---
title: 收集 Azure Sentinel 预览版中的 Cloud App Security 数据 |Microsoft Docs
description: 了解如何收集在 Azure Sentinel Cloud App Security 数据。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b0033f5f8636053f88825541b8b2cfcbf2fc9f8b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245482"
---
# <a name="collect-data-from-microsoft-cloud-app-security"></a>从 Microsoft Cloud App Security 中收集数据 

> [!IMPORTANT]
> Azure Sentinel 目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可以从日志流式传输[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)到 Azure Sentinel 单击一次。 此连接，可从 Cloud App Security 警报流到 Azure Sentinel。 

## <a name="prerequisites"></a>必备组件

- 具有全局管理员或安全管理员权限的用户

## <a name="connect-to-cloud-app-security"></a>连接到 Cloud App Security

如果已有 Cloud App Security，请确保它是[在网络上启用](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)。
如果部署 Cloud App Security，引入你的数据，警报数据可以轻松地进行流式传输到 Azure Sentinel。


1. 在 Azure Sentinel，选择**数据收集**，然后单击**Cloud App Security**磁贴。

2. 单击“连接”。

3. 若要使用 Log Analytics 中的 Cloud App Security 警报相关的架构，搜索**SecurityAlert**。


## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 Microsoft Cloud App Security 连接到 Azure Sentinel。 若要了解有关 Azure Sentinel 的详细信息，请参阅以下文章：
- 了解如何[来了解一下你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[检测威胁 Azure Sentinel](tutorial-detect-threats.md)。
