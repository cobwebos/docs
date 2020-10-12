---
title: Dynamics 365 Customer Engagement 的潜在顾客管理 - Microsoft 商业市场
description: 了解如何设置 Dynamics 365 Customer Engagement 以管理来自 Microsoft AppSource 和 Azure 市场的潜在顾客。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2020
ms.openlocfilehash: 31dba5489db3778d738fc2856cf6aacfd6987711
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90030746"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>为 Dynamics 365 Customer Engagement 配置潜在顾客管理

本文介绍如何设置 Dynamics 365 Customer Engagement（以前名为 Dynamics CRM Online）。 在[使用 Customer Engagement 和 SharePoint Online 配置基于服务器的身份验证](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online)中详细了解这一变化，以处理来自商业市场产品/服务的潜在销售顾客。

>[!NOTE]
>这些说明特定于 Microsoft 为 Dynamics 365 Customer Engagement 托管的云环境。 当前不支持直接连接到 Dynamics 本地环境。 有其他选项可供你用于接收潜在顾客，例如配置 [HTTPS 终结点](./commercial-marketplace-lead-management-instructions-https.md)或 [Azure 表](./commercial-marketplace-lead-management-instructions-azure-table.md)。

## <a name="prerequisites"></a>先决条件

完成本文中的步骤需要以下用户权限：

* Dynamics 365 Customer Engagement 实例上能够安装解决方案的管理员权限。
* 为用于从商业市场产品/服务发送潜在顾客的潜在顾客服务创建新服务帐户的租户管理员权限。
* 访问管理门户。
* 对 Azure 门户的访问权限。

## <a name="install-the-solution"></a>安装解决方案

1. 下载 [Microsoft 市场潜在顾客写入器解决方案](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)并在本地将它保存到计算机。

1. 转到 Dynamics 实例的 URL（如 `https://tenant.crm.dynamics.com`），从而打开 Dynamics 365 Customer Engagement。

