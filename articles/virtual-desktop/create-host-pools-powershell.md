---
title: 创建 Windows 虚拟桌面主机池 PowerShell - Azure
description: 如何使用 PowerShell cmdlet 在 Windows 虚拟桌面中创建主机池。
author: Heidilohr
ms.topic: how-to
ms.date: 10/02/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a47126a48ea63efd4e49097428679b85b7a95a61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91667157"
---
# <a name="create-a-windows-virtual-desktop-host-pool-with-powershell"></a>使用 PowerShell 创建 Windows 虚拟机主机池

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md)。

主机池是 Windows 虚拟桌面租户环境中一个或多个相同虚拟机的集合。 每个主机池可以与多个 RemoteApp 组、一个桌面应用组和多个会话主机关联。

## <a name="prerequisites"></a>先决条件

本文假设你已按照[设置 PowerShell 模块](powershell-module.md)中的说明进行设置。

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>使用 PowerShell 客户端创建主机池

运行以下 cmdlet 登录到 Windows 虚拟桌面环境：

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname>
```

此 cmdlet 将创建主机池、工作区和桌面应用组。 此外，它还会将桌面应用组注册到工作区。 可以使用此 cmdlet 创建工作区，也可以使用现有的工作区。

运行下一个 cmdlet，创建注册令牌以授权会话主机加入主机池，并将其保存到本地计算机上的新文件。 可以使用 -ExpirationHours 参数指定注册令牌的有效时间。

>[!NOTE]
>令牌的到期日期不能少于 1 小时，也不能超过 1 个月。 如果所设置的 -ExpirationTime 超出该限制，该 cmdlet 不会创建令牌。

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

例如，如果想要创建一个在两小时后过期的令牌，请运行以下 cmdlet：

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

然后，运行此 cmdlet，将 Azure Active Directory 用户添加到主机池的默认桌面应用组。

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

接下来运行此 cmdlet，将 Azure Active Directory 用户组添加到主机池的默认桌面应用组：

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

运行以下 cmdlet，将注册令牌导出到变量，稍后[将虚拟机注册到 Windows 虚拟桌面主机池](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)时会用到它。

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname>
```

## <a name="create-virtual-machines-for-the-host-pool"></a>为主机池创建虚拟机

现在，你可以创建可加入 Windows 虚拟桌面主机池的 Azure 虚拟机。

可以通过多种方式创建虚拟机：

- [从 Azure Gallery 映像创建虚拟机](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [从托管映像创建虚拟机](../virtual-machines/windows/create-vm-generalized-managed.md)
- [从非托管映像创建虚拟机](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>如果使用 Windows 7 作为主机操作系统来部署虚拟机，创建和部署过程会稍有不同。 有关详细信息，请参阅[在 Windows 虚拟桌面上部署 Windows 7 虚拟机](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md)。

创建会话主机虚拟机后，[将 Windows 许可证应用到会话主机 VM](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm)以便运行 Windows 或 Windows Server 虚拟机而不用为其他许可证付费。

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>为 Windows 虚拟桌面代理安装准备虚拟机

需要执行以下操作来准备虚拟机，然后才能安装 Windows 虚拟桌面代理并将虚拟机注册到 Windows 虚拟桌面主机池：

- 必须将虚拟机加入域。 这允许将传入的 Windows 虚拟桌面用户从其 Azure Active Directory 帐户映射到其 Active Directory 帐户，并成功向其授予虚拟机访问权限。
- 如果虚拟机正在运行 Windows Server OS，则必须安装远程桌面会话主机 (RDSH) 角色。 RDSH 角色允许 Windows 虚拟桌面代理进行正确安装。

若要成功加入域，请在每个虚拟机上执行以下操作：

1. 用创建虚拟机时提供的凭据[连接到虚拟机](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 在虚拟机上，启动“控制面板”并选择“系统”。 
3. 依次选择“计算机名称”、“更改设置”和“更改…”  
4. 选择“域”，然后输入虚拟网络上的 Active Directory 域。
5. 使用对于加入域的计算机拥有特权的域帐户进行身份验证。

    >[!NOTE]
    > 如果要将 VM 加入到 Azure Active Directory 域服务 (Azure AD DS) 环境，请确保你的域加入用户也是 [AAD DC 管理员组](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)的成员。

>[!IMPORTANT]
>建议你不要启用任何禁用 Windows Installer 的策略或配置。 如果你禁用 Windows Installer，则该服务将无法在你的会话主机上安装代理更新，并且你的会话主机将无法正常运行。

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>将虚拟机注册到 Windows 虚拟机主机池

将虚拟机注册到 Windows 虚拟机主机池非常简单，只需要安装 Windows 虚拟桌面代理即可。

若要注册 Windows 虚拟桌面代理，请在每个虚拟机上执行以下操作：

1. 用创建虚拟机时提供的凭据[连接到虚拟机](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 下载并安装 Windows 虚拟桌面代理。
   - 下载 [Windows 虚拟桌面代理](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)。
   - 运行安装程序。 当安装程序要求你提供注册令牌时，请输入从 **AzWvdRegistrationInfo** cmdlet 获取的值。
3. 下载并安装 Windows 虚拟桌面代理引导加载程序。
   - 下载 [Windows 虚拟桌面代理引导加载程序](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)。
   - 运行安装程序。

>[!IMPORTANT]
>为了帮助保护 Azure 中的 Windows 虚拟桌面环境，我们建议你不要在 VM 上打开入站端口 3389。 Windows 虚拟机不需要打开入站端口 3389，用户就可以访问主机池的 VM。 如果必须打开端口 3389 以进行故障排除，我们建议你使用[实时 VM 访问](../security-center/security-center-just-in-time.md)。 同时建议不要向 VM 分配公共 IP。

## <a name="update-the-agent"></a>更新代理

如果你在以下情况之一，则需要更新代理：

- 要将以前注册的会话迁移到新的主机池
- 更新后，会话主机不会显示在主机池中

更新代理：

1. 以管理员身份登录到 VM。
2. 请先执行 " **服务**"，然后停止 **Rdagent** 和 **远程桌面代理加载** 器进程。
3. 接下来，查找代理和加载程序 Msi。 它们将位于 **C:\DeployAgent** 文件夹中或在安装时保存到的任何位置。
4. 找到以下文件并将其卸载：
     
     - RDInfra. RDAgent-x64-verx. x
     - RDInfra. RDAgentBootLoader-x64

   若要卸载这些文件，请右键单击每个文件名，然后选择 " **卸载**"。
5. 还可以选择删除以下注册表设置：
     
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDInfraAgent
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDAgentBootLoader

6. 卸载这些项后，这会删除与旧主机池的所有关联。 如果要将此主机重新注册到服务，请按照将 [虚拟机注册到 Windows 虚拟机主机池](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)中的说明进行操作。


## <a name="next-steps"></a>后续步骤

创建主机池后，可以使用 RemoteApp 来填充它。 若要详细了解如何在 Windows 虚拟桌面中管理应用，请参阅“管理应用组”教程。

> [!div class="nextstepaction"]
> [管理应用组教程](./manage-app-groups.md)
