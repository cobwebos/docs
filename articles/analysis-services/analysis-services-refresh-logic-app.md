---
title: 使用逻辑应用的 Azure Analysis Services 模型刷新 |Microsoft Docs
description: 了解如何通过使用 Azure 逻辑应用的代码异步刷新。
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 6e1ac5dfd1972e406a1bd8dcd26e6aef2c4ea6d1
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919869"
---
# <a name="refresh-with-logic-apps"></a>刷新使用逻辑应用

通过使用逻辑应用和 REST 调用，可以执行 Azure 分析表格模型，包括同步的查询横向扩展的只读副本上的自动的数据刷新操作。

若要了解有关使用 REST Api 和 Azure Analysis Services 的详细信息，请参阅[使用 REST API 的异步刷新](analysis-services-async-refresh.md)。

## <a name="authentication"></a>Authentication

所有调用必须使用有效的 Azure Active Directory (OAuth 2) 令牌进行身份都验证。  在本文中的示例将使用服务主体 (SPN) 对 Azure Analysis Services 进行身份验证。 若要了解详细信息，请参阅[通过使用 Azure 门户创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="design-the-logic-app"></a>设计逻辑应用

> [!IMPORTANT]
> 下面的示例假定 Azure Analysis Services 防火墙处于禁用状态。  如果启用了防火墙，请求发起程序的公共 IP 地址必须是 Azure Analysis Services 防火墙中的已列入允许列表。 若要了解有关每个区域的逻辑应用 IP 范围的详细信息，请参阅[限制和 Azure 逻辑应用的配置信息](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses)。

### <a name="prerequisites"></a>必备组件

#### <a name="create-a-service-principal-spn"></a>创建服务主体 (SPN)

若要了解如何创建服务主体，请参阅[通过使用 Azure 门户创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

#### <a name="configure-permissions-in-azure-analysis-services"></a>在 Azure Analysis Services 中配置权限
 
您创建的服务主体必须具有服务器管理员权限在服务器上。 有关详细信息，请参阅[将服务主体添加到服务器管理员角色](analysis-services-addservprinc-admins.md)。

### <a name="configure-the-logic-app"></a>配置逻辑应用

在此示例中，逻辑应用设计时收到的 HTTP 请求触发。 这样，便使用一个业务流程工具，例如 Azure 数据工厂，就能触发 Azure Analysis Services 模型刷新。

一旦您已创建逻辑应用：

1. 在逻辑应用设计器中，选择第一个操作**收到时 HTTP 请求**。

   ![添加 HTTP 接收活动](./media/analysis-services-async-refresh-logic-app/1.png)

保存逻辑应用后，使用 HTTP POST URL 将填充此步骤。

2. 添加新步骤，并搜索**HTTP**。  

   ![添加 HTTP 活动](./media/analysis-services-async-refresh-logic-app/9.png)

   ![添加 HTTP 活动](./media/analysis-services-async-refresh-logic-app/10.png)

3. 选择**HTTP**添加此操作。

   ![添加 HTTP 活动](./media/analysis-services-async-refresh-logic-app/2.png)

配置 HTTP 活动，如下所示：

|属性  |值  |
|---------|---------|
|**方法**     |POST         |
|**URI**     | https://*服务器区域*/servers/*作为服务器名称*/models/*你的数据库名称*/ <br /> <br /> 例如： https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/|
|**标头**     |   内容类型、 应用程序/json <br /> <br />  ![标头](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**正文**     |   若要了解有关构成请求正文的详细信息，请参阅[异步刷新使用 REST API-POST /refreshes](analysis-services-async-refresh.md#post-refreshes)。 |
|**身份验证**     |Active Directory OAuth         |
|**租户**     |填写在 Azure Active Directory 租户 Id         |
|**受众**     |https://*.asazure.windows.net         |
|**客户端 ID**     |输入你的服务主体名称 ClientID         |
|**凭据类型**     |密钥         |
|**机密**     |输入服务主体名称机密         |

示例：

![已完成的 HTTP 活动](./media/analysis-services-async-refresh-logic-app/7.png)

现在测试逻辑应用。  在逻辑应用设计器中，单击**运行**。

![测试逻辑应用](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>使用逻辑应用使用 Azure 数据工厂

保存逻辑应用后，查看**收到时 HTTP 请求**活动并复制**HTTP POST URL** ，现在会生成。  这是 Azure 数据工厂可用于进行异步调用触发逻辑应用的 URL。

下面是一个示例 Azure 数据工厂 Web 活动执行此操作。

![数据工厂 Web 活动](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>使用独立的逻辑应用

如果您不打算使用业务流程工具等数据工厂将触发模型刷新，你可以设置该逻辑应用可以触发基于计划的刷新。

使用上面的示例中，删除第一个活动并将其替换为**计划**活动。

![计划活动](./media/analysis-services-async-refresh-logic-app/12.png)

![计划活动](./media/analysis-services-async-refresh-logic-app/13.png)

此示例将使用**重复周期**。

添加活动后配置的时间间隔和频率，然后添加一个新的参数，并选择**在这些小时**。

![计划活动](./media/analysis-services-async-refresh-logic-app/16.png)

选择所需的小时。

![计划活动](./media/analysis-services-async-refresh-logic-app/15.png)

保存逻辑应用。

## <a name="next-steps"></a>后续步骤

[示例](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
