---
title: 将威胁智能数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将威胁智能数据连接到 Azure Sentinel。
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: 44b3830465bf2b5aa06612aa868b086b120f1ece
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372282"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>连接威胁情报提供商提供的数据

> [!IMPORTANT]
> Azure Sentinel 中的威胁情报平台数据连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Sentinel 使你可以导入组织使用的威胁指标，这可以提高安全分析师检测和确定已知威胁优先级的能力。 Azure Sentinel 提供的多项功能变为可用或增强：

- **分析**包括一组计划规则模板，你可以使用这些模板来生成基于威胁指标中的日志事件匹配项的警报和事件。

- **工作簿**提供有关导入到 Azure Sentinel 的威胁指标以及与威胁指标匹配的分析规则生成的任何警报的摘要信息。

- **搜寻**查询允许安全调查人员在常见搜寻方案的上下文中使用威胁指标。

- 当调查异常情况并寻找恶意行为时，**笔记本**可以使用威胁指标。

可以通过使用下一部分中列出的集成威胁情报平台（TIP）产品之一或使用与[Microsoft Graph Security TIINDICATORS API](https://aka.ms/graphsecuritytiindicators)的直接集成，将威胁指标流式传输到 Azure Sentinel。

## <a name="integrated-threat-intelligence-platform-products"></a>集成威胁情报平台产品

- [MISP 开源威胁情报平台](https://www.misp-project.org/)
    
    有关向客户端提供 MISP 实例以将威胁指标迁移到 Microsoft Graph 安全 API 的示例脚本，请参阅[MISP to Microsoft Graph Security script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP)。

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    有关指导说明，请参阅[使用 MineMeld 将 Ioc 发送到 Microsoft Graph 安全 API](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540)。

- [ThreatConnect 平台](https://threatconnect.com/solution/)


## <a name="prerequisites"></a>必备组件  

- Azure AD "全局管理员" 或 "安全管理员" 的角色，以向提示产品或自定义应用程序授予使用与 Microsoft Graph Security tiIndicators API 的直接集成的权限。

- 读取和写入 Azure Sentinel 工作区的权限，以存储威胁指标。

## <a name="connect-azure-sentinel-to-your-threat-intelligence-provider"></a>将 Azure Sentinel 连接到威胁情报提供商

1. 在 Azure Active Directory 中[注册应用](/graph/auth-v2-service#1-register-your-app)程序以获取应用程序 id、应用程序机密和 AZURE ACTIVE DIRECTORY 租户 id。 在配置集成提示产品或使用与 Microsoft Graph Security tiIndicators API 的直接集成的应用时，需要这些值。

2. 为已注册的应用程序[配置 API 权限](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph)：向已注册的应用程序添加 Microsoft Graph 应用程序权限**ThreatIndicators. OwnedBy** 。

3. 请求你的 Azure Active Directory 租户管理员向你的组织的注册应用程序授予管理员许可。 从 Azure 门户： **Azure Active Directory** > **应用注册** >  @no__t-**5_应用名称_>**  > **View API 权限**@no__t**4**。

4. 通过指定以下内容来配置提示产品或使用与 Microsoft Graph Security tiIndicators API 的直接集成的应用，将指示器发送到 Azure Sentinel：
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 已注册应用程序的 ID、机密和租户 ID 的值。
    
    b. 对于目标产品，请指定 Azure Sentinel。
    
    c. 对于 "操作"，请指定警报。

5. 在 Azure 门户中，导航到 " **Azure Sentinel** > **数据连接器**"，然后选择 "**威胁智能平台（预览版）** " 连接器。

6. 选择 "**打开连接器" 页面**，然后单击 "**连接**"。

7. 若要查看导入到 Azure Sentinel 的威胁指标，请导航到**Azure sentinel-Logs** > **SecurityInsights**，然后展开**ThreatIntelligenceIndicator**。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将威胁智能提供程序连接到 Azure Sentinel。 若要了解有关 Azure Sentinel 的详细信息，请参阅以下文章。

- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
