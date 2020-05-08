---
title: 针对 Dynamics 365 的潜在客户管理-Microsoft 商业市场
description: 了解如何设置 Dynamics 365 Customer Engagement，以管理来自 Microsoft AppSource 和 Azure Marketplace 的潜在客户。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 985b3258eb0b957242d529945f32ed9704a91e7d
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790994"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>为 Dynamics 365 客户参与配置潜在客户管理

本文介绍如何设置 Dynamics 365 Customer Engagement （以前称为 Dynamics CRM Online）。 详细了解如何将[基于服务器的身份验证配置为通过客户服务和 SharePoint Online](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online)来处理商业 marketplace 产品/服务的销售线索。

>[!NOTE]
>这些说明特定于 Microsoft 托管的云环境，适用于 Dynamics 365 客户参与。 当前不支持直接连接到 Dynamics 本地环境。 还有其他选项可用于接收潜在顾客，如配置[HTTPS 终结点](./commercial-marketplace-lead-management-instructions-https.md)或[Azure 表](./commercial-marketplace-lead-management-instructions-azure-table.md)。

## <a name="prerequisites"></a>先决条件

需要以下用户权限才能完成本文中的步骤：

* Dynamics 365 Customer Engagement 实例的管理员权限才能安装解决方案。
* 租户管理员权限，用于为使用商业 marketplace 产品/服务的潜在客户创建新的服务帐户。
* 对 Office 365 管理门户的访问权限。
* 对 Azure 门户的访问权限。

## <a name="install-the-solution"></a>安装解决方案

1. 下载[Microsoft 市场的潜在客户编写器解决方案](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)，并将其保存在本地计算机上。

1. 转到 Dynamics 实例的 URL，打开 Dynamics 365 Customer Engagement，如`https://tenant.crm.dynamics.com`。

