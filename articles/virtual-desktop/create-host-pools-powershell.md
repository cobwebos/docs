---
title: 创建 Windows 虚拟桌面主机池 PowerShell - Azure
description: 如何使用 PowerShell cmdlet 在 Windows 虚拟桌面中创建主机池。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b390c0beb20b7557294c18f889a0f41023513e2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246953"
---
# <a name="create-a-host-pool-with-powershell"></a>使用 PowerShell 创建主机池

主机池是 Windows 虚拟桌面租户环境中一个或多个相同虚拟机的集合。 每个主机池可以包含一个应用组，用户可以像在物理桌面上一样与该应用组交互。

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>使用 PowerShell 客户端创建主机池

首先[下载并导入 Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做），以便在 PowerShell 会话中使用。

运行以下 cmdlet 以登录到 Windows 虚拟桌面环境

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

接下来，运行此 cmdlet 以在 Windows 虚拟桌面租户中创建新的主机池：

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

运行下一个 cmdlet 以创建注册令牌，以授权会话主机加入主机池并将其保存到本地计算机上的新文件中。 您可以使用 -过期小时参数指定注册令牌的有效期。

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

之后，运行此 cmdlet 以将 Azure 活动目录用户添加到主机池的默认桌面应用组。

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

**Add-RdsAppGroupUser** cmdlet 不支持添加安全组，并且一次只向应用组添加一个用户。 如果要将多个用户添加到应用组，请使用适当的用户主体名称重新运行 cmdlet。

运行以下 cmdlet 将注册令牌导出到变量，稍后将在[将虚拟机注册到 Windows 虚拟桌面主机池](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)中使用该变量。

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>为主机池创建虚拟机

现在，您可以创建可加入到 Windows 虚拟桌面主机池的 Azure 虚拟机。

您可以通过多种方式创建虚拟机：

- [从 Azure 库映像创建虚拟机](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [从托管映像创建虚拟机](../virtual-machines/windows/create-vm-generalized-managed.md)
- [从非托管映像创建虚拟机](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>如果要使用 Windows 7 作为主机操作系统部署虚拟机，则创建和部署过程将略有不同。 有关详细信息，请参阅在[Windows 虚拟桌面上部署 Windows 7 虚拟机](deploy-windows-7-virtual-machine.md)。

创建会话主机虚拟机后，[将 Windows 许可证应用于会话主机 VM](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm)以运行 Windows 或 Windows Server 虚拟机，而无需支付其他许可证。 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>为 Windows 虚拟桌面代理安装准备虚拟机

在安装 Windows 虚拟桌面代理并将虚拟机注册到 Windows 虚拟桌面主机池之前，您需要执行以下操作来准备虚拟机：

- 您必须域加入计算机。 这允许传入的 Windows 虚拟桌面用户将其 Azure 活动目录帐户映射到其活动目录帐户，并成功允许访问虚拟机。
- 如果虚拟机正在运行 Windows 服务器操作系统，则必须安装远程桌面会话主机 （RDSH） 角色。 RDSH 角色允许 Windows 虚拟桌面代理正确安装。

要成功加入域，在每个虚拟机上执行以下操作：

1. 使用创建虚拟机时提供的凭据[连接到虚拟机](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 在虚拟机上，启动**控制面板**并选择 **"系统**"。
3. 选择**计算机名称**，选择 **"更改设置**"，然后选择 **"更改..."**
4. 选择 **"域**"，然后在虚拟网络上输入活动目录域。
5. 使用具有域加入计算机权限的域帐户进行身份验证。

    >[!NOTE]
    > 如果要将 VM 加入到 Azure Active Directory 域服务 (Azure AD DS) 环境，请确保你的域加入用户也是 [AAD DC 管理员组](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)的成员。

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>将虚拟机注册到 Windows 虚拟桌面主机池

将虚拟机注册到 Windows 虚拟桌面主机池与安装 Windows 虚拟桌面代理一样简单。

要注册 Windows 虚拟桌面代理，请在每台虚拟机上执行以下操作：

1. 使用创建虚拟机时提供的凭据[连接到虚拟机](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 下载并安装 Windows 虚拟桌面代理。
   - 下载[Windows 虚拟桌面代理](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)。
   - 右键单击下载的安装程序，选择 **"属性****"，选择"取消阻止"，** 然后选择 **"确定**"。 这将允许您的系统信任安装程序。
   - 运行安装程序。 当安装程序要求您提供注册令牌时，输入从**导出 Rds注册信息**cmdlet 获得的值。
3. 下载并安装 Windows 虚拟桌面代理引导加载程序。
   - 下载[Windows 虚拟桌面代理引导加载程序](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)。
   - 右键单击下载的安装程序，选择 **"属性****"，选择"取消阻止"，** 然后选择 **"确定**"。 这将允许您的系统信任安装程序。
   - 运行安装程序。

>[!IMPORTANT]
>为了帮助保护 Azure 中的 Windows 虚拟桌面环境，我们建议你不要在 VM 上打开入站端口 3389。 Windows 虚拟机不需要打开入站端口 3389，用户就可以访问主机池的 VM。 如果必须打开端口 3389 以进行故障排除，我们建议你使用[实时 VM 访问](../security-center/security-center-just-in-time.md)。

## <a name="next-steps"></a>后续步骤

现在，您已经制作了一个主机池，您可以使用 RemoteApps 填充它。 若要详细了解如何在 Windows 虚拟桌面中管理应用，请参阅“管理应用组”教程。

> [!div class="nextstepaction"]
> [管理应用组教程](./manage-app-groups.md)
