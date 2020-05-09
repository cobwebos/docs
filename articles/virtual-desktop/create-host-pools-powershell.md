---
title: 创建 Windows 虚拟桌面主机池 PowerShell-Azure
description: 如何使用 PowerShell cmdlet 在 Windows 虚拟桌面中创建主机池。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 474eb4f5247aeb77edce0ebfde1611bf2deef493
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930397"
---
# <a name="create-a-host-pool-with-powershell"></a>使用 PowerShell 创建主机池

>[!IMPORTANT]
>此内容适用于带有 Azure 资源管理器 Windows 虚拟桌面对象的弹簧2020更新。 如果使用的是不带 Azure 资源管理器对象的 Windows 虚拟桌面2019版，请参阅[此文](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md)。
>
> Windows 虚拟桌面春季2020更新目前为公共预览版。 此预览版本在提供时没有服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

主机池是 Windows 虚拟桌面租户环境中一个或多个相同虚拟机的集合。 每个主机池可以与多个 RemoteApp 组、一个桌面应用组和多个会话主机关联。

## <a name="prerequisites"></a>必备条件

本文假设已按照[设置 PowerShell 模块](powershell-module.md)中的说明进行操作。

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>使用 PowerShell 客户端创建主机池

运行以下 cmdlet 登录到 Windows 虚拟桌面环境：

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname> 
```

此 cmdlet 将创建主机池、工作区和桌面应用组。 此外，它还会将桌面应用组注册到工作区。 可以使用此 cmdlet 创建工作区，也可以使用现有的工作区。 

运行下一个 cmdlet 以创建注册令牌，以授权会话主机加入主机池并将其保存到本地计算机上的新文件。 可以使用-ExpirationHours 参数指定注册令牌的有效时间。

>[!NOTE]
>令牌的到期日期不能少于1小时，也不能超过1个月。 如果在该限制之外设置 *-ExpirationTime* ，则 cmdlet 不会创建令牌。

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

运行下一个 cmdlet，将 Azure Active Directory 用户组添加到主机池的默认桌面应用组：

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+“-DAG”> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

运行以下 cmdlet，将注册令牌导出到变量，稍后会在将[虚拟机注册到 Windows 虚拟机主机池](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)时使用。

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> 
```

## <a name="create-virtual-machines-for-the-host-pool"></a>为主机池创建虚拟机

现在，你可以创建可加入你的 Windows 虚拟机主机池的 Azure 虚拟机。

可以通过多种方式创建虚拟机：

- [从 Azure 库映像创建虚拟机](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [从托管映像创建虚拟机](../virtual-machines/windows/create-vm-generalized-managed.md)
- [从非托管映像创建虚拟机](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>如果要使用 Windows 7 将虚拟机部署为主机操作系统，创建和部署过程会稍有不同。 有关更多详细信息，请参阅[在 Windows 虚拟机上部署 windows 7 虚拟机](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md)。

创建会话主机虚拟机后，[将 windows 许可证应用到会话主机 VM](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm)以运行 Windows 或 windows Server 虚拟机，而无需支付其他许可证。 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>为 Windows 虚拟桌面代理安装准备虚拟机

你需要执行以下操作来准备虚拟机，然后才能安装 Windows 虚拟桌面代理并将虚拟机注册到你的 Windows 虚拟机主机池：

- 必须将计算机加入域。 这允许将传入 Windows 虚拟桌面用户从其 Azure Active Directory 帐户映射到其 Active Directory 帐户，并成功允许对虚拟机的访问。
- 如果虚拟机正在运行 Windows Server 操作系统，则必须安装远程桌面会话主机（RDSH）角色。 RDSH 角色允许 Windows 虚拟桌面代理进行正确安装。

若要成功加入域，请在每个虚拟机上执行以下操作：

1. 用在创建虚拟机时提供的凭据[连接到虚拟机](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 在虚拟机上，启动 "**控制面板"** ，然后选择 "**系统**"。
3. 选择 "**计算机名称**"，选择 "**更改设置**"，然后选择 "**更改 ...** "
4. 选择 "**域**"，然后在虚拟网络上输入 Active Directory 域。
5. 使用有权加入域的域帐户进行身份验证。

    >[!NOTE]
    > 如果要将 VM 加入到 Azure Active Directory 域服务 (Azure AD DS) 环境，请确保你的域加入用户也是 [AAD DC 管理员组](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)的成员。

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>向 Windows 虚拟桌面主机池注册虚拟机

将虚拟机注册到 Windows 虚拟机主机池非常简单，只需要安装 Windows 虚拟桌面代理即可。

若要注册 Windows 虚拟桌面代理，请在每个虚拟机上执行以下操作：

1. 用在创建虚拟机时提供的凭据[连接到虚拟机](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 下载并安装 Windows 虚拟桌面代理。
   - 下载[Windows 虚拟桌面代理](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)。
   - 运行安装程序。 当安装程序要求你提供注册令牌时，请输入从**RdsRegistrationInfo** cmdlet 获取的值。
3. 下载并安装 Windows 虚拟桌面代理引导程序。
   - 下载[Windows 虚拟桌面代理引导程序](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)。
   - 运行安装程序。

>[!IMPORTANT]
>为了帮助保护 Azure 中的 Windows 虚拟桌面环境，我们建议你不要在 VM 上打开入站端口 3389。 Windows 虚拟机不需要打开入站端口 3389，用户就可以访问主机池的 VM。 如果必须打开端口 3389 以进行故障排除，我们建议你使用[实时 VM 访问](../security-center/security-center-just-in-time.md)。 我们还建议你不要将 Vm 分配到公共 IP。

## <a name="next-steps"></a>后续步骤

现在，你已创建了一个主机池，可以使用 RemoteApps 来填充它。 若要详细了解如何在 Windows 虚拟桌面中管理应用，请参阅“管理应用组”教程。

> [!div class="nextstepaction"]
> [管理应用组教程](./manage-app-groups.md)
