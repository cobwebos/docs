---
title: 在 Windows 虚拟桌面中创建租户 - Azure
description: 介绍如何在 Azure Active Directory 中设置 Windows 虚拟桌面租户。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 94128c69f227ceff51968354048ec6610e3d7c4c
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82613923"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop"></a>教程：在 Windows 虚拟桌面中创建租户

>[!IMPORTANT]
>本教程的内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的 2019 年秋季版。

在 Windows 虚拟桌面中创建租户是生成桌面虚拟化解决方案的第一步。 租户是由一个或多个主机池构成的组。 每个主机池包含多个会话主机，这些主机作为虚拟机在 Azure 中运行，并注册到 Windows 虚拟桌面服务。 每个主机池还包含用于向用户发布远程桌面和远程应用程序资源的一个或多个应用组。 使用租户可以生成主机池、创建应用组、分配用户，以及通过服务建立连接。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 向 Windows 虚拟桌面服务授予 Azure Active Directory 权限。
> * 将 TenantCreator 应用程序角色分配到 Azure Active Directory 租户中的用户。
> * 创建 Windows 虚拟桌面租户。

## <a name="what-you-need-to-set-up-a-tenant"></a>设置租户需要什么技术

在开始设置 Windows 虚拟桌面租户之前，请确保具备以下条件：

* Windows 虚拟桌面用户的 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 租户 ID。
* Azure Active Directory 租户中的全局管理员帐户。
   * 这也适用于要为其客户创建 Windows 虚拟桌面租户的云解决方案提供商 (CSP) 组织。 CSP 组织必须能够以其客户的 Azure Active Directory 实例全局管理员身份登录。
   * 管理员帐户必须源自 Azure Active Directory 租户，你将在该租户中尝试创建 Windows 虚拟桌面租户。 此过程不支持 Azure Active Directory B2B（来宾）帐户。
   * 管理员帐户必须是工作或学校帐户。
* Azure 订阅。

必须准备好租户 ID、全局管理员帐户和 Azure 订阅，以便本教程中所述的过程能够正常工作。

## <a name="grant-permissions-to-windows-virtual-desktop"></a>向 Windows 虚拟桌面授予权限

如果已向 Windows 虚拟桌面授予对此 Azure Active Directory 实例的权限，请跳过本部分。

向 Windows 虚拟桌面服务授予权限可让该服务在 Azure Active Directory 中查询管理任务和最终用户任务。

向该服务授予权限：

1. 打开浏览器并开始使用 [Windows 虚拟桌面服务器应用](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)的管理员同意流。
   > [!NOTE]
   > 如果你管理客户并需要为客户的目录授予管理员同意，请在浏览器中输入以下 URL，并将 {tenant} 替换为客户的 Azure AD 域名。 例如，如果客户的组织已注册了 Azure AD 域名 contoso.onmicrosoft.com，请将 {tenant} 替换为 contoso.onmicrosoft.com。
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. 使用全局管理员帐户登录到 Windows 虚拟桌面许可页。 例如，如果你所在的组织是 Contoso，则你的帐户可能是 admin@contoso.com 或 admin@contoso.onmicrosoft.com。
3. 选择“接受”  。
4. 等待一分钟，以便 Azure AD 可以记录同意。
5. 打开浏览器并开始使用 [Windows 虚拟桌面客户端应用](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)的管理员同意流。
   >[!NOTE]
   > 如果你管理客户并需要为客户的目录授予管理员同意，请在浏览器中输入以下 URL，并将 {tenant} 替换为客户的 Azure AD 域名。 例如，如果客户的组织已注册了 Azure AD 域名 contoso.onmicrosoft.com，请将 {tenant} 替换为 contoso.onmicrosoft.com。
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. 像在步骤 2 中一样，以全局管理员的身份登录到 Windows 虚拟桌面许可页。
7. 选择“接受”  。

## <a name="assign-the-tenantcreator-application-role"></a>分配 TenantCreator 应用程序角色

为 Azure Active Directory 用户分配 TenantCreator 应用程序角色可让该用户创建与 Azure Active Directory 实例关联的 Windows 虚拟桌面租户。 需要使用全局管理员帐户分配 TenantCreator 角色。

若要分配 TenantCreator 应用程序角色：