1. 选择顶部栏上的齿轮图标，然后选择“高级设置”。
 
    ![Dynamics 365“高级设置”菜单项](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. 在“设置”页面上，打开顶部栏上的“设置”菜单，然后选择“解决方案”。

    >[!NOTE]
    >如果未看到以下屏幕中的选项，则表示你没有继续操作所需的权限。 请联系 Dynamics 365 Customer Engagement 实例的管理员。

    ![Dynamics 365“解决方案”选项](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. 在“解决方案”页面上，选择“导入”，然后转到用于保存步骤 1 中下载的 Microsoft 市场潜在顾客写入器解决方案的位置。

    ![“导入”按钮](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. 按照导入解决方案向导完成解决方案导入。

## <a name="configure-user-permissions"></a>配置用户权限

若要将潜在顾客写入 Dynamics 365 Customer Engagement 实例，你必须与 Microsoft 共享一个服务帐户，并为该帐户配置权限。

使用以下步骤创建服务帐户并分配权限。 可以使用 Azure Active Directory 或 Office 365。

>[!NOTE]
>基于所选的身份验证选项跳到对应的说明。 请参阅 [Azure Active Directory](#azure-active-directory) 或 [Office 365](#office-365)。

### <a name="azure-active-directory"></a>Azure Active Directory

建议使用此选项，因为无需更新用户名或密码即可持续获取潜在顾客。 若要使用 Azure Active Directory 选项，请提供 Active Directory 应用程序中的应用 ID、应用程序密钥和目录 ID。

若要为 Dynamics 365 Customer Engagement 配置 Azure Active Directory，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com/)。 在左窗格中选择“Azure Active Directory”。

1. 选择“属性”，然后在“目录属性”页面上复制“目录 ID”值。   保存此值，因为需要在发布门户中提供它才能接收市场产品/服务的潜在顾客。

    ![Azure Active Directory“属性”菜单项](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. 从 Azure Active Directory 左窗格中选择“应用注册”，然后在该页面上选择“新建注册”。
1. 为应用程序名称输入一个有意义的名称。
1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户”。
1. 在“重定向 URI (可选)”下，选择“Web”并输入 URI，如 `https://contosoapp1/auth`。 
1. 选择“注册”。

    ![注册应用程序页面](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. 现在，你的应用程序已注册，请访问应用程序的概述页面。 在该页面上复制“应用程序(客户端) ID”值。 保存此值，因为需要在发布门户和 Dynamics 365 中提供它才能接收市场产品/服务的潜在顾客。

    ![“应用程序(客户端) ID”框](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. 从应用的左窗格中选择“证书和密码”，然后选择“新建客户端密码”按钮。 为客户端密码输入有意义的说明，然后在“过期”下选择“从不”选项。 选择“添加”以创建客户端密码。

    ![“证书和密码”菜单项](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. 成功创建客户端密码后，请立即复制“客户端密码”值。 离开此页之后，你将无法检索到该值。 保存此值，因为需要在发布门户中提供它才能接收市场产品/服务的潜在顾客。 
1. 从应用的左窗格中选择“API 权限”，然后选择“+ 添加权限”。
1. 选择“Microsoft API”，然后选择“Dynamics CRM”作为 API。
1. 在“应用程序需要哪种类型的权限?”下，确保选择“委派的权限”。 
1. 在“权限”下，对于“以组织用户身份访问 Common Data Service”选中“user_impersonation”复选框 。 然后选择“添加权限”。

    ![“添加权限”按钮](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. 在 Azure 门户中完成步骤 1 到 14 之后，通过转到相应 URL（如 `https://tenant.crm.dynamics.com`）来转到 Dynamics 365 Customer Engagement 实例。
1. 选择顶部栏上的齿轮图标，然后选择“高级设置”。
1. 在“设置”页面上，打开顶部栏上的“设置”菜单，然后选择“安全性”。
1. 在“安全性”页面上，选择“用户” 。 在“用户”页面上，选择“启用的用户”下拉菜单，然后选择“应用程序用户”。
1. 选择“新建”以创建新用户。 

    ![创建新用户](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. 在“新建用户”窗格中，确保选择“用户:应用程序用户”。 为要用于此连接的用户提供用户名、全名和电子邮件地址。 此外，粘贴步骤 8 中在 Azure 门户中创建的应用的“应用程序 ID”。 选择“保存并关闭”以完成用户添加。

    ![“新建用户”窗格](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. 转到本文的“安全设置”部分，以完成此用户的连接配置。

### <a name="office-365"></a>Office 365

如果不想使用 Azure Active Directory，可以在 Microsoft 365 管理中心中注册新用户。 必须每 90 天更新一次用户名和密码才能持续获取潜在顾客。

若要为 Dynamics 365 Customer Engagement 配置 Office 365，请执行以下操作：

1. 登录 [Microsoft 365 管理中心](https://admin.microsoft.com)。

1. 选择“添加用户”。

    ![Microsoft 365 管理中心“添加用户”选项](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. 创建潜在客户编写器服务的新用户。 配置以下设置：

    * 输入用户名。
    * 输入密码，并清除“让此用户在首次登录时更改其密码”选项。
    * 选择“用户(没有管理员访问权限)”作为用户的角色。
    * 选择“Dynamics 365 Customer Engagement 计划”作为产品许可证，如以下屏幕中所示。 需要支付所选许可证的费用。 

保存这些值，因为需要在发布门户中提供“用户名”和“密码”才能接收市场产品/服务的潜在顾客。

![Microsoft 365 管理中心“新建用户”窗格](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>安全设置

最后一步是使创建的用户能够写入潜在顾客。

1. 转到 Dynamics 实例的 URL（如 `https://tenant.crm.dynamics.com`），从而打开 Dynamics 365 Customer Engagement。
1. 选择顶部栏上的齿轮图标，然后选择“高级设置”。
1. 在“设置”页面上，打开顶部栏上的“设置”菜单，然后选择“安全性”。
1. 在“安全性”页面上，选择“用户”，然后选择在本文档的“配置用户权限”部分中创建的用户。 然后选择“管理角色”。 

    ![“管理角色”选项卡](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. 搜索角色名称“Microsoft 市场潜在顾客写入器”，然后选择它以向用户分配角色。

    ![“管理用户角色”窗格](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >此角色由导入的解决方案所创建，仅拥有写入潜在顾客以及跟踪解决方案版本以确保兼容性的权限。

1. 返回到“安全性”页面，然后选择“安全角色”。 搜索角色“Microsoft 市场潜在顾客写入器”，然后选择它。

    ![“安全角色”窗格](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. 在安全角色中，选择“核心记录”选项卡。搜索“用户实体 UI 设置”项。 通过在每个对应圆圈中单击一次，为该实体向用户启用“创建”、“读取”和“写入”权限（1/4 个黄色圆圈）。

    ![Microsoft 市场潜在顾客写入器核心记录选项卡](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. 在“自定义”选项卡上，搜索“系统作业”项。 通过在每个对应圆圈中单击四次，为该实体向组织启用“读取”、“写入”和“AppendTo”权限（实心绿色圆圈）。

    ![Microsoft 市场潜在顾客写入器自定义选项卡](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. 选择“保存并关闭”。

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>配置你的产品/服务以将潜在顾客发送到 Dynamics 365 Customer Engagement 

若要在发布门户中为你的产品/服务配置潜在顾客管理信息，请执行以下步骤：

1. 转到你产品/服务的“产品/服务设置”页面。
1. 在“潜在顾客”部分下，选择“连接”。

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-dynamics/customer-leads.png" alt-text="潜在顾客":::

1. 在“连接详细信息”弹出窗口中，为潜在顾客目标选择“Dynamics 365 Customer Engagement”。

    ![潜在顾客目标框](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. 输入 Dynamics 365 实例的 URL，如 `https://contoso.crm4.dynamics.com`。

1. 选择“身份验证”方法（Azure Active Directory 或 Office 365）。 
1. 如果选择了“Azure Active Directory”，请输入“应用程序(客户端) ID”（例如 `23456052-aaaa-bbbb-8662-1234df56788f`）、“目录 ID”（例如 `12345678-8af1-4asf-1234-12234d01db47`）和“客户端密码”（例如 `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`）。

    ![选择使用 Azure Active Directory 进行身份验证](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. 如果选择了“Office 365”，请输入“用户名”（例如 `contoso@contoso.onmicrosoft.com`）和“密码”（例如 `P@ssw0rd`）。

    ![Office 365 用户名框](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. 对应“联系人电子邮件”，为公司中应在收到新潜在顾客时收到电子邮件通知的人员输入电子邮件地址。 可以输入多个电子邮件地址，用分号分隔。
1. 选择“确定”。

若要确保已成功连接到潜在顾客目标，请选择“验证”按钮。 如果成功，则在潜在顾客目标中会出现测试潜在顾客。

![联系人电子邮件框](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>必须先完成产品/服务的其余内容的配置并进行发布，然后才能接收产品/服务的潜在顾客。
