---
title: IT 服务管理连接器-安全导出 Azure Monitor
description: 本文介绍如何使用 Azure Monitor 中的安全导出来连接 ITSM 产品/服务，以集中监视和管理 ITSM 工作项。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 9b6180f2480d8a92dc0ebdd2cad474a9eef3cbe4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328847"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>使用安全导出将 Azure 连接到 ITSM 工具

本文说明如何使用安全导出配置 IT Service Management (ITSM) 产品或服务之间的连接。

安全导出是 [IT 服务管理连接器 (ITSMC) ](./itsmc-overview.md)的更新版本。 这两个版本都允许在 Azure Monitor 发送警报时在 ITSM 工具中创建工作项。 此功能包括指标、日志和活动日志警报。

ITSMC 使用用户名和密码凭据。 安全导出具有更强的身份验证，因为它使用 Azure Active Directory (Azure AD) 。 Azure AD 是 Microsoft 的基于云的标识和访问管理服务。 它可帮助用户登录和访问内部或外部资源。 将 Azure AD 与 ITSM 结合使用有助于通过发送到外部系统的 Azure AD 应用程序 ID) 来识别 Azure 警报 (。

> [!NOTE]
> 使用安全导出功能将 Azure 连接到 ITSM 工具的功能处于预览阶段。

## <a name="secure-export-architecture"></a>安全导出体系结构

安全导出体系结构引入了以下新功能：

* **新操作组**：通过安全 Webhook 操作组（而不是 ITSMC 使用的 ITSM 操作组）将警报发送到 ITSM 工具。
* **Azure AD 身份验证**：通过 Azure AD 而不是用户名/密码凭据进行身份验证。

## <a name="secure-export-data-flow"></a>安全导出数据流

安全导出数据流的步骤如下：

1. Azure Monitor 将发送配置为使用安全导出的警报。
1. 警报负载由安全 Webhook 操作发送到 ITSM 工具。
1. 如果警报有权进入 ITSM 工具，ITSM 应用程序会检查 Azure AD。
1. 如果警报已获得授权，则应用程序：
   
   1. 创建工作项 (例如，ITSM 工具中) 事件。
   1.  (CMDB) 将配置项目的 ID （ (CI) 绑定到客户管理数据库）。

![显示 ITSM 工具如何与 Azure A D、Azure 警报和操作组通信的示意图。](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="connection-with-bmc-helix"></a>与 BMC Helix 连接

安全导出支持 BMC Helix。 集成的一些优点包括：

* **更好的身份验证**： Azure AD 提供更安全的身份验证，而不会发生 ITSMC 中经常发生的超时。
* **ITSM 工具中解决的警报**：指标警报实现 "已触发" 和 "已解决" 状态。 满足条件时，警报状态为 "已激发"。 当不再满足条件时，警报状态为 "已解决"。 在 ITSMC 中，无法自动解决警报。 通过安全导出，已解决状态流向 ITSM 工具，因此会自动更新。
* **[常见警报架构](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)**：在 ITSMC 中，警报负载的架构根据警报类型的不同而异。 在安全导出中，所有警报类型都有一个通用的架构。 此常见架构包含适用于所有警报类型的 CI。 所有警报类型都可以与 CMDB 绑定其 CI。

使用以下步骤开始使用 ITSM 连接器工具：

1. 将应用注册到 Azure AD。
2. 创建安全 Webhook 操作组。
3. 配置合作伙伴环境。

## <a name="register-with-azure-active-directory"></a>注册到 Azure Active Directory

请按照以下步骤将应用程序注册到 Azure AD：

1. 按照 [向 Microsoft 标识平台注册应用程序](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)中的步骤操作。
1. 在 Azure AD 中，选择 " **公开应用程序**"。
1. 选择 "为**应用程序 ID URI****设置**"。

   [![用于设置应用程序 I D 的 U R I 的选项的屏幕截图。](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
1. 选择“保存”。

## <a name="create-a-secure-webhook-action-group"></a>创建安全 Webhook 操作组

在将应用程序注册到 Azure AD 后，可以使用操作组中的安全 Webhook 操作，基于 Azure 警报在 ITSM 工具中创建工作项。

操作组为 Azure 警报提供模块化且可重用的方法来触发操作。 可以在 Azure 门户中将操作组与指标警报、活动日志警报和 Azure Log Analytics 警报一起使用。
若要了解有关操作组的详细信息，请参阅[在 Azure 门户中创建和管理操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)。

在 BMC Helix 环境中使用以下过程：

1. 登录到 Integration Studio。
1. **从 "Azure 警报**流" 中搜索 "创建事件"。
1. 复制 webhook URL。
   
   ![Integration Studio 中 webhook U R L 的屏幕截图。](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)

若要将 webhook 添加到操作，请按照以下安全 Webhook 说明操作：

1. 在 [Azure 门户](https://portal.azure.com/)中，搜索并选择“监视”。 “监视”窗格将所有监视设置和数据合并到一个视图中。
1. 选择 "**警报**" "  >  **管理操作**"。
1. 选择“添加操作组”，并填写字段。
1. 在“操作组名称”框中输入名称，然后在“短名称”框中输入名称。 使用此组发送通知时，短名称被用来代替完整的操作组名称。
1. 选择 " **安全 Webhook**"。
1. 选择以下详细信息：
   1. 选择您注册的 Azure Active Directory 实例的对象 ID。
   1. 在 "URI" 中，粘贴从 BMC Helix 环境复制的 webhook URL。
   1. 将 **"启用公用警报架构** " 设置为 **"是"**。 

   下图显示了示例安全 Webhook 操作的配置：

   ![显示安全 Webhook 操作的屏幕截图。](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-the-partner-environment"></a>配置合作伙伴环境

### <a name="connect-bmc-helix-to-azure-monitor"></a>将 BMC Helix 连接到 Azure Monitor

以下部分提供有关如何在 Azure 中连接 BMC Helix 产品和安全导出的详细信息。

### <a name="prerequisites"></a>先决条件

确保满足以下先决条件：

* 已注册 Azure AD。
* 你具有受支持的 BMC Helix 版本19.08 或更高版本 (版本或更高版本) 。

### <a name="configure-the-bmc-helix-connection"></a>配置 BMC Helix 连接

1. 按照 accoring 中的说明进行操作：
   * 正在[为版本20.02 启用预置与 Azure Monitor 的集成](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)。
   * 正在[为版本19.11 启用预置与 Azure Monitor 的集成](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html)。

1. 在 BMC Helix 中的连接配置过程中，请进入你的集成 BMC 实例，并按照以下说明进行操作：

   1. 选择 " **目录**"。
   1. 选择 " **Azure 警报**"。
   1. 选择 " **连接器**"。
   1. 选择 " **配置**"。
   1. 选择 " **添加新的连接** 配置"。
   1. 填写配置部分中的信息：
      - **名称**：构成自己的。
      - **授权类型**： **无**
      - **说明**：自行创建。
      - **站点**： **云**
      - **实例数**： **2**（默认值）。
      - **选中**：默认情况下选择此选项可以启用使用。
      - Azure 租户 ID 和 Azure 应用程序 ID 取自前面定义的应用程序。

![显示 BMC 配置的屏幕截图。](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>后续步骤

* [根据 Azure 警报日志创建 ITSM 工作项](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview)
