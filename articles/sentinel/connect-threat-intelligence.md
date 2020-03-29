---
title: 将威胁情报数据连接到 Azure 哨兵*微软文档
description: 了解如何将威胁智能数据连接到 Azure 哨兵。
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 5c79642d287224cd15531701d7cc87ebfd72eb69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588036"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>连接来自威胁情报提供商的数据

> [!IMPORTANT]
> Azure Sentinel 中的威胁智能数据连接器当前处于公共预览版中。
> 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Sentinel 允许您导入组织正在使用的威胁指示器，这可以增强安全分析人员检测已知威胁和确定已知威胁优先级的能力。 然后，Azure Sentinel 的多个功能变为可用或增强：

- **分析**包括一组计划规则模板，您可以启用这些模板，以便根据威胁指示器中的日志事件的匹配项生成警报和事件。

- **工作簿**提供有关导入到 Azure Sentinel 的威胁指标以及从分析规则生成的与威胁指示器匹配的任何警报的汇总信息。

- **寻猎**查询允许安全调查员在常见狩猎方案的上下文中使用威胁指示器。

- 当您调查异常并搜索恶意行为时 **，笔记本**可以使用威胁指示器。

您可以使用下一节中列出的集成威胁智能平台 （TIP） 产品之一、连接到 TAXII 服务器或使用直接集成 Microsoft[图形安全 tiIndicators API](https://aka.ms/graphsecuritytiindicators)将威胁指示器流式传输到 Azure Sentinel。

## <a name="integrated-threat-intelligence-platform-products"></a>集成威胁情报平台产品

- [MISP 开源威胁情报平台](https://www.misp-project.org/)
    
    有关向客户端提供 MISP 实例将威胁指示器迁移到 Microsoft 图形安全 API 的示例脚本，请参阅[MISP 到 Microsoft 图形安全脚本](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP)。

- [帕洛阿尔托网络矿梅尔德](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    有关指导性说明，请参阅[使用 MineMeld 将 IOC 发送到 Microsoft 图形安全 API。](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540)

- [威胁连接平台](https://threatconnect.com/solution/)

    有关详细信息，请参阅[威胁连接集成，](https://threatconnect.com/integrations/)并在页面上查找 Microsoft 图形安全 API。


## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>将 Azure 哨兵连接到威胁情报平台

## <a name="prerequisites"></a>先决条件  

- 全局管理员或安全管理员的 Azure AD 角色，用于向使用与 Microsoft 图形安全 tiIndicators API 直接集成的 TIP 产品或自定义应用程序授予权限。

- 读取和写入 Azure Sentinel 工作区的权限以存储威胁指示器。

## <a name="instructions"></a>Instructions

1. 在 Azure 活动目录中[注册应用程序](/graph/auth-v2-service#1-register-your-app)以获取应用程序 ID、应用程序机密和 Azure 活动目录租户 ID。 当您配置集成的 TIP 产品或应用时，您需要这些值，这些产品或应用使用与 Microsoft 图形安全 tiIndicators API 的直接集成。

2. [为](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph)注册应用程序配置 API 权限：将 Microsoft 图形应用程序权限**威胁指标.ReadWrite.自拥有添加到**已注册的应用程序。

3. 请 Azure 活动目录租户管理员向组织的注册应用程序授予管理员同意。 从 Azure 门户 **：Azure 活动目录** > **应用注册** > **\<_应用名称_>** > **视图 API 权限** > **授予\<_管理员同意租户名称_>**。

4. 配置使用与 Microsoft 图形安全 tiIndicators API 直接集成的 TIP 产品或应用，通过指定以下内容将指标发送到 Azure Sentinel：
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 已注册应用程序的 ID、机密和租户 ID 的值。
    
    b.保留“数据库类型”设置，即设置为“共享”。 对于目标产品，请指定 Azure 哨兵。
    
    c. 对于操作，请指定警报。

5. 在 Azure 门户中，导航到**Azure 哨兵** > **数据连接器**，然后选择**威胁智能平台（预览）** 连接器。

6. 选择 **"打开连接器"页**，然后 **"连接**"。

7. 要查看导入到 Azure Sentinel 的威胁指示器，请查看**Azure 哨兵 - 日志** > **安全见解**，然后展开**威胁智能指示器**。

## <a name="connect-azure-sentinel-to-taxii-servers"></a>将 Azure 哨兵连接到 TAXII 服务器

## <a name="prerequisites"></a>先决条件  

- 读取和写入 Azure Sentinel 工作区的权限以存储威胁指示器。

- TAXII 2.0 服务器 URI 和集合 ID。

## <a name="instructions"></a>Instructions

1. 在 Azure 门户中，导航到**Azure 哨兵** > **数据连接器**，然后选择**威胁智能 - TAXII（预览）** 连接器。

2. 选择 **"打开连接器"页**。

3. 在文本框中指定所需的和可选的信息。

4. 选择 **"添加**"以启用与 TAXII 2.0 服务器的连接。

5. 如果您有其他 TAXII 2.0 服务器：重复步骤 3 和 4。

6. 要查看导入到 Azure Sentinel 的威胁指示器，请查看**Azure 哨兵 - 日志** > **安全见解**，然后展开**威胁智能指示器**。

## <a name="next-steps"></a>后续步骤

在本文中，您学习了如何将威胁智能提供程序连接到 Azure Sentinel。 要了解有关 Azure 哨兵的更多内容，请参阅以下文章。

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
