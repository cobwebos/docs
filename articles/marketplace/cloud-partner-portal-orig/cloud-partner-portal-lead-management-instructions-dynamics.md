---
title: Dynamics CRM |Azure Marketplace
description: 为 Dynamics CRM 配置潜在顾客管理。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 4ccc8b85e72a4da3b0e640abcc70d24b7cdc54af
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825262"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>为 Dynamics CRM Online 配置潜在顾客管理

本文介绍如何设置 Dynamics CRM Online 以处理销售潜在顾客。

## <a name="prerequisites"></a>先决条件

完成本文中的步骤需要以下用户权限：
- 需是 Dynamics CRM Online 实例上的管理员才能安装解决方案。
- 需是租户管理员才能为潜在顾客服务创建新的服务帐户。

<a name="install-the-solution"></a>安装解决方案
--------------------

1.  下载 [Microsoft 市场潜在顾客写入器解决方案](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)并将其保存在本地。

2.  打开 Dynamics CRM Online 并转到“设置”。
    >[!NOTE]
    >如果未看到以下屏幕截图中所示的选项，则表示你没有所需的权限。
 
       ![Dynamics 设置视图](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  选择“导入”，然后选择在步骤 1 中下载的解决方案。
 
    ![Dynamics 导入选项](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  完成安装解决方案。

## <a name="configure-user-permissions"></a>配置用户权限

若要将潜在顾客写入 Dynamics CRM 实例，必须与我们共享一个服务帐户，并为该帐户配置权限。

使用以下步骤创建服务帐户并分配权限。 可以使用 **Azure Active Directory** 或 **Office 365**。

### <a name="azure-active-directory"></a>Azure Active Directory

我们建议使用此选项，因为这可以获得一个额外的好处：无需更新用户名/密码就能持续获取潜在顾客。 若要使用 Azure Active Directory 选项，请提供 Active Directory 应用程序中的应用 ID、应用程序密钥和目录 ID。

使用以下步骤为 Dynamics CRM 配置 Azure Active Directory。

1.  登录到 [Azure 门户](https://portal.azure.com/)，然后选择“Azure Active Directory”服务。

2.  选择 "**属性**"，然后复制 "**目录 Id**"。这是你需要在云合作伙伴门户中使用的租户帐户标识。

    ![获取目录 ID](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  选择“应用注册”，然后选择“新建应用程序注册”。
4.  输入应用程序名称。
5.  对于“类型”，请选择“Web 应用/API”。
6.  提供 URL。 不需要为潜在顾客填写此字段，但若要创建应用程序，则必须填写。
7. 选择“创建”。
8.  现在，已注册应用程序，选择 "**属性**"，然后选择 **"复制应用程序 Id"** 。您将在云合作伙伴门户中使用此连接信息。
9.  在“属性”中，将应用程序设置为“多租户”，然后选择“保存”。

10. 选择“密钥”，并创建“持续时间”设置为“永不过期”的新密钥。 选择“保存”以创建密钥。 
11. 在“密钥”菜单中，选择“复制密钥值”。 保存此值的副本，因为在云合作伙伴门户中需要使用它。
    
    ![在 Dynamics 中获取注册的密钥](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. 依次选择“所需权限”、“添加”。 
13. 选择“Dynamics CRM Online”作为新 API，并选中“以组织用户身份访问 CRM Online”权限。

14. 在 Dynamics CRM 中转到“用户”，并选择“已启用的用户”下拉列表以切换到“应用程序用户”。
    
    ![应用程序用户](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. 选择“新建”以创建新用户。 选择“用户: 应用程序用户”下拉列表。
    
    ![添加新应用程序用户](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. 在“新建用户”中，提供要用于此连接的名称和电子邮件。 粘贴在 Azure 门户中创建的应用的“应用程序 ID”。

     ![配置新用户](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. 转到本文的“安全设置”部分，以完成此用户的连接配置。

### <a name="office-365"></a>Office 365

如果不想使用 Azure Active Directory，则可以在*Microsoft 365 管理中心*注册新用户。 必须每隔 90 天更新用户名/密码才能持续获取潜在顾客。

使用以下步骤为 Dynamics CRM 配置 Office 365。

1. 登录到[Microsoft 365 管理中心](https://admin.microsoft.com)。

2. 选择 "**管理员**" 磁贴。

    ![Office Online 管理](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. 选择“添加用户”。

    ![添加用户](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. 创建潜在客户编写器服务的新用户。 配置以下设置：

    -   提供密码，并取消选中“使此用户在首次登录时更改其密码”选项。
    -   选择“用户(无管理员权限)”作为用户的角色。
    -   选择以下屏幕截图中所示的产品许可证。 需要支付所选许可证的费用。 该解决方案也可使用 Dynamics CRM Online Basic 许可证。
    
    ![配置用户权限和许可证](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>安全设置

最后一步是使创建的用户能够写入潜在顾客。

1.  登录到 Dynamics CRM Online。
2.  在“设置”中选择“安全性”。
    
    ![安全设置](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  选择在“用户权限”中创建的用户，然后选择“管理用户角色”。 选中“Microsoft 市场潜在顾客写入者”以分配该角色。

    ![分配用户角色](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >此角色由导入的解决方案所创建，仅拥有写入潜在顾客以及跟踪解决方案版本以确保兼容性的权限。

4.  在“安全性”中，选择“安全角色”并找到“Microsoft 市场潜在顾客写入者”角色。
    
    ![配置安全组长编写器](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. 选择 "**核心记录**" 选项卡。启用用户实体 UI 的 "创建/读取/写入"。

    ![为用户启用创建/读取/写入权限](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>总结

将生成的帐户信息添加到云合作伙伴门户，完成 Dynamics CRM 的潜在顾客管理配置。 例如：

-   **Azure Active Directory** - **应用程序 ID**（示例：*23456052-aaaa-bbbb-8662-1234df56788f*）、**目录 ID**（示例：*12345678-8af1-4asf-1234-12234d01db47*）和**应用程序密钥**（示例：*1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=* ）。
-   **Office 365** - **URL**（示例： *https://contoso.crm4.dynamics.com* ）、**用户名**（示例：*contoso\@contoso.onmicrosoft.com*）和**密码**（示例：*P\@ssw0rd*）。
