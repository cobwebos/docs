---
title: 在 Windows 虚拟桌面预览版中创建租户 - Azure
description: 介绍如何在 Azure Active Directory 中设置 Windows 虚拟桌面预览版租户。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: b8b5c2cef1db5018ce0d61e1950f49a3bd215ac2
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402892"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>教程：在 Windows 虚拟桌面预览版中创建租户

在 Windows 虚拟桌面预览版中创建租户是生成桌面虚拟化解决方案的第一步。 租户是由一个或多个主机池构成的组。 每个主机池包含多个会话主机，这些主机作为虚拟机在 Azure 中运行，并注册到 Windows 虚拟桌面服务。 每个主机池还包含用于向用户发布远程桌面和远程应用程序资源的一个或多个应用组。 使用租户可以生成主机池、创建应用组、分配用户，以及通过服务建立连接。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 向 Windows 虚拟桌面服务授予 Azure Active Directory 权限。
> * 将 TenantCreator 应用程序角色分配到 Azure Active Directory 租户中的用户。
> * 创建 Windows 虚拟桌面租户。

需要做好以下准备才能设置 Windows 虚拟桌面租户：

* Windows 虚拟桌面用户的 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 租户 ID。
* Azure Active Directory 租户中的全局管理员帐户。
   * 这也适用于要为其客户创建 Windows 虚拟桌面租户的云解决方案提供商 (CSP) 组织。 CSP 组织必须能够以其客户的 Azure Active Directory 全局管理员身份登录。
* Azure 订阅 ID

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>向 Windows 虚拟桌面预览版服务授予 Azure Active Directory 权限

如果已向 Windows 虚拟桌面授予对此 Azure Active Directory 的权限，请跳过本部分。

向 Windows 虚拟桌面服务授予权限可让该服务在 Azure Active Directory 中查询管理任务和最终用户任务。

向该服务授予权限：

1. 打开浏览器并连接到 [Windows 虚拟桌面许可页](https://rdweb.wvd.microsoft.com)。
2. 对于“许可选项” > “服务器应用”，请输入 Azure Active Directory 租户名称或目录 ID，然后选择“提交”。
        对于云解决方案提供商客户，该 ID 是合作伙伴门户中显示的客户 Microsoft ID。 对于企业客户，该 ID 位于“Azure Active Directory” > “属性” > “目录 ID”下。
3. 使用全局管理员帐户登录到 Windows 虚拟桌面许可页。 例如，如果你所在的组织是 Contoso，则你的帐户可能是 admin@contoso.com 或 admin@contoso.onmicrosoft.com。  
4. 选择“接受”。
5. 等待一分钟。
6. 导航回到 [Windows 虚拟桌面许可页](https://rdweb.wvd.microsoft.com)。
7. 转到“许可选项” > “客户端应用”，输入相同的 Azure AD 租户名称或目录 ID，然后选择“提交”。
8. 像在前面的步骤 3 中一样，以全局管理员的身份登录到 Windows 虚拟桌面许可页。
9. 选择“接受”。

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>将 TenantCreator 应用程序角色分配到 Azure Active Directory 租户中的用户

为 Azure Active Directory 用户分配 TenantCreator 应用程序角色可让该用户创建与 Azure Active Directory 关联的 Windows 虚拟桌面租户。 需要使用全局管理员帐户分配 TenantCreator 角色。

使用全局管理员帐户分配 TenantCreator 应用程序角色：

1. 打开浏览器，并使用全局管理员帐户连接到 [Azure Active Directory 门户](https://aad.portal.azure.com)。
   - 如果你正在使用多个 Azure AD 租户，则最好是打开私密浏览器会话，然后复制 URL 并将其粘贴到地址栏中。
2. 选择“企业应用程序”并搜索“Windows 虚拟桌面”。 将会看到在前一部分中提供了许可的两个应用程序。 在这两个应用中，选择“Windows 虚拟桌面”。
3. 依次选择“用户和组”、“添加用户”。
4. 在“添加分配”边栏选项卡中选择“用户和组”
5. 搜索用于创建 Windows 虚拟桌面租户的用户帐户。
   - 为简单起见，可以使用全局管理员帐户。
6. 选择该用户帐户，单击“选择”按钮，然后选择“分配”。

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>创建 Windows 虚拟桌面预览版租户

向 Windows 虚拟桌面服务授予查询 Azure Active Directory 的权限并将 TenantCreator 角色分配到用户帐户后，可以创建 Windows 虚拟桌面租户。

首先[下载并导入 Windows 虚拟桌面模块](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)（如果尚未这样做），以便在 PowerShell 会话中使用。

运行以下 cmdlet，使用 TenantCreator 用户帐户登录到 Windows 虚拟桌面：

```powershell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
```

然后，创建与 Azure Active Directory 租户关联的新 Windows 虚拟桌面租户：

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

应该将带方括号的值替换为与组织和租户相关的值。 例如，假设你是 Contoso 组织的 Windows 虚拟桌面的 TenantCreator， 则运行的 cmdlet 如下所示：

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>后续步骤

创建租户后，需要创建主机池。 若要详细了解主机池，请继续学习有关在 Windows 虚拟桌面中创建主机池的教程。

> [!div class="nextstepaction"]
> [Windows 虚拟桌面主机池教程](./create-host-pools-azure-marketplace.md)