1. 选择顶部栏上的齿轮图标，然后选择 "**高级设置**"。
 
    ![Dynamics 365 高级设置菜单项](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. 在 "**设置**" 页上，打开顶部栏上的 "**设置**" 菜单，然后选择 "**解决方案**"。

    >[!NOTE]
    >如果在下面的屏幕中看不到这些选项，则没有所需的权限。 请联系 Dynamics 365 Customer Engagement 实例上的管理员。

    ![Dynamics 365 解决方案选项](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. 在 "**解决方案**" 页上，选择 "**导入**"，然后前往你在步骤1中下载的**Microsoft 市场潜在客户编写器**解决方案的保存位置。

    ![“导入”按钮](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. 按照导入解决方案向导完成导入解决方案。

## <a name="configure-user-permissions"></a>配置用户权限

若要将潜在顾客写入 Dynamics 365 Customer Engagement 实例，你必须与 Microsoft 共享服务帐户，并配置帐户的权限。

使用以下步骤创建服务帐户并分配权限。 可以使用 Azure Active Directory 或 Office 365。

>[!NOTE]
>根据所选的身份验证选项跳到相应的说明。 请参阅[Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory)或[Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365)。

### <a name="azure-active-directory"></a>Azure Active Directory

建议选择此选项，因为您永远都不需要更新您的用户名或密码即可继续获得潜在顾客。 若要使用 Azure Active Directory 选项，请从 Active Directory 应用程序提供应用程序 ID、应用程序密钥和目录 ID。

若要为 Dynamics 365 客户参与配置 Azure Active Directory：

1. 登录 [Azure 门户](https://portal.azure.com/)。 在左窗格中选择“Azure Active Directory”****。

1. 选择 "**属性**"，并复制 "**目录属性**" 页上的 "**目录 ID** " 值。 保存此值，因为你需要在发布门户中提供它才能接收 marketplace 产品/服务的潜在顾客。

    ![Azure Active Directory 属性 "菜单项](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. 从 Azure Active Directory 左窗格中选择 "**应用注册**"，然后在该页上选择 "**新建注册**"。
1. 为应用程序名称输入有意义的名称。
1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户”。  
1. 在 "**重定向 URI （可选）**" 下，选择 " **Web** " 并`https://contosoapp1/auth`输入 URI，例如。 
1. 选择“注册”  。

    !["注册应用程序" 页](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. 现在，你的应用程序已注册，请访问应用程序的 "概述" 页。 复制该页面上的**应用程序（客户端） ID**值。 请保存此值，因为你需要在发布门户和 Dynamics 365 中提供此值以接收 marketplace 产品/服务的潜在顾客。

    ![应用程序（客户端） ID 框](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. 从应用的左窗格中选择 "**证书" & 机密**，然后选择 "**新建客户端密钥**" 按钮。 为客户端密码输入有意义的说明，并选择 "**过期**" 下的 "**从不**" 选项。 选择 "**添加**" 创建客户端密钥。

    ![证书 & 机密菜单项](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. 成功创建客户端密码后，请立即复制**客户端密钥**值。 离开页面后，将无法检索值。 保存此值，因为你需要在发布门户中提供它才能接收 marketplace 产品/服务的潜在顾客。 
1. 从应用的左窗格中选择 " **API 权限**"，然后选择 " **+ 添加权限**"。
1. 选择 " **Microsoft api**"，并选择 " **Dynamics CRM** " 作为 API。
1. 在 "**你的应用程序需要何种类型的权限？**" 下，确保选择 "**委派权限**"。 
1. 在 "**权限**" 下，选中 " **user_impersonation** " 复选框**作为组织用户访问 Common Data Service**。 然后选择“添加权限”****。

    !["添加权限" 按钮](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. 完成 Azure 门户中的步骤1至14后，请转到 URL，转到 Dynamics 365 Customer Engagement 实例，如`https://tenant.crm.dynamics.com`。
1. 选择顶部栏上的齿轮图标，然后选择 "**高级设置**"。
1. 在 "**设置**" 页上，打开顶部栏上的 "**设置**" 菜单，然后选择 "**安全性**"。
1. 在 "**安全**" 页上，选择 "**用户**"。 在 "**用户**" 页上，选择 "**启用的用户**" 下拉箭头，然后选择 "**应用程序用户**"。
1. 选择“新建”以创建新用户。**** 

    ![创建新用户](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. 在 "**新建用户**" 窗格中，确保已选择 "**用户：应用程序用户**"。 提供要与此连接一起使用的用户的用户名、全名和电子邮件地址。 同时，粘贴到步骤8的 Azure 门户中创建的应用的**应用程序 ID** 。 选择 "**保存" & "关闭**" 以完成添加用户。

    ![新用户窗格](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. 请参阅本文中的 "安全设置" 一节来完成为此用户配置连接的操作。

### <a name="office-365"></a>Office 365

如果不想使用 Azure Active Directory，则可以在 Microsoft 365 管理中心注册新用户。 每90天都需要更新用户名和密码，以便继续获得潜在顾客。

若要为 Dynamics 365 客户参与配置 Office 365：

1. 登录 [Microsoft 365 管理中心](https://admin.microsoft.com)。

1. 选择“添加用户”。****

    ![Microsoft 365 管理中心添加用户选项](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. 创建潜在客户编写器服务的新用户。 配置下列设置：

    * 输入用户名。
    * 输入密码，并清除 "**使此用户在首次登录时更改其密码**" 选项。
    * 选择 "**用户（无管理员访问权限）** " 作为该用户的角色。
    * 选择**Dynamics 365 Customer Engagement 计划**作为产品许可证，如以下屏幕中所示。 需要支付所选许可证的费用。 

保存这些值，因为你需要在发布门户中提供**用户名**和**密码**值才能接收适用于 marketplace 产品/服务的潜在顾客。

![Microsoft 365 管理中心新用户窗格](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>安全设置

最后一步是使创建的用户能够编写潜在顾客。

1. 转到 Dynamics 实例的 URL，打开 Dynamics 365 Customer Engagement，如`https://tenant.crm.dynamics.com`。
1. 选择顶部栏上的齿轮图标，然后选择 "**高级设置**"。
1. 在 "**设置**" 页上，打开顶部栏上的 "**设置**" 菜单，然后选择 "**安全性**"。
1. 在 "**安全**" 页上，选择 "**用户**"，然后选择你在本文档的 "配置用户权限" 部分中创建的用户。 然后选择 "**管理角色**"。 

    ![管理角色选项卡](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. 在**潜在客户编写器 Microsoft 市场**搜索角色名称，然后选择该角色以向用户分配角色。

    ![管理用户角色窗格](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >此角色由导入的解决方案所创建，仅拥有写入潜在顾客以及跟踪解决方案版本以确保兼容性的权限。

1. 返回到 "**安全**" 页，然后选择 "**安全角色**"。 搜索角色**Microsoft 市场主导作者**，并选择该角色。

    ![安全角色窗格](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. 在 "安全角色" 中，选择 "**核心记录**" 选项卡。搜索 "**用户实体" UI 设置**项。 通过在每个相应的圆圈中单击一次，为用户启用 "创建"、"读取" 和 "写入" 权限（1/4 黄色圆圈）。

    ![Microsoft 市场潜在客户编写核心记录 "选项卡](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. 在 "**自定义**" 选项卡上，搜索 "**系统作业**" 项。 通过在每个相应的圆圈中单击四次，为该实体启用 "读取"、"写入" 和 "AppendTo" 权限（绿色圆圈）。

    ![Microsoft 市场潜在客户编写器自定义选项卡](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. 选择 "**保存并关闭**"。

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>配置你的产品/服务以将潜在顾客发送到 Dynamics 365 客户参与 

在发布门户中配置产品/服务的潜在客户管理信息：

1. 请参阅产品/服务的**产品/服务设置**页。
1. 选择 "**潜在顾客管理**" 部分下的 "**连接**"。

    ![潜在客户管理部分连接按钮](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

1. 在 "连接详细信息" 弹出窗口中，为潜在顾客目标选择 " **Dynamics 365 Customer Engagement** "。

    ![潜在顾客目标框](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. 输入 Dynamics 365 实例的**URL** ，例如`https://contoso.crm4.dynamics.com`。

1. 选择**身份验证**方法，Azure Active Directory 或 Office 365。 
1. 如果选择了 " **Azure Active Directory**"，请输入**应用程序（客户端） ID** （例如`23456052-aaaa-bbbb-8662-1234df56788f`，）、**目录 ID** （ `12345678-8af1-4asf-1234-12234d01db47`例如）和**客户端密码**（例如`1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`）。

    ![选择 Azure Active Directory 的身份验证](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. 如果选择了**Office 365**，请输入**用户名**（例如`contoso@contoso.onmicrosoft.com`）和**密码**（例如`P@ssw0rd`）。

    ![Office 365 用户名框](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. 对于 "**联系人电子邮件**"，请输入公司中应在收到新潜在客户时收到电子邮件通知的人员的电子邮件地址。 您可以输入多个电子邮件地址，用分号分隔。
1. 选择“确定”  。

若要确保已成功连接到潜在客户目标，请选择 "**验证**" 按钮。 如果成功，则在潜在客户目标中会出现测试主管。

![联系人电子邮件框](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>您必须先完成该产品/服务的其余部分的配置，然后发布该产品/服务，才能接收该产品/服务的潜在客户。
