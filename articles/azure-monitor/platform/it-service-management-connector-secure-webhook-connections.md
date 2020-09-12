---
title: IT 服务管理连接器-安全导出 Azure Monitor
description: 本文提供了有关如何使用 Azure Monitor 中的安全导出来连接 ITSM 产品/服务的信息，以集中监视和管理 ITSM 工作项。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 160054e7e98dc2cb06c2c7daf325536766963daa
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568636"
---
# <a name="connect-azure-to-itsm-tools-using-secure-export"></a>使用安全导出将 Azure 连接到 ITSM 工具

本文提供了有关如何使用安全导出在 ITSM 产品/服务之间配置连接的信息。

安全导出是 [ITSMC](./itsmc-overview.md) (IT 服务管理) 的更新版本。 这两个版本都允许在 Azure Monitor 警报触发时在 ITSM 工具中创建工作项。 此功能包括指标、日志和活动日志警报。

[ITSMC](./itsmc-overview.md) 使用用户和密码凭据，而安全导出具有更强的身份验证，因为它使用 Azure Active Directory (Azure AD) 。 Azure Active Directory (Azure AD) 是 Microsoft 推出的基于云的标识和访问管理服务。 它可帮助用户登录和访问内部或外部资源。 将 Azure AD 与 ITSM 结合使用，可帮助使用发送到外部系统) Azure AD 应用程序 ID (来识别 Azure 警报。

> [!NOTE]
> 使用安全导出连接 Azure 到 ITSM 工具的预览

## <a name="secure-export-architecture"></a>安全导出体系结构

安全导出体系结构引入了以下新功能：

* **新的操作组** -使用安全 webhook 操作组将警报发送到 ITSM 工具， (而不是在 ITSMC) 中使用 ITSM 操作组。
* **Azure AD 身份验证** -使用 Azure AD 而不是用户/密码凭据进行身份验证。

## <a name="secure-export-data-flow"></a>安全导出数据流

安全导出数据流步骤如下：

1) 配置为使用安全导出的警报在 Azure Monitor
2) 警报负载会通过安全 webhook 操作发送到 ITSM 工具。
3) 如果警报有权进入 ITSM 工具，ITSM 应用程序会检查 Azure AD。
4) 如果警报已授权应用程序：
    1) 在 ITSM 工具中创建工作项 (例如事件) 。
    2)  (CMDB) 将配置项目 (CI) ID 绑定到客户管理数据库。
![Itsm 图](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="connection-with-bmc-helix"></a>与 BMC Helix 连接

安全导出支持 BMC Helix。 集成的一些优点包括：

* **更好的身份验证** – Azure AD 提供更安全的身份验证，而不会发生 ITSMC 中经常发生的超时。
* **ITSM 工具中解决的警报** -指标警报实现 "已触发" 和 "已解决" 状态。 满足条件时，警报状态为 "已触发"。 当不再满足条件时，警报状态为 "已解决"。 在 ITSMC 中，无法自动解决警报。 通过安全导出，已解决的状态会流向 ITSM 工具，因此会自动更新。
* **[常见架构允许](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)** –在 ITSMC 中，警报有效负载的架构根据警报类型的情况而有差异。 在安全导出中，我们是适用于所有警报类型的常见架构。 这一新的通用架构包含所有警报类型的 CI。 通过此方法，所有警报类型都可以与 CMDB 绑定其 CI。

使用以下步骤开始使用 ITSM 连接器：

1. 将你的应用程序注册到 Azure Active Directory。
2. 创建安全 webhook 操作组。
3. 配置合作伙伴环境。

## <a name="register-with-azure-active-directory"></a>注册到 Azure Active Directory

请按照以下步骤向注册 Azure AD 应用程序 Azure Active Directory

1) [Azure AD 创建](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)
2) 在 Azure Active Directory 选择 "公开应用程序"
3) 在应用程序 ID URI 上选择 "设置" [ ![ Azure AD](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4) 单击“保存”。

## <a name="create-a-secure-webhook-action-group"></a>创建安全 Webhook 操作组

注册 Azure AD 后，可以使用操作组中的安全 Webhook 操作，基于 Azure 警报在 ITSM 工具中创建工作项 (s) 。
操作组对 Azure 警报提供模块化且可重用的方法来触发操作。 可以在 Azure 门户结合指标警报、活动日志警报和 Azure Log Analytics 警报使用操作组。
若要了解有关操作组的详细信息，请参阅[在 Azure 门户中创建和管理操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)。
请按以下过程操作：

在 BMC Helix 环境中：

1. 登录到 Integration Studio。
2. 从 "Azure 警报流" 中搜索 "创建事件"。
3. 复制 WebHook URL。
![BMC URL](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)

若要将 webhook 添加到操作，请按照安全 webhook 的说明进行操作：

1. 在 [Azure 门户](https://portal.azure.com/)中，搜索并选择“监视”。 “监视”窗格将所有监视设置和数据合并到一个视图中。
2. 选择“警报”，然后选择“管理操作”。
3. 选择“添加操作组”，并填写字段。
4. 在“操作组名称”框中输入名称，然后在“短名称”框中输入名称。 使用此组发送通知时，短名称被用来代替完整的操作组名称。
5. 选择 **受保护的 Webhook**
6. 选择 "编辑详细信息"。 下图显示了一个示例安全 webhook 操作：
    1. 选择所注册 Azure Active Directory 的正确对象 ID
    2. 在 "URI" 字段中粘贴从 "BMC Helix 环境" 复制的 WebHook URL
    3. 将 **常见警报架构** 设置为 **"是"**。 
7. 下图显示了一个示例安全 webhook 操作配置： ![ 安全 webhook](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-partner-environment"></a>配置合作伙伴环境

### <a name="connect-bmc-helix-to-azure-monitor"></a>将 BMC Helix 连接到 Azure Monitor

以下部分提供有关如何在 Azure 中连接 BMC Helix 产品和安全导出的详细信息。

### <a name="prerequisites"></a>先决条件

请确保满足以下先决条件：

* 已注册 Azure AD。
* 支持的 BMC 版本 Helix 多云服务管理：20.02 版或更高版本

若要配置 BMC Helix 连接：

1) [为版本20.2 启用与 Azure Monitor 的预生成集成](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)

2) 作为 BMC Helix 中的连接配置的一部分，请进入你的集成 BMC 实例，并按照说明进行操作：

1. 选择 **目录**
2. 选择 **Azure 警报**
3. 选择 **连接器**
4. 选择 **配置**
5. 选择 " **添加新连接** 配置"
6. 填写配置部分的信息。
    1. **名称** -构成你自己的
    2. **授权类型** -无
    3. **说明**-自行创建
    4. **站点**-云
    5. **实例数** -2 –默认值
    6. **选中-默认** 情况下选中并启用使用情况
    7. Azure 租户 ID，Azure 应用程序 ID 是从应用程序中获取的，该应用程序是在 "创建 Azure Active Directory" 步骤中定义的。
![BMC 配置](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>后续步骤

* [根据 Azure 警报日志创建 ITSM 工作项](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
