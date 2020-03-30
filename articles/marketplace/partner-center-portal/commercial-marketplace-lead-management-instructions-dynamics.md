---
title: Dynamics 365 客户参与的潜在客户管理 |Azure 应用商店
description: 为客户参与度配置 Dynamics 365 的潜在客户管理。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dsindona
ms.openlocfilehash: 8af6b3a451d20bcc9cab3fa4adb9643f82b85e49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288812"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>为客户参与度配置 Dynamics 365 的潜在客户管理

本文介绍如何为客户参与度设置 Dynamics 365（以前动态 CRM 在线），[在此处](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online)阅读有关处理市场产品销售线索的更改。 

>[!Note]
>这些说明特定于 Microsoft 托管的云动态 365，用于客户参与环境。 当前不支持直接连接到 Dynamics on-prem 环境，还有其他选项可供您接收潜在顾客，例如配置[https 终结点](./commercial-marketplace-lead-management-instructions-https.md)或[Azure 表](./commercial-marketplace-lead-management-instructions-azure-table.md)以接收潜在顾客。

## <a name="prerequisites"></a>先决条件

完成本文中的步骤需要以下用户权限：

* 您需要成为 Dynamics 365 客户参与实例上的管理员，以便能够安装解决方案并遵循这些说明。
* 您需要是租户管理员，才能为用于从市场产品/服务发送潜在顾客的潜在顾客服务创建新的服务帐户。
* 您需要有权访问 Office 365 管理门户。
* 您需要有权访问 Azure 门户。

## <a name="install-the-solution"></a>安装解决方案

1.  下载[Microsoft 应用商店首席编写器解决方案](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)，并将其本地保存到您的计算机。

2.  通过导航到 Dynamics 实例的 URL（如`https://tenant.crm.dynamics.com`），为客户参与打开动态 365。

