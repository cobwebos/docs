---
title: 使用 Azure 市场创建 Windows 虚拟桌面主机池 - Azure
description: 如何使用 Azure 市场创建 Windows 虚拟桌面主机池。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: helohr
ms.openlocfilehash: c0b93529872de774e1a6e915ef8254c5c0e0a1a9
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676705"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>教程：通过使用 Azure 市场创建主机池

主机池是 Windows 虚拟桌面租户环境中一个或多个相同虚拟机的集合。 每个主机池可以包含一个应用组，用户可以像在物理桌面上一样与该应用组交互。

本教程介绍如何使用 Microsoft Azure 市场产品/服务在 Windows 虚拟桌面租户中创建主机池。 相关任务包括：

> [!div class="checklist"]
> * 在 Windows 虚拟桌面中创建主机池。
> * 在 Azure 订阅中创建包含 VM 的资源组。
> * 将 VM 加入到 Active Directory 域。
> * 将 VM 注册到 Windows 虚拟桌面。

在开始之前，如果尚未这样做，请[下载并导入 Windows 虚拟桌面 PowerShell 模块](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)，以便在 PowerShell 会话中使用。 然后，运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>运行 Azure 市场套餐以预配新的主机池

若要运行 Azure 市场套餐以预配新的主机池，请执行以下操作：

1. 选择 **+** 或“+ 创建资源”。 
2. 在市场搜索窗口中输入“Windows 虚拟桌面”。 
3. 选择“Windows 虚拟桌面 - 预配主机池”，然后选择“创建”   。

然后，按照下一部分中的说明为相应边栏选项卡输入信息。

### <a name="basics"></a>基础

下面是你在“基本信息”边栏选项卡中可执行的操作  ：

1. 输入主机池的名称，该名称在 Windows 虚拟桌面租户中必须唯一。
2. 选择适合个人桌面的选项。 如果选择“是”，则连接到此主机池的每个用户将永久分配到某个虚拟机。 
3. 输入在 Azure 市场产品/服务完成后可登录到 Windows 虚拟桌面客户端并访问桌面的用户的逗号分隔列表。 例如，如果要分配 user1@contoso.com 和 user2@contoso.com 访问权限，请输入“user1@contoso.com,user2@contoso.com”。
4. 选择“新建”，并为新资源组提供一个名称。 
5. 对于“位置”，请选择连接到 Active Directory 服务器的虚拟网络所在的同一位置。 
6. 选择“确定”  。

>[!IMPORTANT]
>如果使用的是纯 Azure Active Directory 域服务和 Azure Active Directory 解决方案，请确保将主机池部署在与 Azure Active Directory 域服务相同的区域中，以避免加入域错误和凭据错误。

### <a name="configure-virtual-machines"></a>配置虚拟机

在“配置虚拟机”边栏选项卡中，可以执行以下操作  ：

1. 接受默认设置，或自定义 VM 的数量和大小。
2. 输入虚拟机名称的前缀。 例如，如果输入“prefix”作为名称，则虚拟机的名称将是“prefix-0”、“prefix-1”，依此类推。
3. 选择“确定”  。

### <a name="virtual-machine-settings"></a>虚拟机设置

在“虚拟机设置”边栏选项卡中，可以执行以下操作  ：

>[!NOTE]
> 如果要将 VM 加入到 Azure Active Directory 域服务 (Azure AD DS) 环境，请确保你的域加入用户也是 [AAD DC 管理员组](../active-directory-domain-services/tutorial-create-instance.md#configure-an-administrative-group)的成员。

1. 对于“映像源”，请选择所需的源，并输入有关如何找到和存储该源的相应信息  。 如果选择不使用托管磁盘，请选择包含 .vhd 文件的存储帐户。
2. 输入用于将 VM 加入 Active Directory 域的域帐户的用户主体名称和密码。 在虚拟机上将会创建与此相同的用户名和密码作为本地帐户。 以后可以重置这些本地帐户。
3. 选择连接到 Active Directory 服务器的虚拟网络，然后选择一个子网用于托管虚拟机。
4. 选择“确定”  。

### <a name="windows-virtual-desktop-tenant-information"></a>Windows 虚拟桌面租户信息

在“Windows 虚拟桌面租户信息”边栏选项卡中  ，可以执行以下操作：

1. 对于“Windows 虚拟桌面租户组名称”  ，请输入租户所在的租户组的名称。 将此字段保留默认值，除非为你提供了具体的租户组名称。
2. 对于“Windows 虚拟桌面租户名称”  ，请输入要在其中创建此主机池的租户的名称。
3. 指定以 Windows 虚拟桌面租户 RDS 所有者身份进行身份验证时要使用的凭据类型。 如果已完成[“使用 PowerShell 创建服务主体和角色分配”教程](./create-service-principal-role-powershell.md)，选择“服务主体”  。 在“Azure AD 租户 ID”显示时  ，请输入包含服务主体的 Azure Active Directory 实例的 ID。
4. 请输入租户管理员帐户的凭据。 仅支持具有密码凭据的服务主体。
5. 选择“确定”  。

## <a name="complete-setup-and-create-the-virtual-machine"></a>完成设置并创建虚拟机

对于最后两个边栏选项卡：

1. 在“摘要”边栏选项卡中查看设置信息  。 如果需要更改设置，请返回相应的边栏选项卡进行更改，然后继续。 如果信息正确，请选择“确定”。 
2. 在“购买”边栏选项卡中，查看有关在 Azure 市场中购买的内容的附加信息  。
3. 选择“创建”以部署主机池。 

此过程可能需要 30 分钟或更长时间才能完成，具体取决于创建的 VM 有多少。

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>（可选）将其他用户分配到桌面应用程序组

在 Azure 市场产品/服务完成后，可将更多用户分配到桌面应用程序组，然后在虚拟机上开始测试完整的会话桌面。 如果已在 Azure 市场套餐中添加默认用户，并且不想要添加其他用户，则可以跳过本部分。

若要将用户分配到桌面应用程序组，首先必须打开 PowerShell 窗口。 然后，需要输入以下两个 cmdlet。

运行以下 cmdlet 登录到 Windows 虚拟桌面环境：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

使用此 cmdlet 将用户添加到桌面应用程序组：

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

用户的 UPN 应与该用户在 Azure Active Directory 中的标识（例如 user1@contoso.com）相匹配。 若要添加多个用户，必须针对每个用户运行此 cmdlet。

完成这些步骤后，添加到桌面应用程序组的用户可以使用支持的远程桌面客户端登录到 Windows 虚拟桌面，并且可以看到会话桌面的资源。

当前支持的客户端如下：

- [适用于 Windows 7 和 Windows 10 的远程桌面客户端](connect-windows-7-and-10.md)
- [Windows 虚拟桌面 Web 客户端](connect-web.md)

>[!IMPORTANT]
>为了帮助保护 Azure 中的 Windows 虚拟桌面环境，我们建议你不要在 VM 上打开入站端口 3389。 Windows 虚拟机不需要打开入站端口 3389，用户就可以访问主机池的 VM。 如果必须打开端口 3389 以进行故障排除，我们建议你使用[实时 VM 访问](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)。

## <a name="next-steps"></a>后续步骤

创建了主机池并分配了可访问其桌面的用户后，便可使用 RemoteApp 程序填充主机池。 若要详细了解如何在 Windows 虚拟桌面中管理应用，请参阅以下教程：

> [!div class="nextstepaction"]
> [管理应用组教程](./manage-app-groups.md)
