---
title: 收集 Azure Sentinel 预览版中的 Azure 安全中心数据 |Microsoft Docs
description: 了解如何收集 Azure Sentinel 中的 Azure 安全中心数据。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: f59c5f6a9f497a6420172996f9f327f16ffd26f9
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242264"
---
# <a name="collect-data-from-azure-security-center"></a>从 Azure 安全中心收集数据

> [!IMPORTANT]
> Azure Sentinel 目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



Azure Sentinel 使你可以收集的警报[Azure 安全中心](../security-center/security-center-intro.md)并将其传送到 Azure Sentinel。 

## <a name="prerequisites"></a>必备组件

- 如果你想要从 Azure 安全中心中导出的警报，你必须是您传输其日志的订阅的参与者。

- 您必须具有[Azure 安全中心标准层](../security-center/security-center-pricing.md)订阅上运行。 如果不是，[将订阅升级到标准](https://azure.microsoft.com/pricing/details/security-center/)。

- 您必须对你想要连接的每个订阅的全局管理员或安全管理员权限的用户登录。


## <a name="connect-to-azure-security-center"></a>连接到 Azure 安全中心

1. 在 Azure Sentinel，选择**数据收集**，然后单击**Azure 安全中心**磁贴。
1. 在右侧，单击**Connect**每个订阅你想要流式传输到 Azure Sentinel 其警报旁边。 请确保每个订阅升级到 Azure 安全中心标准层到流的警报与 Azure Sentinel。

3. 单击“连接”。

4. 若要使用 Log Analytics 中的 Azure 安全中心警报相关的架构，搜索**SecurityEvent**。

## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 Azure 安全中心连接到 Azure Sentinel。 若要了解有关 Azure Sentinel 的详细信息，请参阅以下文章：
- 了解如何[来了解一下你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[检测威胁 Azure Sentinel](tutorial-detect-threats.md)。
