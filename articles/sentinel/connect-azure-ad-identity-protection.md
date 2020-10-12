---
title: 将 Azure AD Identity Protection 数据连接到 Azure Sentinel
description: 了解如何将日志和警报从 Azure AD Identity Protection 流式传输到 Azure Sentinel，以查看仪表板、创建自定义警报和改进调查。
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: 69ab76bf213653ea10db8dfd181b615a7e0f47b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564482"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>将数据从 Azure Active Directory (Azure AD) Identity Protection

可以将日志从 [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 流式传输到 azure sentinel，以将警报流式传输到 azure sentinel，以查看仪表板、创建自定义警报和改进调查。 Azure Active Directory Identity Protection 提供了风险用户、风险检测和漏洞的综合视图，能够立即补救风险，并设置策略以自动修正将来的事件。 此服务构建在 Microsoft 的体验上，可保护使用者标识，并从超过13000000000的一天的信号中获得极大的准确性。 

## <a name="prerequisites"></a>先决条件

- 您必须具有 [Azure AD Premium 的 P2 订阅](https://azure.microsoft.com/pricing/details/active-directory/)。
- 您必须具有具有全局管理员或安全管理员权限的用户。


## <a name="connect-to-azure-ad-identity-protection"></a>连接到 Azure AD Identity Protection

如果有 Azure AD Premium P2 订阅，则 Azure AD Identity Protection 包括在内。 如果启用了任何 [策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 并生成警报，则可以轻松地将警报数据流式传输到 Azure Sentinel。

1. 在 Azure Sentinel 中，选择 " **数据连接器** "，然后单击 " **Azure AD Identity Protection** " 磁贴。

1. 单击 " **连接** " 开始将 Azure AD Identity Protection 事件流式传输到 Azure Sentinel。

1. 若要在 Azure AD Identity Protection 警报 Log Analytics 中使用相关架构，请搜索 **SecurityAlert**。

如果要测试连接器，可以 [模拟检测](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md) 以生成将流式传输到 Azure Sentinel 的示例警报。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Azure AD Identity Protection 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
