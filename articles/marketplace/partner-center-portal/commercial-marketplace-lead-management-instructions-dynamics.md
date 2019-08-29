---
title: 适用于客户参与的 Dynamics 365 的潜在客户管理 |Azure Marketplace
description: 为 Customer Engagement 配置 Dynamics 365 的潜在客户管理。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 03d67249ef1d74844a7e9019e90f6331ecfdf0c3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102977"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>为 Customer Engagement 配置 Dynamics 365 的潜在客户管理

本文介绍如何为客户参与 (以前的 Dynamics CRM Online) 设置 Dynamics 365, 详细了解[此处](https://docs.microsoft.com/dynamics365/customer-engagement/admin/switch-dynamics-crm-online-dynamics-365)的更改, 以便处理 marketplace 产品的销售潜在顾客。 

>[!Note]
>这些说明特定于 Microsoft 托管的 cloud Dynamics 365 for Customer Engagement 环境。 当前不支持直接连接到 Dynamics 本地环境, 有其他选项可用于接收潜在顾客, 如配置[https 终结点](./commercial-marketplace-lead-management-instructions-https.md)或[Azure 表](./commercial-marketplace-lead-management-instructions-azure-table.md)以接收潜在顾客。

## <a name="prerequisites"></a>先决条件

要完成本文中的步骤, 需要以下用户权限:

* 你需要成为 Dynamics 365 上的管理员才能安装解决方案, 并按照这些说明操作。
* 你需要成为租户管理员, 才能为用于从 marketplace 产品/服务发送销售线索的潜在客户服务创建新的服务帐户。
* 需要具有对 Office 365 管理门户的访问权限。
* 需要具有对 Azure 门户的访问权限。

## <a name="install-the-solution"></a>安装解决方案

1.  下载[Microsoft 市场的潜在客户编写器解决方案](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip), 并将其保存到计算机本地。

2.  通过导航到 Dynamics 实例的 URL (如`https://tenant.crm.dynamics.com`), 打开 dynamics 365 以供客户参与。

3.  通过选择顶部导航栏上的 "齿轮" 图标和 "**高级设置**" 访问设置。
 
    ![Dynamics-高级设置](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  在 "设置" 页上, 访问顶部导航栏中的 "设置" 菜单, 然后选择 "**解决方案**"。

    >[!Note]
    >如果在下一屏幕捕获中看不到这些选项, 则没有所需的权限。 联系 Dynamics 365 for Customer Engagement 实例的管理员。

    ![Dynamics 365-解决方案](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. 在 "解决方案" 页上, 选择 "**导入**", 然后导航到你在步骤1中下载的*Microsoft 市场潜在客户编写器*解决方案的保存位置。

    ![Dynamics 365 for Customer Engagement-Import](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. 按照导入解决方案向导完成导入解决方案。

## <a name="configure-user-permissions"></a>配置用户权限

若要将潜在顾客写入 Dynamics 365 for Customer Engagement 实例, 你必须与我们共享服务帐户, 并配置帐户的权限。

使用以下步骤创建服务帐户并分配权限。 可以使用 **Azure Active Directory** 或 **Office 365**。

>[!Note]
>根据你选择的身份验证选项, 可以根据你的选择跳到相应的说明。 请参阅[Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory)或[Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365)。

### <a name="azure-active-directory"></a>Azure Active Directory

建议选择此选项, 因为你可以获得不需要更新用户名/密码以继续接收线索的额外权益。 若要使用 Azure Active Directory 选项, 请从 Active Directory 应用程序提供应用程序 ID、应用程序密钥和目录 ID。

使用以下步骤为 Dynamics 365 配置客户参与 Azure Active Directory。

1. 登录到 " [Azure 门户](https://portal.azure.com/)", 然后从左侧导航栏中选择 "Azure Active Directory" 服务。

2. 从 Azure Active Directory 左侧导航栏中选择 "**属性**", 然后复制该页面上的 "**目录 ID** " 值。 保存此值, 因为它是你需要在发布门户中提供以便接收适用于 marketplace 产品/服务的潜在客户的*目录 ID*值。

    ![Azure Active Directory-属性](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. 从 Azure Active Directory 左侧导航栏中选择 "**应用注册**", 然后在该页上选择 "**新注册**"。
4. 输入应用程序名称的名称。 提供有意义的应用程序名称。
5. 在 "支持的帐户类型" 下, 选择**任何组织目录中的帐户**。
6. 在 "重定向 URI" 下, 选择 " **Web** " 并`https://contosoapp1/auth`提供 URI (如)。 
7. 选择“注册”。

    ![注册应用程序](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. 现在, 你的应用程序已注册, 请访问应用程序的 "概述" 页, 并复制该页上的**应用程序 (客户端) ID**值。 保存此值, 因为它是你需要在发布门户中提供的*应用程序 (客户端) ID*值, 并且可以通过 Dynamics 接收 marketplace 产品/服务的潜在顾客。

    ![应用程序 (客户端) ID](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. 从应用的左侧导航栏中选择 "**证书和机密**", 然后在该页上选择 "**新建客户端密码**"。 为客户端密码输入有意义的说明, 并选择 "过期" 下的 "**从不**" 选项。 选择 "**添加**" 创建客户端密钥。

    ![应用程序证书和机密](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. 成功创建客户端密码后, 请立即**复制客户端密钥值**。 离开页面后, 将无法检索该值。 保存此值, 因为它是*客户端密钥*值, 需要在发布门户中提供才能接收 marketplace 产品的潜在顾客。 
11. 从 "应用" 左侧导航栏中选择 " **API 权限**", 然后选择 "**添加权限**"。
12. 选择 "Microsoft Api", 并选择 " **DYNAMICS CRM** " 作为 API。
13. 在*应用程序所需的权限类型*下, 确保已选择 "**委派权限**"。 以组织用户的身份检查**user_impersonation** *Access Common Data Service*的权限。 选择“添加权限”。

    ![添加权限](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. 完成 Azure 门户上的步骤1-13 后, 导航到 URL (如`https://tenant.crm.dynamics.com`), 导航到 Dynamics 365 以获取客户参与实例。
15. 通过选择齿轮图标和顶部导航栏上的 "**高级设置**" 来访问设置。
16. 在 "设置" 页上, 访问顶部导航栏中的 "设置" 菜单, 然后选择 "**安全性**"。
17. 在 "安全" 页上, 选择 "**用户**"。  在 "用户" 页上, 选择 "启用的用户" 下拉列表以切换到**应用程序用户**。
18. 选择“新建”以创建新用户。 

    ![创建新用户](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. 在 "**新用户**" 中, 确保用户:选择 "应用程序用户"。 提供要与此连接一起使用的用户的用户名、全名和电子邮件地址。 同时, 粘贴到步骤8的 Azure 门户中创建的应用的**应用程序 ID** 。 选择 "**保存并关闭**" 以完成添加用户的工作。

    ![新建用户](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. 转到本文的“安全设置”部分，以完成此用户的连接配置。

### <a name="office-365"></a>Office 365

如果不想使用 Azure Active Directory, 则可以在*Microsoft 365 管理中心*注册新用户。 必须每隔 90 天更新用户名/密码才能持续获取潜在顾客。

使用以下步骤为 Dynamics 365 配置 Office 365 以供客户参与。

1. 登录到[Microsoft 365 管理中心](https://admin.microsoft.com)。

2. 选择“添加用户”。

    ![Microsoft 365 管理中心-添加用户](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. 创建潜在客户编写器服务的新用户。 配置以下设置：

    * 提供用户名
    * 提供密码, 并取消选中 "使此用户在首次登录时更改其密码" 选项。
    * 选择“用户(无管理员权限)”作为用户的角色。
    * 选择 "Dynamics 365 Customer Engagement 计划", 作为下一个屏幕捕获中显示的产品许可证。 需要支付所选许可证的费用。 

保存这些值, 因为它们是需要在发布门户中提供的*用户名和密码*值, 用于接收 marketplace 产品/服务的潜在顾客。

![Microsoft 365 管理中心-新用户](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>安全设置

最后一步是使创建的用户能够写入潜在顾客。

1. 通过导航到 Dynamics 实例的 URL (如`https://tenant.crm.dynamics.com`), 打开 dynamics 365 以供客户参与。
2. 通过选择顶部导航栏上的 "齿轮" 图标和 "**高级设置**" 访问设置。
3. 在 "设置" 页上, 访问顶部导航栏中的 "设置" 菜单, 然后选择 "**安全性**"。
4. 在 "安全" 页上, 选择 "**用户**", 然后选择你在本文档的 "配置用户权限" 部分中创建的用户, 然后选择 "**管理角色**"。 

    ![管理角色](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. 搜索角色名称 "Microsoft 市场组长编写者", 然后选择它以向用户分配角色。

    ![管理用户角色](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >此角色由导入的解决方案所创建，仅拥有写入潜在顾客以及跟踪解决方案版本以确保兼容性的权限。

6. 导航回 "安全" 页, 然后选择 "**安全角色**"。 搜索 "Microsoft 市场潜在客户编写者" 角色, 并将其选中。

    ![安全角色](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. 在安全角色中, 选择 "**核心记录**" 选项卡。搜索 "用户实体 UI 设置" 实体, 并通过单击每个对应的圆圈为用户启用 "创建"、"读取" 和 "写入" 权限 (1/4 黄色圆圈)。

    ![Microsoft 市场 Lead 编写人员核心记录](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. 现在导航到 "**自定义**" 选项卡。搜索 tor "系统作业" 实体, 通过在每个相应的圆圈中单击四次, 为该实体启用 "读取"、"写入" 和 "AppendTo" 权限 (以绿色为绿色)。

    ![Microsoft 市场主管编写人员-自定义](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **保存并关闭**。

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>将产品/服务配置为向 Dynamics 365 发送客户参与

准备好在发布门户中为产品/服务配置潜在顾客管理信息时, 请执行以下步骤:

1. 导航到产品/服务的 "**产品/服务设置**" 页。
2. 选择 "潜在顾客管理" 部分下的 "**连接**"。

    ![连接到潜在客户管理](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. 在 "连接详细信息" 弹出窗口中, 为 "潜在顾客目标" 的 "**客户参与" 选择 Dynamics 365**

    ![连接详细信息-潜在客户目标](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. 提供**Dynamics 365 实例 URL** , `https://contoso.crm4.dynamics.com`如。
5. 选择**身份验证**、Azure Active Directory 或 Office 365 的方法。 
6. 如果选择了 "Azure Active Directory", 请提供**应用程序 (客户端) ID** (例如: `23456052-aaaa-bbbb-8662-1234df56788f`)、**目录 ID** (示例`12345678-8af1-4asf-1234-12234d01db47`:) 和**客户端密码**(示例`1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`:)。

    ![连接详细信息-Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. 如果选择了 Office 365, 请提供**用户名**(例如: `contoso@contoso.onmicrosoft.com`) 和密码 (例如: `P@ssw0rd`)。

    ![连接详细信息-用户名](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>您必须先完成该产品/服务的其余部分的配置, 然后发布该产品/服务, 才能接收该产品/服务的潜在客户。
