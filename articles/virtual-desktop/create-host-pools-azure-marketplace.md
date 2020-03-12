---
title: 使用 Azure 市场创建 Windows 虚拟桌面主机池 - Azure
description: 如何使用 Azure 市场创建 Windows 虚拟桌面主机池。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d5165b160ffc196416052a56aaa0d93c05db56bc
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127974"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>教程：使用 Azure Marketplace 创建主机池

在本教程中，你将了解如何使用 Microsoft Azure 市场产品/服务在 Windows 虚拟桌面租户中创建主机池。

主机池是 Windows 虚拟桌面租户环境中一个或多个相同虚拟机的集合。 每个主机池可以包含一个应用组，用户可以像在物理桌面上一样与该应用组交互。

本教程中的任务包括：

> [!div class="checklist"]
>
> * 在 Windows 虚拟桌面中创建主机池。
> * 在 Azure 订阅中创建包含 VM 的资源组。
> * 将 VM 加入到 Active Directory 域。
> * 将 VM 注册到 Windows 虚拟桌面。

## <a name="prerequisites"></a>必备条件

* 虚拟机中的租户。 前面的[教程](tenant-setup-azure-active-directory.md)创建了一个租户。
* [Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)。

安装此模块后，请运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>运行 Azure 市场套餐以预配新的主机池

若要运行 Azure 市场套餐以预配新的主机池，请执行以下操作：

1. 在 Azure 门户菜单或“主页”页上，选择“创建资源”。
1. 在市场搜索窗口中输入“Windows 虚拟桌面”。
1. 选择“Windows 虚拟桌面 - 预配主机池”，然后选择“创建”。

然后，按照下一部分中的说明，为相应的选项卡输入信息。

### <a name="basics"></a>基础

下面是 "**基本**信息" 选项卡的作用：

1. 选择一个“订阅”。
1. 对于 "**资源组**"，选择 "**新建**" 并提供新资源组的名称。
1. 选择**区域**。
1. 输入 Windows 虚拟桌面租户中唯一的主机池的名称。
1. 选择 "**桌面类型**"。 如果选择 "**个人**"，则会将连接到此主机池的每个用户永久分配给虚拟机。
1. 输入可登录到 Windows 虚拟桌面客户端和访问桌面的用户。 使用以逗号分隔的列表。 例如，如果要分配 `user1@contoso.com` 和 `user2@contoso.com` 访问权限，请输入 *`user1@contoso.com,user2@contoso.com`*
1. 对于 "**服务元数据位置**"，选择与已连接到 Active Directory 服务器的虚拟网络相同的位置。

   >[!IMPORTANT]
   >如果使用纯 Azure Active Directory 域服务（Azure AD DS）和 Azure Active Directory （Azure AD）解决方案，请确保将主机池部署在与 Azure AD DS 相同的区域中，以避免域加入和凭据错误。

1. 选择 "**下一步：配置虚拟机**"。

### <a name="configure-virtual-machines"></a>配置虚拟机

对于 "**配置虚拟机**" 选项卡：

1. 接受默认值或自定义虚拟机的数量和大小。

    >[!NOTE]
    >如果要查找的特定虚拟机大小未显示在大小选择器中，这是因为我们尚未将其载入到 Azure Marketplace 工具。 若要请求大小，请在[Windows 虚拟桌面 UserVoice 论坛](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)中创建请求或投票赞成现有请求。

1. 输入虚拟机名称的前缀。 例如，如果输入*前缀*，则虚拟机将被称为**前缀-0**、**前缀-1**，依此类推。
1. 选择 "**下一步：虚拟机设置**"。

### <a name="virtual-machine-settings"></a>虚拟机设置

对于 "**虚拟机设置**" 选项卡：

1. 对于“映像源”，请选择所需的源，并输入有关如何找到和存储该源的相应信息。 Blob 存储、托管映像和库的选项有所不同。

   如果选择不使用托管磁盘，请选择包含 *.vhd*文件的存储帐户。
