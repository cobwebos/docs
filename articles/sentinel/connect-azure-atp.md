---
title: 将 Azure ATP 数据连接到 Azure Sentinel Preview |Microsoft Docs
description: 了解如何将 Azure ATP 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: rkarlin
ms.openlocfilehash: 6f41a5704c783ba8aeab7bc2e82ef731c6a257ac
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599157"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>连接来自 Azure 高级威胁防护 (ATP) 的数据

> [!IMPORTANT]
> Azure Sentinel 当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


只需要单击一次即可将日志从[Azure 高级威胁防护](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp)流式传输到 azure Sentinel。

## <a name="prerequisites"></a>系统必备

- 具有全局管理员或安全管理员权限的用户
- 你必须是 Azure ATP 的预览版客户

## <a name="connect-to-azure-atp"></a>连接到 Azure ATP

请确保[网络上已启用](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)Azure ATP 预览版本。
如果部署了 Azure ATP 并引入数据, 则可以轻松地将可疑警报流式传输到 Azure Sentinel。 可能需要长达24小时的时间才能开始向 Azure Sentinel 流式传输。


1. 若要将 Azure ATP 连接到 Azure Sentinel, 必须先在 Azure ATP 与 Microsoft Cloud App Security 之间启用集成。 有关如何执行此操作的信息, 请参阅[Azure 高级威胁防护集成](https://docs.microsoft.com/cloud-app-security/aatp-integration)。

1. 在 Azure Sentinel 中, 选择 "**数据连接器**", 然后单击 " **Azure ATP** " 磁贴。

2. 单击“连接”。

6. 若要在 Azure ATP 警报 Log Analytics 中使用相关架构, 请搜索**SecurityAlert**。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Azure 高级威胁防护连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。

