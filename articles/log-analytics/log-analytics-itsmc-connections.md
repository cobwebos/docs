---
title: "Azure Log Analytics 中与 IT Service Management Connector 的手支持连接 | Microsoft Docs"
description: "将 ITSM 产品/服务与 Azure Log Analytics 中的 IT Service Management Connector 相连接，以集中监视和管理 ITSM 工作项。"
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 8231b7ce-d67f-4237-afbf-465e2e397105
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: v-jysur
ms.openlocfilehash: bbec5773987b29eb62d10d17b88efcda29889612
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector-preview"></a>将 ITSM 产品/服务与 IT Service Management Connector（预览版）相连接
本文提供有关如何将 ITSM 产品/服务连接到 OMS 中的 IT Service Management Connector，以集中管理工作项的信息。 有关 IT Service Management Connector 的详细信息，请参阅[概述](log-analytics-itsmc-overview.md)。

支持以下产品/服务：

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-oms)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-oms)
- [Provance](#connect-provance-to-it-service-management-connector-in-oms)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-oms"></a>将 System Center Service Manager 连接到 OMS 中的 IT Service Management Connector

以下部分提供有关如何将 System Center Service Manager 产品连接到 OMS 中的 IT Service Management Connector 的详细信息。

### <a name="prerequisites"></a>先决条件

请确保满足以下先决条件：

- 已安装 IT Service Management Connector。
详细信息：[添加 IT Service Management Connector 解决方案](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution)。
- 已部署并配置 Service Manager Web 应用程序（Web 应用）。 [此处](#create-and-deploy-service-manager-web-app-service)提供了有关 Web 应用的信息。
- 已创建并配置混合连接。 详细信息：[配置混合连接](#configure-the-hybrid-connection)。
- 支持的 Service Manager 版本：2012 R2 或 2016。
- 用户角色：[高级操作员](https://technet.microsoft.com/library/ff461054.aspx)。

### <a name="connection-procedure"></a>连接过程

使用以下过程将 System Center Service Manager 实例连接到 IT Service Management Connector：

1. 转到“OMS” >“设置” > “连接的源”。
2. 选择“ITSM Connector”，单击“添加新连接”。

    ![Service Manager ](./media/log-analytics-itsmc/itsmc-service-manager-connection.png)
3. 提供下表中所述的信息，并单击“保存”创建连接：

> [!NOTE]
> 所有这些参数都是必需的。

| **字段** | **说明** |
| --- | --- |
| **Name**   | 键入要与 IT Service Management Connector 连接的 System Center Service Manager 实例的名称。  以后在此实例中配置工作项/查看详细日志分析时，需要使用此名称。 |
| **选择连接类型**   | 选择“System Center Service Manager”。 |
| **服务器 URL**   | 键入 Service Manager Web 应用的 URL。 [此处](#create-and-deploy-service-manager-web-app-service)提供了有关 Service Manager Web 应用的详细信息。
| **客户端 ID**   | 键入（使用自动脚本）生成的、用于对 Web 应用进行身份验证的客户端 ID。 [此处](log-analytics-itsmc-service-manager-script.md)提供了有关自动化脚本的详细信息。|
| **客户端机密**   | 键入为此 ID 生成的客户端机密。   |
| **数据同步范围**   | 选择要通过 IT Service Management Connector 同步的 Service Manager 工作项。  这些工作项将导入到 Log Analytics。 **选项：**“事件”、“更改请求”。|
| **同步数据** | 键入检索数据的过去天数。 **最大限制**：120 天 |
| **在 ITSM 解决方案中创建新的配置项** | 如果想要在 ITSM 产品中创建配置项，请选择此选项。 选择此选项后，OMS 会在支持的 ITSM 系统中创建受影响的 CI 作为配置项（如果不存在 CI）。 **默认**：已禁用。 |

成功连接并同步后：

- 选定的工作项将从 Service Manager 导入 OMS **Log Analytics**。 可以在 IT Service Management Connector 磁贴中查看这些工作项的摘要。

- 在此 Service Manager 实例中，可以通过 OMS 从 OMS 警报或日志搜索创建事件。

详细信息：[为 OMS 警报创建 ITSM 工作项](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts)和[从 OMS 日志创建 ITSM 工作项](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs)。

### <a name="create-and-deploy-service-manager-web-app-service"></a>创建和部署 Service Manager Web 应用服务

为了将本地 Service Manager 与 OMS 中的 IT Service Management Connector 相连接，Microsoft 已在 GitHub 中创建一个 Service Manager Web 应用。

若要为 Service Manager 设置 ITSM Web 应用，请执行以下操作：

- **部署 Web 应用** – 部署 Web 应用，设置属性，并在 Azure AD 上进行身份验证。 可以使用 Microsoft 提供的[自动化脚本](log-analytics-itsmc-service-manager-script.md)部署 Web 应用。
- 手动**配置混合连接** - [配置此连接](#configure-the-hybrid-connection)。

#### <a name="deploy-the-web-app"></a>部署 Web 应用
使用自动化[脚本](log-analytics-itsmc-service-manager-script.md)部署 Web 应用，设置属性，并在 Azure AD 上进行身份验证。

通过提供以下所需详细信息来运行脚本：

- Azure 订阅详细信息
- 资源组名称
- 位置
- Service Manager 服务器详细信息（服务器名称、域、用户名和密码）
- Web 应用的站点名称前缀
- ServiceBus 命名空间。

该脚本将使用指定的名称（以及使该名称保持唯一的其他几个字符串）创建 Web 应用。 它将生成 **Web 应用 URL**、**客户端 ID** 和**客户端机密**。

请保存这些值，因为在与 IT Service Management Connector 建立连接时需要用到。

**检查 Web 应用安装**

1. 转到“Azure 门户” > “资源”。
2. 选择 Web 应用，并单击“设置” > “应用程序设置”。
3. 确认有关通过脚本部署应用时提供的 Service Manager 实例的信息。

### <a name="configure-the-hybrid-connection"></a>配置混合连接

使用以下过程来配置混合连接，以便将 Service Manager 实例与 OMS 中的 IT Service Management Connector 相连接。

1. 在“Azure 资源”下面找到 Service Manager Web 应用。
2. 单击“设置” > “网络”。
3. 在“混合连接”下面，单击“配置混合连接终结点”。

    ![混合连接网络](./media/log-analytics-itsmc/itsmc-hybrid-connection-networking-and-end-points.png)
4. 在“混合连接”边栏选项卡中，单击“添加混合连接”。

    ![添加混合连接](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-add.png)

5. 在“添加混合连接”边栏选项卡中，单击“创建新的混合连接”。

    ![新建混合连接](./media/log-analytics-itsmc/itsmc-create-new-hybrid-connection.png)

6. 键入以下值：

    - **终结点名称**：指定新混合连接的名称。
    -  **终结点主机**：Service Manager 管理服务器的 FQDN。
    - **终结点端口**：键入 5724
    - **服务总线命名空间**：使用现有的服务总线命名空间，或新建一个。
    - **位置**：选择位置。
    -  **名称**：指定服务总线的名称（如果要创建）。

    ![混合连接值](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-values.png)
6. 单击“确定”关闭“创建混合连接”边栏选项卡并开始创建混合连接。

    创建混合连接后，它会显示在边栏选项卡下面。

7. 创建混合连接后，请选择该连接，并单击“添加所选混合连接”。

    ![新建混合连接](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>配置侦听器设置

使用以下过程配置混合连接的侦听器设置。

1. 在“混合连接”边栏选项卡中，单击“下载连接管理器”，并将它安装在运行 System Center Service Manager 实例的计算机上。

    安装完成后，“开始”菜单下会出现“混合连接管理器 UI”选项。

2. 单击“混合连接管理器 UI”。系统会提示提供 Azure 凭据。

3. 请使用 Azure 凭据登录，并选择在其中创建了混合连接的订阅。

4. 单击“保存” 。

现已成功连接到混合连接。

![混合连接成功](./media/log-analytics-itsmc/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]

> 创建混合连接后，请通过访问部署的 Service Manager Web 应用来验证和测试该连接。 请确保该连接成功后，再尝试连接到 OMS 中的 IT Service Management Connector。

下图显示了成功连接的详细信息：

![混合连接测试](./media/log-analytics-itsmc/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-oms"></a>将 ServiceNow 连接到 OMS 中的 IT Service Management Connector

以下部分提供有关如何将 ServiceNow 产品连接到 OMS 中的 IT Service Management Connector 的详细信息。

### <a name="prerequisites"></a>先决条件

请确保满足以下先决条件：

- 已安装 IT Service Management Connector。 详细信息：[添加 IT Service Management Connector 解决方案](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution)。
- ServiceNow 支持的版本 – Fuji、Geneva、Helsinki。

ServiceNow 管理员必须在其 ServiceNow 实例中执行以下操作：
- 生成 ServiceNow 产品的客户端 ID 和客户端密码。 有关如何生成客户端 ID 和机密的信息，请参阅 [OAuth 设置](http://wiki.servicenow.com/index.php?title=OAuth_Setup)。
- 安装用于 Microsoft OMS 集成的用户应用（ServiceNow 应用）。 [了解详细信息](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.0 )。
- 为安装的用户应用创建集成用户角色。 [此处](#create-integration-user-role-in-servicenow-app)提供了有关如何创建集成用户角色的信息。


### <a name="connection-procedure"></a>**连接过程**

使用以下过程创建 ServiceNow 连接：

1. 转到“OMS” > “设置” > “连接的源”。
2. 选择“ITSM Connector”，单击“添加新连接”。

    ![ServiceNow 连接](./media/log-analytics-itsmc/itsmc-servicenow-connection.png)

3. 提供下表中所述的信息，并单击“保存”创建连接：

> [!NOTE]
> 所有这些参数都是必需的。

| **字段** | **说明** |
| --- | --- |
| **Name**   | 键入要与 IT Service Management Connector 连接的 ServiceNow 实例的名称。  以后在此 ITSM 中配置工作项/查看详细日志分析时，需要在 OMS 中使用此名称。 |
| **选择连接类型**   | 选择“ServiceNow”。 |
| **用户名**   | 键入在 ServiceNow 应用中创建的、用于支持连接到 IT Service Management Connector 的集成用户名。 详细信息：[创建 ServiceNow 应用用户角色](#create-integration-user-role-in-servicenow-app)。|
| **密码**   | 键入此用户名的关联密码。 **注意**：用户名和密码仅用于生成身份验证令牌，不会存储在 OMS 服务中的任何位置。  |
| **服务器 URL**   | 键入要连接到 IT Service Management Connector 的 ServiceNow 实例的 URL。 |
| **客户端 ID**   | 键入前面生成的、用于 OAuth2 身份验证的客户端 ID。  有关生成客户端 ID 和机密的详细信息：[OAuth 设置](http://wiki.servicenow.com/index.php?title=OAuth_Setup)。 |
| **客户端机密**   | 键入为此 ID 生成的客户端机密。   |
| **数据同步范围**   | 选择要通过 IT Service Management Connector 同步到 OMS 的 ServiceNow 工作项。  选定的值将导入到 Log Analytics。   **选项：**“事件”和“更改请求”。|
| **同步数据** | 键入检索数据的过去天数。 **最大限制**：120 天 |
| **在 ITSM 解决方案中创建新的配置项** | 如果想要在 ITSM 产品中创建配置项，请选择此选项。 选择此选项后，OMS 会在支持的 ITSM 系统中创建受影响的 CI 作为配置项（如果不存在 CI）。 **默认**：已禁用。 |


成功连接并同步后：

- 选定的工作项将从 ServiceNow 连接导入到 OMS Log Analytics。  可以在 IT Service Management Connector 磁贴中查看这些工作项的摘要。
- 在此 ServiceNow 实例中，可以通过 OMS 警告或日志搜索创建警报和事件。  


详细信息：[为 OMS 警报创建 ITSM 工作项](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts)和[从 OMS 日志创建 ITSM 工作项](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs)。

### <a name="create-integration-user-role-in-servicenow-app"></a>在 ServiceNow 应用中创建集成用户角色

使用以下过程：

1.  访问 [ServiceNow 应用商店](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.0)，并在 ServiceNow 实例中安装**用于 ServiceNow 和 Microsoft OMS 集成的用户应用**。
2.  安装后，请访问 ServiceNow 实例的左侧导航栏，搜索并选择 Microsoft OMS 集成器。  
3.  单击“安装清单”。

    如果用户角色尚未创建，状态会显示为“未完成”。

4.  在“创建集成用户”旁边的文本框中，输入可连接到 OMS 中的 IT Service Management Connector 的用户的用户名。
5.  输入此用户的密码，并单击“确定”。  

>[!NOTE]

> 使用这些凭据在 OMS 中建立 ServiceNow 连接。

将显示新建的用户及其分配的默认角色。

默认角色：
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   itil
-   template_editor
-   view_changer

成功创建用户后，“检查安装清单”的状态将改为“已完成”，同时会列出针对应用创建的用户角色的详细信息。

> [!NOTE]

> 若要允许用户在 ServiceNow 中通过 OMS 创建**警报**和**事件**：

> - 请确保已在 ServiceNow 实例上安装事件管理模块。

> - 将以下角色添加到集成用户：
>      - evt_mgmt_integration
>      - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-oms"></a>将 Provance 连接到 OMS 中的 IT Service Management Connector

以下部分提供有关如何将 Provance 产品连接到 OMS 中的 IT Service Management Connector 的详细信息。

### <a name="prerequisites"></a>先决条件

请确保满足以下先决条件：


- 已安装 IT Service Management Connector。 详细信息：[添加 IT Service Management Connector 解决方案](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution)。
- Provance 应用应已注册到 Azure AD，并且可提供客户端 ID。 有关详细信息，请参阅[如何配置 Active Directory 身份验证](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)。

- 用户角色：管理员。

### <a name="connection-procedure"></a>连接过程

使用以下过程创建 Provance 连接：

1. 转到“OMS” > “设置” > “连接的源”。
2. 选择“ITSM Connector”，单击“添加新连接”。  

    ![Provance 连接](./media/log-analytics-itsmc/itsmc-provance-connection.png)
3. 提供下表中所述的信息，并单击“保存”创建连接。

> [!NOTE]
> 所有这些参数都是必需的。

| **字段** | **说明** |
| --- | --- |
| **Name**   | 键入要与 IT Service Management Connector 连接的 Provance 实例的名称。  以后在此 ITSM 中配置工作项/查看详细日志分析时，需要在 OMS 中使用此名称。 |
| **选择连接类型**   | 选择“Provance”。 |
| **用户名**   | 键入可连接到 IT Service Management Connector 的用户名。    |
| **密码**   | 键入此用户名的关联密码。 **注意**：用户名和密码仅用于生成身份验证令牌，不会存储在 OMS 服务中的任何位置。|
| **服务器 URL**   | 键入要连接到 IT Service Management Connector 的 Provance 实例的 URL。 |
| **客户端 ID**   | 键入在 Provance 实例中生成的、用于对此连接进行身份验证的客户端 ID。  有关客户端 ID 的详细信息，请参阅[如何配置 Active Directory 身份验证](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md)。 |
| **数据同步范围**   | 选择要通过 IT Service Management Connector 同步到 OMS 的 Provance 工作项。  这些工作项将导入到 Log Analytics。   **选项：**“事件”、“更改请求”。|
| **同步数据** | 键入检索数据的过去天数。 **最大限制**：120 天 |
| **在 ITSM 解决方案中创建新的配置项** | 如果想要在 ITSM 产品中创建配置项，请选择此选项。 选择此选项后，OMS 会在支持的 ITSM 系统中创建受影响的 CI 作为配置项（如果不存在 CI）。 **默认**：已禁用。|

成功连接并同步后：

- 选定的工作项将从 Provance 连接导入到 OMS **Log Analytics**。  可以在 IT Service Management Connector 磁贴中查看这些工作项的摘要。
- 在此 Provance 实例中，可以通过 OMS 警告或日志搜索创建事件。

详细信息：[为 OMS 警报创建 ITSM 工作项](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts)和[从 OMS 日志创建 ITSM 工作项](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs)。

## <a name="connect-cherwell-to-it-service-management-connector-in-oms"></a>将 Cherwell 连接到 OMS 中的 IT Service Management Connector

以下部分提供有关如何将 Cherwell 产品连接到 OMS 中的 IT Service Management Connector 的详细信息。

### <a name="prerequisites"></a>先决条件

请确保满足以下先决条件：

- 已安装 IT Service Management Connector。 详细信息：[添加 IT Service Management Connector 解决方案](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution)。
- 已生成客户端 ID。 详细信息：[为 Cherwell 生成客户端 ID](#generate-client-id-for-cherwell)。
- 用户角色：管理员。

### <a name="connection-procedure"></a>连接过程

使用以下过程创建 Cherwell 连接：

1. 转到“OMS” >  “设置” > “连接的源”。
2. 选择“ITSM Connector”，单击“添加新连接”。  

    ![Cherwell 用户 ID](./media/log-analytics-itsmc/itsmc-cherwell-connection.png)

3. 提供下表中所述的信息，并单击“保存”创建连接。

> [!NOTE]
> 所有这些参数都是必需的。

| **字段** | **说明** |
| --- | --- |
| **Name**   | 键入要连接到 IT Service Management Connector 的 Cherwell 实例的名称。  以后在此 ITSM 中配置工作项/查看详细日志分析时，需要在 OMS 中使用此名称。 |
| **选择连接类型**   | 选择“Cherwell”。 |
| **用户名**   | 键入可连接到 IT Service Management Connector 的 Cherwell 用户名。 |
| **密码**   | 键入此用户名的关联密码。 **注意**：用户名和密码仅用于生成身份验证令牌，不会存储在 OMS 服务中的任何位置。|
| **服务器 URL**   | 键入要连接到 IT Service Management Connector 的 Cherwell 实例的 URL。 |
| **客户端 ID**   | 键入在 Cherwell 实例中生成的、用于对此连接进行身份验证的客户端 ID。   |
| **数据同步范围**   | 选择要通过 IT Service Management Connector 同步的 Cherwell 工作项。  这些工作项将导入到 Log Analytics。   **选项：**“事件”、“更改请求”。 |
| **同步数据** | 键入检索数据的过去天数。 **最大限制**：120 天 |
| **在 ITSM 解决方案中创建新的配置项** | 如果想要在 ITSM 产品中创建配置项，请选择此选项。 选择此选项后，OMS 会在支持的 ITSM 系统中创建受影响的 CI 作为配置项（如果不存在 CI）。 **默认**：已禁用。 |

成功连接并同步后：

- 选定的工作项将从 Cherwell 连接导入到 OMS Log Analytics。 可以在 IT Service Management Connector 磁贴中查看这些工作项的摘要。
- 可以在此 Cherwell 实例中通过 OMS 创建事件。 详细信息：“为 OMS 警报创建 ITSM 工作项”和“从 OMS 日志创建 ITSM 工作项”。

详细信息：[为 OMS 警报创建 ITSM 工作项](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts)和[从 OMS 日志创建 ITSM 工作项](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs)。

### <a name="generate-client-id-for-cherwell"></a>为 Cherwell 生成客户端 ID

若要为 Cherwell 生成客户端 ID/密钥，请使用以下过程：

1. 以管理员身份登录到 Cherwell 实例。
2. 单击“安全性” > “编辑 REST API 客户端设置”。
3. 选择“创建新客户端” > “客户端机密”。

    ![Cherwell 用户 ID](./media/log-analytics-itsmc/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>后续步骤
 - [为 OMS 警报创建 ITSM 工作项](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts)

 - [从 OMS 日志创建 ITSM 工作项](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs)

- [查看连接的日志分析](log-analytics-itsmc-overview.md#using-the-solution)