1. 输入用户主体名称和密码。 此帐户必须是将虚拟机加入到 Active Directory 域的域帐户。 在虚拟机上将会创建与此相同的用户名和密码作为本地帐户。 以后可以重置这些本地帐户。

   >[!NOTE]
   > 如果要将虚拟机加入到 Azure AD DS 环境中，请确保域加入用户是[AAD DC Administrators 组](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)的成员。
   >
   > 该帐户还必须是 Azure AD DS 托管域或 Azure AD 租户的成员。 与 Azure AD 租户关联的外部目录的帐户无法在加入域的过程中正确进行身份验证。

1. 选择连接到 Active Directory 服务器的**虚拟网络**，然后选择用于托管虚拟机的子网。
1. 选择**下一步： Windows 虚拟桌面信息**。

### <a name="windows-virtual-desktop-tenant-information"></a>Windows 虚拟桌面租户信息

对于**Windows 虚拟桌面租户信息**选项卡：

1. 对于“Windows 虚拟桌面租户组名称”，请输入租户所在的租户组的名称。 将此字段保留默认值，除非为你提供了具体的租户组名称。
1. 对于“Windows 虚拟桌面租户名称”，请输入要在其中创建此主机池的租户的名称。
1. 指定以 Windows 虚拟桌面租户 RDS 所有者身份进行身份验证时要使用的凭据类型。 输入 UPN 或服务主体和密码。

   如果已完成[“使用 PowerShell 创建服务主体和角色分配”教程](./create-service-principal-role-powershell.md)，选择“服务主体”。

1. 对于 "**服务主体**"，为**Azure AD 租户 ID**"输入包含服务主体的 Azure AD 实例的租户管理员帐户。 仅支持具有密码凭据的服务主体。
1. 选择**下一步：查看 + 创建**。

## <a name="complete-setup-and-create-the-virtual-machine"></a>完成设置并创建虚拟机

在 "**检查和创建**" 中，查看安装信息。 如果需要更改某些内容，请返回并进行更改。 准备就绪后，选择 "**创建**" 以部署主机池。

完成此过程可能需要30分钟或更长时间，具体取决于要创建的虚拟机的数量。

>[!IMPORTANT]
> 为了帮助保护 Azure 中的 Windows 虚拟桌面环境，我们建议你不要在虚拟机上打开入站端口3389。 Windows 虚拟桌面不需要打开的入站端口3389供用户访问主机池的虚拟机。
>
> 如果出于故障排除目的，必须打开端口3389，建议使用实时访问。 有关详细信息，请参阅[使用实时访问保护管理端口](../security-center/security-center-just-in-time.md)。

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>（可选）将其他用户分配到桌面应用程序组

Azure Marketplace 完成创建池后，可以将更多用户分配到桌面应用程序组。 如果不想添加其他内容，请跳过此部分。

将用户分配到桌面应用程序组：

1. 打开 PowerShell 窗口。

1. 运行以下命令以登录到 Windows 虚拟桌面环境：

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. 使用以下命令将用户添加到桌面应用程序组：

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   用户的 UPN 应匹配用户在 Azure AD 中的标识，例如 *user1@contoso.com* 。 如果要添加多个用户，请为每个用户运行此命令。

添加到桌面应用程序组的用户可以使用受支持的远程桌面客户端登录到 Windows 虚拟桌面，并查看会话桌面的资源。

当前支持的客户端如下：

* [适用于 Windows 7 和 Windows 10 的远程桌面客户端](connect-windows-7-and-10.md)
* [Windows 虚拟桌面 Web 客户端](connect-web.md)

## <a name="next-steps"></a>后续步骤

你已经创建了一个主机池，并为其分配了用户访问其桌面的权限。 可以用 RemoteApp 程序填充主机池。 若要详细了解如何在 Windows 虚拟桌面中管理应用，请参阅以下教程：

> [!div class="nextstepaction"]
> [管理应用组教程](./manage-app-groups.md)
