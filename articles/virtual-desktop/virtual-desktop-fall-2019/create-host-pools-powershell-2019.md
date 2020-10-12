---
title: 创建 Windows 虚拟桌面 (经典) 主机池 PowerShell-Azure
description: 如何使用 PowerShell cmdlet 在 Windows 虚拟桌面 (经典) 中创建主机池。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c035a7fbafe9b3a42fbd16e3f8377014010ddd49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88003549"
---
# <a name="create-a-host-pool-in-windows-virtual-desktop-classic-with-powershell"></a>使用 PowerShell 在 Windows 虚拟桌面 (经典) 中创建主机池

>[!IMPORTANT]
>本教程的内容适用于 Windows 虚拟桌面（经典），后者不支持 Azure 资源管理器 Windows 虚拟桌面对象。 要尝试管理 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[本文](../create-host-pools-powershell.md)。

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

运行下一个 cmdlet，创建注册令牌以授权会话主机加入主机池，并将其保存到本地计算机上的新文件。 可以使用 -ExpirationHours 参数指定注册令牌的有效时间。

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

然后，运行此 cmdlet，将 Azure Active Directory 用户添加到主机池的默认桌面应用组。

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

RdsAppGroupUser cmdlet 不支持添加安全组，每次只会向应用组添加一个用户。 如果要将多个用户添加到应用组，请使用适当的用户主体名称重新运行该 cmdlet。

运行以下 cmdlet，将注册令牌导出到变量，稍后[将虚拟机注册到 Windows 虚拟桌面主机池](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)时会用到它。

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>为主机池创建虚拟机

现在，你可以创建可加入 Windows 虚拟桌面主机池的 Azure 虚拟机。

可以通过多种方式创建虚拟机：

- [从 Azure Gallery 映像创建虚拟机](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [从托管映像创建虚拟机](../../virtual-machines/windows/create-vm-generalized-managed.md)
- [从非托管映像创建虚拟机](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

>[!NOTE]
>如果使用 Windows 7 作为主机 OS 来部署虚拟机，创建和部署过程会稍有不同。 有关详细信息，请参阅[在 Windows 虚拟桌面上部署 Windows 7 虚拟机](deploy-windows-7-virtual-machine.md)。

创建会话主机虚拟机后，[将 Windows 许可证应用到会话主机 VM](../apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm)以便运行 Windows 或 Windows Server 虚拟机而不用为其他许可证付费。

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>为 Windows 虚拟桌面代理安装准备虚拟机

需要执行以下操作来准备虚拟机，然后才能安装 Windows 虚拟桌面代理并将虚拟机注册到 Windows 虚拟桌面主机池：

- 必须将虚拟机加入域。 这允许将传入的 Windows 虚拟桌面用户从其 Azure Active Directory 帐户映射到其 Active Directory 帐户，并成功向其授予虚拟机访问权限。
- 如果虚拟机正在运行 Windows Server OS，则必须安装远程桌面会话主机 (RDSH) 角色。 RDSH 角色允许 Windows 虚拟桌面代理进行正确安装。

若要成功加入域，请在每个虚拟机上执行以下操作：

1. 用创建虚拟机时提供的凭据[连接到虚拟机](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 在虚拟机上，启动“控制面板”并选择“系统”。 
3. 依次选择“计算机名称”、“更改设置”和“更改…”  
4. 选择“域”，然后输入虚拟网络上的 Active Directory 域。
5. 使用对于加入域的计算机拥有特权的域帐户进行身份验证。

    >[!NOTE]
    > 如果要将 VM 加入到 Azure Active Directory 域服务 (Azure AD DS) 环境，请确保你的域加入用户也是 [AAD DC 管理员组](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)的成员。

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>将虚拟机注册到 Windows 虚拟桌面主机池

将虚拟机注册到 Windows 虚拟桌面主机池非常简单，只需要安装 Windows 虚拟桌面代理即可。

若要注册 Windows 虚拟桌面代理，请在每个虚拟机上执行以下操作：

1. 用创建虚拟机时提供的凭据[连接到虚拟机](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 下载并安装 Windows 虚拟桌面代理。
   - 下载 [Windows 虚拟桌面代理](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)。
   - 右键单击已下载的安装程序，然后依次选择“属性”、“取消阻止”和“确定”。   这将让系统能够信任该安装程序。
   - 运行安装程序。 当安装程序要求你提供注册令牌时，请输入从 Export-RdsRegistrationInfo cmdlet 获取的值。
3. 下载并安装 Windows 虚拟桌面代理引导加载程序。
   - 下载 [Windows 虚拟桌面代理引导加载程序](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)。
   - 右键单击已下载的安装程序，然后依次选择“属性”、“取消阻止”和“确定”。   这将让系统能够信任该安装程序。
   - 运行安装程序。

>[!IMPORTANT]
>为了帮助保护 Azure 中的 Windows 虚拟桌面环境，我们建议你不要在 VM 上打开入站端口 3389。 Windows 虚拟机不需要打开入站端口 3389，用户就可以访问主机池的 VM。 如果必须打开端口 3389 以进行故障排除，我们建议你使用[实时 VM 访问](../../security-center/security-center-just-in-time.md)。

## <a name="next-steps"></a>后续步骤

创建主机池后，可以使用 RemoteApp 来填充它。 若要详细了解如何在 Windows 虚拟桌面中管理应用，请参阅“管理应用组”教程。

> [!div class="nextstepaction"]
> [管理应用组教程](../manage-app-groups.md)