1. 转到 [Azure 门户](https://portal.azure.com)以管理 TenantCreator 应用程序角色。 搜索并选择“企业应用程序”。  如果使用多个 Azure Active Directory 租户，则最好打开私密浏览器会话，然后将 URL 复制粘贴到地址栏。

   ![在 Azure 门户中搜索企业应用程序的屏幕截图](../media/azure-portal-enterprise-applications.png)
2. 在企业应用程序中，搜索“Windows 虚拟桌面”   。 将会看到在前一部分中提供了许可的两个应用程序。 在这两个应用中，选择“Windows 虚拟桌面”。 
   
   ![显示在企业应用程序中搜索“Windows 虚拟桌面”时出现的搜索结果的屏幕截图。 名为“Windows 虚拟桌面”的应用突出显示。](../media/tenant-enterprise-app.png)
3. 选择“用户和组”  。 你可能会看到，已列出授予应用程序许可的管理员且已分配“默认访问”角色  。 但这还不足以创建 Windows 虚拟桌面租户。 请继续按照这些说明向用户添加 TenantCreator 角色  。
   
   ![显示负责管理“Windows 虚拟桌面”企业应用程序的用户和组的屏幕截图。 该屏幕截图仅显示“默认访问”的一项分配。](../media/tenant-default-access.png)
4. 选择“添加用户”，然后在“添加分配”选项卡中选择“用户和组”    。
5. 搜索用于创建 Windows 虚拟桌面租户的用户帐户。 为简单起见，可以使用全局管理员帐户。
   - 如果使用的是 Microsoft 标识提供程序（如 contosoadmin@live.com 或 contosoadmin@outlook.com），则可能无法登录到 Windows 虚拟桌面。 建议改为使用特定于域的帐户（如 admin@contoso.com 或 admin@contoso.onmicrosoft.com）。

   ![该屏幕截图显示选来添加为“TenantCreator”的用户。](../media/tenant-assign-user.png)
   > [!NOTE]
   > 必须选择来自此 Azure Active Directory 实例的用户（或包含用户的组）。 无法选择来宾 (B2B) 用户或服务主体。

6. 选择该用户帐户，选择“选择”按钮，然后选择“分配”。  
7. 在“Windows 虚拟桌面 - 用户和组”页面上，验证是否看到有新条目显示向要创建 Windows 虚拟桌面租户的用户分配了 TenantCreator 角色   。

   ![显示负责管理“Windows 虚拟桌面”企业应用程序的用户和组的屏幕截图。 屏幕截图现还有一个条目显示向“TenantCreator”角色分配了用户。](../media/tenant-tenant-creator-added.png)

继续创建 Windows 虚拟桌面租户前，需提供下列两项信息：

   - Azure Active Directory 租户 ID（或**目录 ID**）
   - Azure 订阅 ID

若要查找 Azure Active Directory 租户 ID（或**目录 ID**），请执行以下操作：
1. 在同一 [Azure 门户](https://portal.azure.com)会话中，搜索并选择“Azure Active Directory”  。

   ![显示 Azure 门户中“Azure Active Directory”的搜索结果的屏幕截图。 “服务”下的搜索结果突出显示。](../media/tenant-search-azure-active-directory.png)
2. 向下滚动直到找到“属性”，然后将其选中  。
3. 查找“目录 ID”，然后选择剪贴板图标  。 将其粘贴到方便的位置，以便稍后可将其用作 **AadTenantId** 值。

   ![Azure Active Directory 属性的屏幕截图。 将鼠标悬停在剪贴板图标上来复制粘贴“目录 ID”。](../media/tenant-directory-id.png)

要查找 Azure 订阅 ID，请执行以下操作：
1. 在同一 [Azure 门户](https://portal.azure.com)会话中，搜索并选择“订阅”  。
   
   ![显示 Azure 门户中“Azure Active Directory”的搜索结果的屏幕截图。 “服务”下的搜索结果突出显示。](../media/tenant-search-subscription.png)
2. 选择要用于接收 Windows 虚拟桌面服务通知的 Azure 订阅。
3. 查找“订阅 ID”，然后将鼠标悬停在该值上，直到出现剪贴板图标  。 选择剪贴板图标，再将其粘贴到方便的位置，以便稍后可将其用作 **AzureSubscriptionId** 值。
   
   ![Azure 订阅属性的屏幕截图。 将鼠标悬停在剪贴板图标上来复制粘贴“订阅 ID”。](../media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>创建 Windows 虚拟桌面租户

向 Windows 虚拟桌面服务授予查询 Azure Active Directory 的权限并将 TenantCreator 角色分配到用户帐户后，可以创建 Windows 虚拟桌面租户。

首先[下载并导入 Windows 虚拟桌面模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做），以便在 PowerShell 会话中使用。

运行以下 cmdlet，使用 TenantCreator 用户帐户登录到 Windows 虚拟桌面：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

然后，创建与 Azure Active Directory 租户关联的新 Windows 虚拟桌面租户：

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

将带方括号的值替换为与组织和租户相关的值。 为新的 Windows 虚拟桌面租户选择的名称应全局唯一。 例如，假设你是 Contoso 组织的 Windows 虚拟桌面的 TenantCreator， 则运行的 cmdlet 如下所示：

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

最好为另一个用户分配管理访问权限，以应对自己被锁定在帐户外或者在休假时需要有人充当你缺勤时的租户管理员的情况。 若要为另一个用户分配管理员访问权限，请运行以下 cmdlet，并将 `<TenantName>` 和 `<Upn>` 替换为你的租户名称和这第二个用户的 UPN。

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>后续步骤

创建租户后，需要在 Azure Active Directory 中创建服务主体并在 Windows 虚拟桌面中为其分配角色。 通过服务主体可成功部署 Windows 虚拟桌面 Azure 市场产品/服务，从而创建主机池。 若要详细了解主机池，请继续学习有关在 Windows 虚拟桌面中创建主机池的教程。

> [!div class="nextstepaction"]
> [使用 PowerShell 创建服务主体和角色分配](create-service-principal-role-powershell.md)