3.  通过选择顶部导航栏上的齿轮图标和**高级设置**来访问设置。
 
    ![动态 - 高级设置](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  在"设置"页上，从顶部导航栏访问"设置"菜单并选择 **"解决方案**"。

    >[!Note]
    >如果在下一个屏幕截图中看不到这些选项，则您没有继续所需的权限。 联系 Dynamics 365 上的管理员，了解客户参与度实例。

    ![动态 365 - 解决方案](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. 在"解决方案"页上，选择 **"导入"** 并导航到在步骤 1 中下载的*Microsoft 应用商店首席编写器*解决方案保存的位置。

    ![面向客户参与的动态 365 - 导入](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. 按照导入解决方案向导完成导入解决方案。

## <a name="configure-user-permissions"></a>配置用户权限

要将潜在顾客写入 Dynamics 365 的客户参与实例，您必须与我们共享服务帐户，并配置该帐户的权限。

使用以下步骤创建服务帐户并分配权限。 可以使用 **Azure Active Directory** 或 **Office 365**。

>[!Note]
>根据您选择的身份验证选项，您可以根据您的选择跳到相应的说明。 请参阅[Azure 活动目录](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory)或[Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365)。

### <a name="azure-active-directory"></a>Azure Active Directory

我们建议使用此选项，因为您得到了无需更新用户名/密码即可继续获取潜在顾客的额外好处。 要使用 Azure 活动目录选项，请从活动目录应用程序提供应用 ID、应用程序密钥和目录 ID。

使用以下步骤为客户参与配置 Dynamics 365 的 Azure 活动目录。

1. 登录到[Azure 门户](https://portal.azure.com/)，然后从左侧导航中选择 Azure 活动目录服务。

2. 从 Azure 活动目录左侧导航中选择**属性**，并在该页上复制**目录 ID**值。 保存此值，因为需要在发布门户中提供*目录 ID*值，以便接收市场产品/服务的潜在顾客。

    ![Azure 活动目录 - 属性](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. 从 Azure 活动目录左侧导航**中选择应用注册**，然后选择该页上**的新注册**。
4. 输入应用程序名称的名称。 提供有意义的应用程序名称。
5. 在"支持帐户类型"下，选择**任何组织目录中的帐户**。
6. 在重定向 URI 下，选择**Web**并提供 URI（如`https://contosoapp1/auth`）。 
7. 选择“注册”****。

    ![注册应用程序](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. 注册应用程序后，访问应用程序的概览页并复制该页上**的应用程序（客户端）ID**值。 保存此值，因为需要在发布门户和 Dynamics 中提供*应用程序（客户端）ID*值，以便接收市场产品的潜在顾客。

    ![应用程序(客户端) ID](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. 从应用的左侧导航中选择**证书和机密**，然后选择该页上**的"新建客户端机密**"。 输入客户端机密的有意义的说明，然后选择"过期"下的 **"从不"** 选项。 选择 **"添加"** 以创建客户端机密。

    ![应用程序 - 认证和秘密](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. 成功创建客户端密钥后，**立即复制客户端机密值**。 导航离开页面后，您将无法检索该值。 保存此值，因为它是您需要在发布门户中提供的*客户端机密*值，以便接收市场产品的潜在顾客。 
11. 从应用的左侧导航中选择**API 权限**，然后选择 **"添加权限**"。
12. 选择 Microsoft API，然后选择**动态 CRM**作为 API。
13. 在 *"应用程序需要哪些类型的权限*"下，请确保选择了**委派权限**。 检查**user_impersonation***作为组织用户访问公共数据服务*的权限。 选择“添加权限”****。

    ![添加权限](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. 在 Azure 门户上完成步骤 1-13 后，通过导航到 URL（如`https://tenant.crm.dynamics.com`），导航到客户参与实例的动态 365。
15. 通过选择齿轮图标和顶部导航栏上的 **"高级设置"** 来访问设置。
16. 在"设置"页上，从顶部导航栏访问"设置"菜单并选择 **"安全**"。
17. 在"安全"页上一次，选择 **"用户**"。  在"用户"页上，选择"启用用户"下拉列表以切换到**应用程序用户**。
18. 选择“新建”以创建新用户。**** 

    ![创建新用户](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. 在新**用户**中，请确保选择了用户：应用用户。 为要使用此连接的用户提供用户名、全名和电子邮件地址。 此外，请粘贴到步骤 8 中在 Azure 门户中创建的应用**的应用程序 ID**中。 选择 **"保存"和"关闭**"以完成用户添加。

    ![新建用户](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. 转到本文的“安全设置”部分，以完成此用户的连接配置。

### <a name="office-365"></a>Office 365

如果不想使用 Azure 活动目录，可以在*Microsoft 365 管理中心*注册新用户。 必须每隔 90 天更新用户名/密码才能持续获取潜在顾客。

使用以下步骤为 Dynamics 365 为客户参与配置 Office 365。

1. 登录 [Microsoft 365 管理中心](https://admin.microsoft.com)。

2. 选择“添加用户”。****

    ![微软365管理中心 - 添加用户](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. 创建潜在客户编写器服务的新用户。 配置以下设置：

    * 提供用户名
    * 提供密码，并取消选择"让此用户在首次登录时更改其密码"选项。
    * 选择“用户(无管理员权限)”作为用户的角色。
    * 选择"动态 365 客户参与计划"作为下一个屏幕捕获中显示的产品许可证。 需要支付所选许可证的费用。 

保存这些值，因为它们是您在发布门户中接收市场产品的潜在顾客所需的*用户名和密码*值。

![微软365管理中心 - 新用户](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>安全设置

最后一步是使创建的用户能够写入潜在顾客。

1. 通过导航到 Dynamics 实例的 URL（如`https://tenant.crm.dynamics.com`），为客户参与打开动态 365。
2. 通过选择顶部导航栏上的齿轮图标和**高级设置**来访问设置。
3. 在"设置"页上，从顶部导航栏访问"设置"菜单并选择 **"安全**"。
4. 在"安全"页上，选择 **"用户"** 并选择您在本文档的"配置用户权限"部分中创建的用户，然后选择 **"管理角色**"。 

    ![管理角色](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. 搜索角色名称"Microsoft 市场首席编写器"并选择它来分配用户的角色。

    ![管理用户角色](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >此角色由导入的解决方案所创建，仅拥有写入潜在顾客以及跟踪解决方案版本以确保兼容性的权限。

6. 导航回安全页面并选择**安全角色**。 搜索角色"微软市场首席编写者"并选择它。

    ![安全角色](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. 进入安全角色后，选择 **"核心记录**"选项卡。搜索"用户实体 UI 设置"实体，并通过单击一次到每个相应的圈子，为该实体启用该实体的创建、读取和写入权限（1/4 黄色圆圈）。

    ![微软市场首席撰稿人 - 核心记录](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. 现在导航到 **"自定义**"选项卡。搜索 tor"系统作业"实体，并通过单击四次单击每个相应圈子，为该实体启用"读取、写入和追加到组织"权限（纯绿色）。

    ![微软市场首席编写器 - 自定义](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **保存并关闭**。

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>将产品/服务配置为将潜在顾客发送到 Dynamics 365 以进行客户互动

准备好在发布门户中配置产品/服务的首席管理信息时，请按照以下步骤操作：

1. 导航到产品 **/服务"产品/服务"设置**页面。
2. 在"潜在顾客管理"部分下选择 **"连接**"。

    ![连接到潜在客户管理](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. 在"连接详细信息"弹出窗口上，为潜在客户目标**的客户参与度选择 Dynamics 365**

    ![连接详细信息 - 潜在顾客目标](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. 提供**Dynamics 365 实例 URL，** 如`https://contoso.crm4.dynamics.com`。
5. 选择**身份验证**、Azure 活动目录或 Office 365 的方法。 
6. 如果选择 Azure 活动目录，请提供**应用程序（客户端）ID（** 例如`23456052-aaaa-bbbb-8662-1234df56788f`： ），**目录 ID（** 示例：）`12345678-8af1-4asf-1234-12234d01db47`和**客户端密钥**（示例： `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`）。

    ![连接详细信息 - Azure 活动目录](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. 如果选择 Office 365，请提供**用户名**（示例：）`contoso@contoso.onmicrosoft.com`和密码（示例： `P@ssw0rd`）。

    ![连接详细信息 - 用户名](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>您必须完成配置产品/服务的其余部分并发布它，然后才能收到产品/服务的潜在顾客。
