---
title: 将 Azure AD 标识保护数据连接到 Azure 哨兵
description: 了解如何将 Azure AD 标识保护数据连接到 Azure 哨兵。
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: yelevin
ms.openlocfilehash: 7d42ff28ddd2d883feb25139096d781efe64d50f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588563"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>连接来自 Azure AD 标识保护的数据



您可以将[Azure AD 标识保护](https://docs.microsoft.com/azure/information-protection/reports-aip)中的日志流式传输到 Azure Sentinel 中，以将警报流式传输到 Azure Sentinel 中，以查看仪表板、创建自定义警报并改进调查。 Azure 活动目录标识保护提供风险用户、风险检测和漏洞的整合视图，并能够立即修复风险，并设置策略以自动修复未来事件。 该服务基于 Microsoft 保护消费者身份的经验，从每天超过 130 亿次登录的信号中获得极大的准确性。 


## <a name="prerequisites"></a>先决条件

- 您必须具有[Azure 活动目录高级 P1 或 P2 许可证](https://azure.microsoft.com/pricing/details/active-directory/)
- 具有全局管理员或安全管理员权限的用户


## <a name="connect-to-azure-ad-identity-protection"></a>连接到 Azure AD 标识保护

如果您已经具有 Azure AD 标识保护，请确保它在[网络上启用](../active-directory/identity-protection/overview-identity-protection.md)。
如果部署 Azure AD 标识保护并获取数据，则警报数据可以轻松地流式传输到 Azure Sentinel 中。


1. 在 Azure 哨兵中，选择 **"数据"连接器**，然后单击**Azure AD 标识保护**磁贴。

2. 单击"**连接**"可开始将 Azure AD 标识保护事件流式传输到 Azure 哨兵中。


6. 要在日志分析中为 Azure AD 标识保护警报使用相关架构，请搜索**安全警报**。

## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 Azure AD 标识保护连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
