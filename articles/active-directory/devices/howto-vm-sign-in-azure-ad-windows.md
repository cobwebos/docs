---
title: 使用 Azure 活动目录登录到 Azure 中的 Windows 虚拟机（预览）
description: Azure AD 登录到运行 Windows 的 Azure VM
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88ae3c45126403161e35ec46e5ccc2666c3edb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050061"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>使用 Azure 活动目录身份验证登录到 Azure 中的 Windows 虚拟机（预览）

组织现在可以对其运行**Windows Server 2019 数据中心版**或**Windows 10 1809**及更高版本的 Azure 虚拟机 （VM） 使用 Azure 活动目录 （AD） 身份验证。 使用 Azure AD 对 VM 进行身份验证为您提供了集中控制和强制实施策略的方法。 使用 Azure 基于角色的访问控制 （RBAC） 和 Azure AD 条件访问等工具，您可以控制谁可以访问 VM。 本文介绍如何创建和配置 Windows Server 2019 VM 以使用 Azure AD 身份验证。

|     |
| --- |
| Azure Windows VM 的 Azure AD 登录是 Azure 活动目录的公共预览功能。 有关预览的详细信息，请参阅 Microsoft [Azure 预览的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

使用 Azure AD 身份验证登录到 Azure 中的 Windows VM 有许多好处，包括：

- 使用通常使用的相同联合或托管 Azure AD 凭据。
- 不再需要管理本地管理员帐户。
- Azure RBAC 允许您根据需要授予对 VM 的适当访问权限，并在不再需要 VM 时将其删除。
- 在允许访问 VM 之前，Azure AD 条件访问可以强制实施其他要求，例如： 
   - 多重身份验证
   - 登录风险检查
- 自动和缩放作为 VDI 部署一部分的 Azure Windows VM 的 Azure Windows VM 加入。

> [!NOTE]
> 启用此功能后，Azure 中的 Windows VM 将加入 Azure AD。 不能将其加入其他域，如本地 AD 或 Azure AD DS。 如果需要这样做，则需要通过卸载扩展断开 VM 与 Azure AD 租户的连接。

## <a name="requirements"></a>要求

### <a name="supported-azure-regions-and-windows-distributions"></a>受支持的 Azure 区域和 Windows 分发

在此功能的预览期间，当前支持以下 Windows 发行版：

- Windows Server 2019 Datacenter
- 视窗 10 1809 及更高版本

> [!IMPORTANT]
> 仅允许连接到 Azure AD 的 VM 的远程连接从 Azure AD 联接的 Windows 10 电脑或与 VM**加入的同一**目录的混合 Azure AD。 

下面是目前（此功能的预览期间）受支持的 Azure 区域：

- 所有 Azure 全局区域

> [!IMPORTANT]
> 要使用此预览功能，只需在受支持的 Windows 分发区和受支持的 Azure 区域中部署。 Azure 政府或主权云当前不支持此功能。

### <a name="network-requirements"></a>网络要求

要为 Azure 中的 Windows VM 启用 Azure AD 身份验证，需要确保 VM 网络配置允许通过 TCP 端口 443 对以下终结点进行出站访问：

- https：\//enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- https：\//device.login.microsoftonline.com
- https：\//pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>在 Azure 中为 Windows VM 启用 Azure AD 登录

要在 Azure 中使用 Windows VM 的 Azure AD 登录，需要首先为 Windows VM 启用 Azure AD 登录选项，然后需要为有权登录到 VM 的用户配置 RBAC 角色分配。
您可以通过多种方式为 Windows VM 启用 Azure AD 登录：

- 创建 Windows VM 时使用 Azure 门户体验
- 在创建 Windows VM**或针对现有 Windows VM**时使用 Azure 云壳体验

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>使用 Azure 门户创建 VM 体验以启用 Azure AD 登录

您可以为 Windows 服务器 2019 数据中心或 Windows 10 1809 和更高版本的 VM 映像启用 Azure AD 登录。 

要在 Azure 中使用 Azure AD 登录创建 Windows 服务器 2019 数据中心 VM，请执行： 

1. 使用有权访问创建 VM 的帐户登录到[Azure 门户](https://portal.azure.com)，然后选择 **" 创建资源**"。
1. 在搜索应用商店搜索栏中键入**Windows 服务器**。
   1. 单击**Windows 服务器**并从"选择软件计划下拉列表"中选择 Windows 服务器**2019 数据中心**。
   1. 单击“创建”。****
1. 在"管理"选项卡上，启用在 Azure 活动目录部分"从关闭到**打开****"下使用 AAD 凭据（预览）登录**的选项。
1. 确保"标识"部分下**分配的系统托管标识**设置为**On**。 启用使用 Azure AD 凭据登录后，将自动执行此操作。
1. 体验创建虚拟机的其他体验。 在此预览期间，您必须为 VM 创建管理员用户名和密码。

![使用 Azure AD 凭据登录创建 VM](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> 为了使用 Azure AD 凭据登录到 VM，首先需要配置 VM 的角色分配，如下节之一所述。

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>使用 Azure 云外壳体验启用 Azure AD 登录

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的操作步骤。 Cloud Shell 中预安装并配置了常用的 Azure 工具以供你的帐户使用。 只需要选择“复制”按钮来复制代码，将其粘贴到 Cloud Shell 中，然后按 Enter 键来运行它。 可通过多种方式打开 Cloud Shell：

选择代码块右上角的“试用”。
在浏览器中打开 Cloud Shell。
选择 [Azure 门户](https://portal.azure.com)右上角菜单上的“Cloud Shell”按钮。

如果选择在本地安装和使用 CLI，则本文要求您运行 Azure CLI 版本 2.0.31 或更高版本。 若要查找版本，请运行 az --version。 如果需要安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

1. 使用 [az group create](/cli/azure/group#az-group-create) 创建资源组。 
1. 使用受支持的区域中的受支持分发创建具有[az vm](/cli/azure/vm#az-vm-create)的 VM。 
1. 安装 Azure AD 登录 VM 扩展。 

下面的示例将使用 Win2019Datacenter 的名为 myVM 的 VM 部署到位于中南部区域名为 myResourceGroup 的资源组中。 在以下示例中，可以根据需要提供自己的资源组和 VM 名称。

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> 在安装 Azure AD 登录 VM 扩展之前，需要在虚拟机上启用系统分配的托管标识。

创建 VM 和支持资源需要几分钟时间。

最后，安装 Azure AD 登录 VM 扩展，以启用 Windows VM 的 Azure AD 登录名。 VM 扩展是小型应用程序，可在 Azure 虚拟机上提供部署后配置和自动化任务。 使用[az vm 扩展](/cli/azure/vm/extension#az-vm-extension-set)集在 myResourceGroup 资源组中名为 myVM 的 VM 上安装 AADLoginForWindows 扩展：

> [!NOTE]
> 您可以在现有的 Windows 服务器 2019 或 Windows 10 1809 和更高版本的 VM 上安装 AADLoginForWindows 扩展，以启用它进行 Azure AD 身份验证。 下面显示了 AZ CLI 的示例。

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

在`provisioningState` `Succeeded` VM 上安装扩展后，将显示 。

## <a name="configure-role-assignments-for-the-vm"></a>为 VM 配置角色分配

创建 VM 后，需要配置 Azure RBAC 策略以确定谁可以登录到 VM。 使用两个 RBAC 角色来授权 VM 登录：

- **虚拟机管理员登录**：分配此角色的用户可以登录到具有管理员权限的 Azure 虚拟机。
- **虚拟机用户登录名**：分配了此角色的用户可以使用常规用户权限登录到 Azure 虚拟机。

> [!NOTE]
> 要允许用户通过 RDP 登录到 VM，必须分配虚拟机管理员登录或虚拟机用户登录角色。 具有为 VM 分配的所有者或参与者角色的 Azure 用户不会自动具有通过 RDP 登录到 VM 的权限。 这是为控制虚拟机的一组人员与可以访问虚拟机的人员集提供审核分离。

您可以通过多种方式为 VM 配置角色分配：

- 使用 Azure AD 门户体验
- 使用 Azure 云外壳体验

### <a name="using-azure-ad-portal-experience"></a>使用 Azure AD 门户体验

要为启用 Azure AD 的 Windows 服务器 2019 数据中心 VM 配置角色分配，请执行以下任务：

1. 导航到特定的虚拟机概述页面
1. 从菜单选项中选择**访问控制 （IAM）**
1. 选择 **"添加****"，添加角色分配**以打开"添加角色分配"窗格。
1. 在**角色**下拉列表中，选择**虚拟机管理员登录**或**虚拟机用户登录**等角色。
1. 在 **"选择"** 字段中，选择用户、组、服务主体或托管标识。 如果没有在列表中看到安全主体，则可在“选择”框中键入相应内容，以便在目录中搜索显示名称、电子邮件地址和对象标识符****。
1. 选择 **"保存**"以分配角色。

片刻之后，会在所选范围内为安全主体分配角色。

![将角色分配给将访问 VM 的用户](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>使用 Azure 云外壳体验

以下示例使用 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) 为当前的 Azure 用户分配登录到 VM 所需的“虚拟机管理员登录名”角色。 活动 Azure 帐户的用户名是使用 [az account show](/cli/azure/account#az-account-show) 获得的，而 scope 则设置为在前面的步骤中使用 [az vm show](/cli/azure/vm#az-vm-show) 创建的 VM。 也可在资源组或订阅级别设置 scope，这种情况下会应用正常的 RBAC 继承权限。 有关详细信息，请参阅[基于角色的访问控件](../../virtual-machines/linux/login-using-aad.md)。

```   AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> 如果您的 AAD 域和登录用户名域不匹配，则必须使用 指定用户帐户的对象`--assignee-object-id`ID，而不仅仅是 的`--assignee`用户名。 可以使用 [az ad user list](/cli/azure/ad/user#az-ad-user-list) 获取用户帐户的对象 ID。

有关如何使用 RBAC 管理对 Azure 订阅资源的访问的详细信息，请参阅以下文章：

- [使用 RBAC 和 Azure CLI 管理对 Azure 资源的访问权限](/azure/role-based-access-control/role-assignments-cli)
- [使用 RBAC 和 Azure 门户管理对 Azure 资源的访问权限](/azure/role-based-access-control/role-assignments-portal)
- [使用 RBAC 和 Azure PowerShell 管理对 Azure 资源的访问](/azure/role-based-access-control/role-assignments-powershell)。

## <a name="using-conditional-access"></a>使用条件访问

在授权访问 Azure 中启用 Azure AD 登录的 Windows VM 之前，可以强制实施条件访问策略，如多重身份验证或用户登录风险检查。 要应用条件访问策略，必须从云应用或操作分配选项中选择"Azure Windows VM 登录"应用，然后将登录风险用作条件，和/或需要多重身份验证作为授予访问控制。 

> [!NOTE]
> 如果使用"需要多重身份验证"作为请求访问"Azure Windows VM 登录"应用的授予访问控件，则必须提供多重身份验证声明，作为将 RDP 会话启动到目标 Windows 的客户端的一部分Azure 中的 VM。 在 Windows 10 客户端上实现此目的的唯一方法是使用 Windows Hello 业务 PIN 或与 RDP 客户端进行生物识别身份验证。 在 Windows 10 版本 1809 中，对生物识别身份验证的支持已添加到 RDP 客户端。 使用 Windows Hello 业务身份验证的远程桌面仅适用于使用证书信任模型且当前不适用于密钥信任模型的部署。

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>使用 Azure AD 凭据登录到 Windows VM

> [!IMPORTANT]
> 仅允许连接到 Azure AD 的 VM 的远程连接从 Azure AD 联接的 Windows 10 电脑或与 VM**加入的同一**目录的混合 Azure AD。 此外，对于使用 Azure AD 凭据的 RDP，用户必须属于两个 RBAC 角色之一，即虚拟机管理员登录或虚拟机用户登录。 此时，Azure 堡垒不能用于使用具有 AADLoginForWindows 扩展名的 Azure 活动目录身份验证登录。 仅支持直接 RDP。

要使用 Azure AD 登录到 Windows 服务器 2019 虚拟机，请执行： 

1. 导航到已启用 Azure AD 登录的虚拟机的概览页。
1. 选择 **"连接**"以打开"连接到虚拟机"边栏选项卡。
1. 选择**下载 RDP 文件**。
1. 选择 **"打开"** 以启动远程桌面连接客户端。
1. 选择 **"连接**"以启动 Windows 登录对话框。
1. 使用 Azure AD 凭据登录。

现在，您已登录到 Windows Server 2019 Azure 虚拟机，具有已分配的角色权限，例如 VM 用户或 VM 管理员。 

> [!NOTE]
> 可以保存 。RDP 文件在计算机上的本地启动将来的远程桌面连接到虚拟机，而不必导航到 Azure 门户中的虚拟机概览页面并使用连接选项。

## <a name="troubleshoot"></a>疑难解答

### <a name="troubleshoot-deployment-issues"></a>排查部署问题

AADLoginForWindows 扩展必须成功安装才能使 VM 完成 Azure AD 加入过程。 如果 VM 扩展无法正确安装，请执行以下步骤。

1. RDP 使用本地管理员帐户到 VM，并在下检查 CommandExecuti'n.log  
   
   C：_WindowsAzure_Logs_Plugs_微软.Azure.ActiveDirectory.AADloginForWindows\0.3.1.0。 

   > [!NOTE]
   > 如果扩展在初始失败后重新启动，则具有部署错误的日志将保存为 CommandExecution_YYYYMMDDHHMMSSSSS.log。 "
1. 在 VM 上打开命令提示，并针对在 Azure 主机上运行的实例元数据服务 （IMDS） 终结点验证这些查询：

   | 要运行的命令 | 预期输出 |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | 有关 Azure VM 的正确信息 |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | 与 Azure 订阅关联的有效租户 ID |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Azure 活动目录为分配给此 VM 的托管标识颁发的有效访问令牌 |

   > [!NOTE]
   > 访问令牌可以使用 像 的工具解码[http://calebb.net/](http://calebb.net/)。 验证访问令牌中的"appid"与分配给 VM 的托管标识匹配。

1. 确保使用命令行可从 VM 访问所需的终结点：
   
   - 卷曲 https：\//login.microsoftonline.com/ -D |
   - 卷曲 https：\/`<TenantID>`/login.microsoftonline.com/ / -D |

   > [!NOTE]
   > 替换为`<TenantID>`与 Azure 订阅关联的 Azure AD 租户 ID。

   - 卷曲 https：\//enterpriseregistration.windows.net/ -D -
   - 卷曲 https：\//device.login.microsoftonline.com/ -D -
   - 卷曲 https：\//pas.windows.net/ -D -

1. 可以通过运行`dsregcmd /status`来查看设备状态。 目标是将设备状态显示为`AzureAdJoined : YES`。

   > [!NOTE]
   > Azure AD 联接活动在"用户设备注册"和管理日志下的事件查看器中捕获。

如果 AADLoginForWindows 扩展在某些错误代码下失败，您可以执行以下步骤：

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>问题 1：AADLoginForWindows 扩展无法安装终端错误代码"1007"和退出代码：-2145648574。

此退出代码转换为DSREG_E_MSI_TENANTID_UNAVAILABLE，因为扩展无法查询 Azure AD 租户信息。

1. 验证 Azure VM 可以从实例元数据服务中检索租户 ID。

   - RDP 作为本地管理员向 VM 返回 VM，并通过从 VM 上的提升命令行运行此命令来验证终结点返回有效的租户 ID：
      
      - 卷曲 -H 元数据：truehttp://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. VM 管理员尝试安装 AADLoginForWindows 扩展，但分配了托管标识的系统尚未首先启用 VM。 导航到 VM 的标识边栏选项卡。 从"系统分配"选项卡中，验证状态切换为"打开"。

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>问题 2： AADLoginForWindows 扩展无法安装退出代码： -2145648607

此退出代码转换为DSREG_AUTOJOIN_DISC_FAILED因为扩展无法访问`https://enterpriseregistration.windows.net`终结点。

1. 使用命令行从 VM 可访问所需的终结点：

   - 卷曲 https：\//login.microsoftonline.com/ -D |
   - 卷曲 https：\/`<TenantID>`/login.microsoftonline.com/ / -D |
   
   > [!NOTE]
   > 替换为`<TenantID>`与 Azure 订阅关联的 Azure AD 租户 ID。 如果需要查找租户 ID，可以将鼠标悬停在帐户名称上以获取目录/租户 ID，或者选择 Azure 门户中>属性>目录 ID 中的 Azure 活动目录。

   - 卷曲 https：\//enterpriseregistration.windows.net/ -D -
   - 卷曲 https：\//device.login.microsoftonline.com/ -D -
   - 卷曲 https：\//pas.windows.net/ -D -

1. 如果任何命令在"无法解析主机`<URL>`"时失败，请尝试运行此命令以确定 VM 正在使用的 DNS 服务器。
   
   `nslookup <URL>`

   > [!NOTE] 
   > 替换为`<URL>`终结点使用的完全限定的域名，如"login.microsoftonline.com"。

1. 接下来，查看指定公共 DNS 服务器是否允许命令成功：

   `nslookup <URL> 208.67.222.222`

1. 如有必要，更改分配给 Azure VM 所属的网络安全组的 DNS 服务器。

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>问题 3：AADLoginForWindows 扩展无法使用退出代码安装：51

退出代码 51 转换为"VM 的操作系统不支持此扩展"。

在公共预览版上，AADLoginForWindows 扩展仅用于安装在 Windows 服务器 2019 或 Windows 10（生成 1809 或更高版本）。 确保支持 Windows 版本。 如果不支持 Windows 生成，请卸载 VM 扩展。

### <a name="troubleshoot-sign-in-issues"></a>排查登录问题

当您尝试使用 Azure AD 凭据 RDP 时，某些常见错误包括未分配 RBAC 角色、未经授权的客户端或所需的 2FA 登录方法。 使用以下信息更正这些问题。

可以通过运行`dsregcmd /status`来查看设备和 SSO 状态。 目标是使设备状态显示为`AzureAdJoined : YES`和。 `SSO State` `AzureAdPrt : YES`

此外，使用 Azure AD 帐户的 RDP 登录在 AAD_操作事件日志下的事件查看器中捕获。

#### <a name="rbac-role-not-assigned"></a>未分配 RBAC 角色

如果在启动与 VM 的远程桌面连接时看到以下错误消息： 

- 您的帐户配置为阻止您使用此设备。 有关详细信息，请与系统管理员联系

![您的帐户配置为阻止您使用此设备。](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

验证您是否为授予用户的虚拟机管理员登录或虚拟机用户登录角色授予的 VM 配置了[RBAC 策略](../../virtual-machines/linux/login-using-aad.md)：
 
#### <a name="unauthorized-client"></a>未经授权的客户端

如果在启动与 VM 的远程桌面连接时看到以下错误消息： 

- 您的凭据不起作用

![您的凭据不起作用](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

验证用于启动远程桌面连接的 Windows 10 电脑是 Azure AD 加入的，还是将混合 Azure AD 连接到 VM 加入的同一 Azure AD 目录。 有关设备标识的详细信息，请参阅文章["什么是设备标识](/azure/active-directory/devices/overview)"。

> [!NOTE]
> Windows 10 20H1，将添加对 Azure AD 注册 PC 的支持，以启动到 VM 的远程桌面连接。 加入 Windows 预览体验程序，试用此功能并探索 Windows 10 的新功能。

此外，在 Azure AD 联接完成后，验证 AADLoginForWindows 扩展尚未卸载。
 
#### <a name="mfa-sign-in-method-required"></a>所需的 MFA 登录方法

如果在启动与 VM 的远程桌面连接时看到以下错误消息： 

- 不允许使用登录方法。 请尝试其他登录方法或与系统管理员联系。

![不允许使用登录方法。](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

如果已配置了条件访问策略，该策略在可以访问资源之前需要多重身份验证 （MFA），则需要确保启动远程桌面连接到 VM 的 Windows 10 电脑使用强身份验证方法，如 Windows Hello。 如果不对远程桌面连接使用强身份验证方法，您将看到以前的错误。

如果您尚未部署适用于企业的 Windows Hello，并且现在不是选项，则可以通过配置条件访问策略（将"Azure Windows VM 登录"应用从需要 MFA 的云应用列表中排除 MFA 要求来排除 MFA 要求。 要了解有关适用于企业的 Windows Hello 的详细信息，请参阅[Windows Hello 业务概述](/windows/security/identity-protection/hello-for-business/hello-identity-verification)。

> [!NOTE]
> Windows 10 支持多个版本的 Windows Hello 与 RDP 的业务 PIN 身份验证，但 Windows 10 版本 1809 中添加了对 RDP 生物识别身份验证的支持。 在 RDP 期间使用适用于业务身份验证的 Windows Hello 仅适用于使用证书信任模型且当前不适用于密钥信任模型的部署。
 
## <a name="preview-feedback"></a>预览功能反馈

在[Azure AD 反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上，使用预览功能或报告问题的反馈。

## <a name="next-steps"></a>后续步骤

有关 Azure Active Directory 的详细信息，请参阅[什么是 Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
