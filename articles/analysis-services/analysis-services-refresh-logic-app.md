---
title: 刷新 Azure Analysis Services 模型的逻辑应用 |Microsoft Docs
description: 了解如何使用 Azure 逻辑应用对异步刷新进行代码编写。
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 2234a2c6cd42be45a2b2e7784c1dd5aec8839cb9
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311743"
---
# <a name="refresh-with-logic-apps"></a>使用逻辑应用进行刷新

通过使用逻辑应用和 REST 调用, 你可以对 Azure 分析表格模型执行自动数据刷新操作, 包括同步查询横向扩展副本。

若要了解有关将 REST Api 与 Azure Analysis Services 一起使用的详细信息, 请参阅[使用 REST API 的异步刷新](analysis-services-async-refresh.md)。

## <a name="authentication"></a>身份验证

所有调用都必须使用有效的 Azure Active Directory (OAuth 2) 令牌进行身份验证。  本文中的示例将使用服务主体 (SPN) 对 Azure Analysis Services 进行身份验证。 若要了解详细信息, 请参阅[使用 Azure 门户创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="design-the-logic-app"></a>设计逻辑应用

> [!IMPORTANT]
> 以下示例假定 Azure Analysis Services 防火墙已禁用。  如果启用了防火墙, 则必须在 Azure Analysis Services 防火墙中加入请求发起方的公共 IP 地址。 若要了解有关每个区域的逻辑应用 IP 范围的详细信息, 请参阅[Azure 逻辑应用的限制和配置信息](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses)。

### <a name="prerequisites"></a>系统必备

#### <a name="create-a-service-principal-spn"></a>创建服务主体 (SPN)

若要了解如何创建服务主体, 请参阅[使用 Azure 门户创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

#### <a name="configure-permissions-in-azure-analysis-services"></a>在 Azure Analysis Services 中配置权限
 
你创建的服务主体必须对服务器拥有服务器管理员权限。 有关详细信息，请参阅[将服务主体添加到服务器管理员角色](analysis-services-addservprinc-admins.md)。

### <a name="configure-the-logic-app"></a>配置逻辑应用

在此示例中, 逻辑应用旨在在收到 HTTP 请求时触发。 这将允许使用业务流程工具 (如 Azure 数据工厂) 触发 Azure Analysis Services 模型刷新。

创建逻辑应用后:

1. 在逻辑应用设计器中, 选择第一个操作, 就像**收到 HTTP 请求**一样。

   ![添加 HTTP 接收活动](./media/analysis-services-async-refresh-logic-app/1.png)

保存逻辑应用后, 此步骤将用 HTTP POST URL 填充。

2. 添加新步骤并搜索**HTTP**。  

   ![添加 HTTP 活动](./media/analysis-services-async-refresh-logic-app/9.png)

   ![添加 HTTP 活动](./media/analysis-services-async-refresh-logic-app/10.png)

3. 选择 " **HTTP** " 添加此操作。

   ![添加 HTTP 活动](./media/analysis-services-async-refresh-logic-app/2.png)

配置 HTTP 活动, 如下所示:

|属性  |ReplTest1  |
|---------|---------|
|**方法**     |发布         |
|**URI**     | https://*服务器区域*/servers/ *.aas server name*/models/*数据库名称*/refreshes <br /> <br /> 例如: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes|
|**标头**     |   Content-type、application/json <br /> <br />  ![标头](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**正文**     |   若要了解有关构成请求正文的详细信息, 请参阅[带有 REST API POST/refreshes 的异步刷新](analysis-services-async-refresh.md#post-refreshes)。 |
|**身份验证**     |Active Directory OAuth         |
|**组织**     |填写 Azure Active Directory TenantId         |
|**受众**     |https://*.asazure.windows.net         |
|**客户端 ID**     |输入服务主体名称 ClientID         |
|**凭据类型**     |Secret         |
|**机密**     |输入服务主体名称机密         |

例如：

![已完成 HTTP 活动](./media/analysis-services-async-refresh-logic-app/7.png)

现在, 请测试逻辑应用。  在逻辑应用设计器中, 单击 "**运行**"。

![测试逻辑应用](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>通过 Azure 数据工厂使用逻辑应用

保存逻辑应用后, 查看 "**收到 HTTP 请求时**" 活动, 然后复制现在生成的**http POST URL** 。  这是 Azure 数据工厂可用于触发逻辑应用的异步调用的 URL。

下面是执行此操作的示例 Azure 数据工厂 Web 活动。

![数据工厂 Web 活动](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>使用独立逻辑应用

如果不打算使用数据工厂等业务流程工具来触发模型刷新, 则可以将逻辑应用设置为根据计划触发刷新。

使用上面的示例, 删除第一个活动, 并将其替换为**计划**活动。

![计划活动](./media/analysis-services-async-refresh-logic-app/12.png)

![计划活动](./media/analysis-services-async-refresh-logic-app/13.png)

此示例将使用**重复周期**。

添加活动后, 配置时间间隔和频率, 然后添加新参数并选择 "**此时**"。

![计划活动](./media/analysis-services-async-refresh-logic-app/16.png)

选择所需的小时数。

![计划活动](./media/analysis-services-async-refresh-logic-app/15.png)

保存逻辑应用。

## <a name="next-steps"></a>后续步骤

[示例](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
