---
title: 使用 Azure Active Directory 登录到 Azure 中的 Windows 虚拟机（预览版）
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
ms.openlocfilehash: 70fe718884796ac127be38c375003dd728089be8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016028"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>使用 Azure Active Directory 身份验证（预览版）登录到 Azure 中的 Windows 虚拟机

组织现在可以利用运行**Windows Server 2019 Datacenter edition**或**windows 10 1809**及更高版本的 Azure 虚拟机（vm）的 Azure Active Directory （AD）身份验证。 通过使用 Azure AD 向 Vm 进行身份验证，可以集中控制和强制实施策略。 Azure 基于角色的访问控制（RBAC）和 Azure AD 条件性访问等工具可让你控制谁可以访问 VM。 本文介绍如何创建和配置 Windows Server 2019 VM 以使用 Azure AD 身份验证。

|     |
| --- |
| Azure Windows Vm Azure AD 登录是 Azure Active Directory 的公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

使用 Azure AD 身份验证登录到 Azure 中的 Windows Vm 有很多好处，包括：

- 利用你通常使用的相同联合或托管 Azure AD 凭据。
- 不再需要管理本地管理员帐户。
- Azure RBAC 允许根据需要授予 Vm 的适当访问权限，并在不再需要时将其删除。
- 在允许访问 VM 之前，Azure AD 条件访问可强制执行其他要求，例如： 
   - 多重身份验证
   - 登录风险检查
- 自动执行和缩放 Azure AD 加入适用于你的 VDI 部署的 Azure Windows Vm。

> [!NOTE]
> 启用此功能后，将 Azure AD 加入 Azure 中的 Windows Vm。 不能将其加入其他域，如本地 AD 或 Azure AD DS。 如果需要执行此操作，需要通过卸载扩展将 VM 从 Azure AD 租户断开连接。

## <a name="requirements"></a>要求

### <a name="supported-azure-regions-and-windows-distributions"></a>支持的 Azure 区域和 Windows 分发

预览此功能时，当前支持以下 Windows 分发：

- Windows Server 2019 Datacenter
- Windows 10 1809 及更高版本

> [!IMPORTANT]
> 仅允许从 Azure AD 加入或混合 Azure AD 加入到 VM 的**同一**目录中的 Windows 10 电脑连接到连接到 Azure AD 的 vm 的远程连接。 

下面是目前（此功能的预览期间）受支持的 Azure 区域：

- 所有 Azure 全局区域

> [!IMPORTANT]
> 若要使用此预览功能，请仅在受支持的 Azure 区域中部署受支持的 Windows 分发版。 Azure 政府版或主权云中当前不支持此功能。

### <a name="network-requirements"></a>网络要求

若要为 Azure 中的 Windows Vm 启用 Azure AD 身份验证，需要确保 Vm 网络配置允许通过 TCP 端口443对以下终结点进行出站访问：

- https：\//enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- https：\//device.login.microsoftonline.com
- https：\//pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>在 Azure 中启用 Windows VM 的 Azure AD 登录

若要在 Azure 中使用 Windows VM Azure AD 登录，需要首先为 Windows VM 启用 Azure AD login 选项，然后需要为有权登录到 VM 的用户配置 RBAC 角色分配。
可以通过多种方式为 Windows VM 启用 Azure AD 登录：

- 在创建 Windows VM 时使用 Azure 门户体验
- 在创建 Windows VM**或现有 WINDOWS vm**时使用 Azure Cloud Shell 体验

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>使用 Azure 门户创建 VM 体验来启用 Azure AD 登录

可以为 Windows Server 2019 Datacenter、Windows 10 1809 和更高版本的 VM 映像启用 Azure AD 登录。 

使用 Azure AD 登录名在 Azure 中创建 Windows Server 2019 Datacenter VM： 

1. 使用有权创建 Vm 的帐户登录到[Azure 门户](https://portal.azure.com)，并选择 " **+ 创建资源**"。
1. 在 "搜索 Marketplace" 搜索栏中键入 " **Windows Server** "。
   1. 单击 " **Windows server** "，然后从 "选择软件计划" 下拉列表中选择 " **Windows server 2019 Datacenter** "。
   1. 单击“创建”。
1. 在 "管理" 选项**卡上，** 在 "Azure Active Directory" 部分中，启用 "**使用 AAD 凭据登录" 选项（预览）** 。
1. 请确保 "标识" 部分下的 "**系统分配的托管标识**" 设置为 **"开**"。 使用 Azure AD 凭据启用登录后，应会自动执行此操作。
1. 完成创建虚拟机的其余体验。 在此预览版中，你将需要为 VM 创建管理员用户名和密码。

![使用 Azure AD 凭据登录创建 VM](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> 若要使用 Azure AD 凭据登录到 VM，首先需要为 VM 配置角色分配，如以下某个部分所述。

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>使用 Azure Cloud Shell 体验启用 Azure AD 登录

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的操作步骤。 Cloud Shell 中预安装并配置了常用的 Azure 工具以供你的帐户使用。 只需要选择“复制”按钮来复制代码，将其粘贴到 Cloud Shell 中，然后按 Enter 键来运行它。 可通过多种方式打开 Cloud Shell：

选择代码块右上角的 "试用"。
在浏览器中打开 Cloud Shell。
选择[Azure 门户](https://portal.azure.com)右上角菜单中的 "Cloud Shell" 按钮。

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 版本2.0.31 或更高版本。 若要查找版本，请运行 az --version。 如果需要安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)一文。

1. 使用 [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) 创建资源组。 
1. 使用[az vm create](https://docs.microsoft.com/cli/azure/vm#az-vm-create)创建 vm，并在受支持的区域中使用受支持的分发版。 
1. 安装 Azure AD 登录 VM 扩展。 

下面的示例将一个名为 myVM 的 VM 部署到 default-machinelearning-southcentralus 区域中名为 myResourceGroup 的资源组中。 在以下示例中，可以根据需要提供自己的资源组和 VM 名称。

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
> 必须先在虚拟机上启用系统分配的托管标识，然后才能安装 Azure AD 登录 VM 扩展。

创建 VM 和支持资源需要几分钟时间。

最后，安装 Azure AD 登录 VM 扩展，以便为 Windows VM 启用 Azure AD 登录。 VM 扩展是小型应用程序，可在 Azure 虚拟机上提供部署后配置和自动化任务。 使用[az vm extension](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) set，在 myResourceGroup 资源组中名为 MYVM 的 vm 上安装 AADLoginForWindows 扩展：

> [!NOTE]
> 可以在现有的 Windows Server 2019 或 Windows 10 1809 及更高版本的 VM 上安装 AADLoginForWindows 扩展，以便 Azure AD 身份验证。 下面显示了 AZ CLI 的示例。

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

当在 VM 上安装扩展后，将显示 `Succeeded` 的 `provisioningState`。

## <a name="configure-role-assignments-for-the-vm"></a>为 VM 配置角色分配

创建 VM 后，需要配置 Azure RBAC 策略来确定哪些用户可以登录到该 vm。 使用两个 RBAC 角色来授权 VM 登录：

- **虚拟机管理员登录**：分配了此角色的用户可以使用管理员权限登录到 Azure 虚拟机。
- **虚拟机用户登录名**：分配了此角色的用户可以使用常规用户权限登录到 Azure 虚拟机。

> [!NOTE]
> 若要允许用户通过 RDP 登录到 VM，你必须分配 "虚拟机管理员登录名" 或 "虚拟机用户登录" 角色。 为 VM 分配所有者或参与者角色的 Azure 用户不会自动拥有通过 RDP 登录到 VM 的权限。 这是为了提供控制虚拟机的一组人员与可访问虚拟机的一组用户之间经过审核的隔离。

可以通过多种方式为 VM 配置角色分配：

- 使用 Azure AD 门户体验
- 使用 Azure Cloud Shell 体验

### <a name="using-azure-ad-portal-experience"></a>使用 Azure AD 门户体验

为启用了 Windows Server 2019 Datacenter Vm 的 Azure AD 配置角色分配：

1. 导航到 "特定虚拟机概述" 页
1. 从菜单选项中选择 "**访问控制（IAM）** "
1. 依次选择 "**添加**"、"**添加角色分配**"，以打开 "添加角色分配" 窗格。
1. 在 "**角色**" 下拉列表中，选择一个角色，如 "**虚拟机管理员登录名**" 或 "**虚拟机用户登录名**"。
1. 在 "**选择**" 字段中，选择用户、组、服务主体或托管标识。 如果没有在列表中看到安全主体，则可在“选择”框中键入相应内容，以便在目录中搜索显示名称、电子邮件地址和对象标识符。
1. 选择 "**保存**" 以分配角色。

片刻之后，会在所选范围内为安全主体分配角色。

![向将访问 VM 的用户分配角色](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>使用 Azure Cloud Shell 体验

下面的示例使用[az role 赋值 create](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create)为当前 Azure 用户将虚拟机管理员登录角色分配给 VM。 使用[az account show](https://docs.microsoft.com/cli/azure/account#az-account-show)获取活动 Azure 帐户的用户名，并使用[az VM show](https://docs.microsoft.com/cli/azure/vm#az-vm-show)将作用域设置为上一步中创建的 VM。 也可在资源组或订阅级别设置 scope，这种情况下会应用正常的 RBAC 继承权限。 有关详细信息，请参阅[基于角色的访问控制](../../virtual-machines/linux/login-using-aad.md)。

```AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> 如果 AAD 域和登录用户名域不匹配，则必须指定用户 `--assignee-object-id`帐户的对象 ID，而不只是 `--assignee`的用户名。 可以使用 [az ad user list](https://docs.microsoft.com/cli/azure/ad/user#az-ad-user-list) 获取用户帐户的对象 ID。

有关如何使用 RBAC 管理对 Azure 订阅资源的访问的详细信息，请参阅以下文章：

- [使用 RBAC 和 Azure CLI 管理对 Azure 资源的访问](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)
- [使用 RBAC 和 Azure 门户管理对 Azure 资源的访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
- [使用 RBAC 和 Azure PowerShell 管理对 Azure 资源的访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)。

## <a name="using-conditional-access"></a>使用条件性访问

在授权访问通过 Azure AD 登录启用的 Azure 中的 Windows Vm 之前，可以强制实施条件访问策略（如多重身份验证或用户登录风险检查）。 若要应用条件性访问策略，你必须从 "云应用" 或 "操作" 分配选项中选择 "Azure Windows VM 登录" 应用，然后将登录风险用作条件，并/或需要多重身份验证作为授权访问控制。 

> [!NOTE]
> 如果你使用 "需要多重身份验证" 作为授权访问控制来请求访问 "Azure Windows VM 登录" 应用，则必须将多重身份验证声明作为启动与目标 Windows 的 RDP 会话的客户端的一部分提供Azure 中的 VM。 在 Windows 10 客户端上实现此目的的唯一方法是将 Windows Hello 企业版 PIN 或生物识别身份验证用于 RDP 客户端。 对生物识别身份验证的支持已添加到 Windows 10 版本1809中的 RDP 客户端。 使用 Windows Hello 企业版身份验证的远程桌面仅适用于使用证书信任模型并且当前不可用于密钥信任模式的部署。

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>使用 Windows VM Azure AD 凭据登录

> [!IMPORTANT]
> 仅允许从 Azure AD 加入或混合 Azure AD 加入到 VM 的**同一**目录中的 Windows 10 电脑连接到连接到 Azure AD 的 vm 的远程连接。 此外，对于使用 Azure AD 凭据的 RDP，用户必须属于两个 RBAC 角色之一： "虚拟机管理员登录名" 或 "虚拟机用户登录名"。 目前，Azure 堡垒不能用于使用 AADLoginForWindows 扩展进行 Azure Active Directory 身份验证登录。 仅支持直接 RDP。

使用 Azure AD 登录到 Windows Server 2019 虚拟机： 

1. 导航到已启用 Azure AD 登录的虚拟机的 "概述" 页。
1. 选择 "**连接**" 以打开 "连接到虚拟机" 边栏选项卡。
1. 选择“下载 RDP 文件”。
1. 选择 "**打开**" 以启动远程桌面连接客户端。
1. 选择 "**连接**" 以启动 Windows 登录对话框。
1. 使用 Azure AD 凭据进行登录。

你现在已登录到具有分配的角色权限的 Windows Server 2019 Azure 虚拟机，例如 VM 用户或 VM 管理员。 

> [!NOTE]
> 可以保存。RDP 文件本地计算机上用于启动到虚拟机的远程桌面连接，而无需导航到 Azure 门户中的虚拟机概述页面并使用连接选项。

## <a name="troubleshoot"></a>故障排除

### <a name="troubleshoot-deployment-issues"></a>排查部署问题

必须成功安装 AADLoginForWindows 扩展，才能让 VM 完成 Azure AD 联接过程。 如果 VM 扩展无法正确安装，请执行以下步骤。

1. 使用本地管理员帐户通过 RDP 连接到 VM，并检查 Commandexecution.log  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > 如果在第一次失败后重新启动扩展，则包含部署错误的日志将保存为 CommandExecution_YYYYMMDDHHMMSSSSS .log。 

1. 在 VM 上打开命令提示符，并针对 Azure 主机上运行的实例元数据服务（IMDS）终结点验证这些查询：

   | 要运行的命令 | 预期输出 |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | 更正有关 Azure VM 的信息 |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | 与 Azure 订阅关联的有效租户 ID |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | 为分配到此 VM 的托管标识 Azure Active Directory 颁发的有效访问令牌 |

   > [!NOTE]
   > 使用[http://calebb.net/](http://calebb.net/)之类的工具可以对访问令牌进行解码。 验证访问令牌中的 "appid" 是否与分配给 VM 的托管标识匹配。

1. 请确保使用命令行从 VM 访问所需的终结点：
   
   - 卷 https：\//login.microsoftonline.com/-D –
   - 卷 https：\//login.microsoftonline.com/`<TenantID>`/-D –

   > [!NOTE]
   > 将 `<TenantID>` 替换为与 Azure 订阅关联的 Azure AD 租户 ID。

   - 卷 https：\//enterpriseregistration.windows.net/
   - 卷 https：\//device.login.microsoftonline.com/
   - 卷 https：\//pas.windows.net/

1. 可以通过运行 `dsregcmd /status`查看设备状态。 目标是将设备状态显示为 `AzureAdJoined : YES`。

   > [!NOTE]
   > Azure AD 联接活动在事件查看器中捕获到用户设备 Registration\Admin 日志下。

如果 AADLoginForWindows 扩展失败，并出现特定的错误代码，可以执行以下步骤：

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>问题1：无法安装 AADLoginForWindows 扩展，出现终端错误代码 "1007" 和退出代码：-2145648574。

此退出代码转换为 DSREG_E_MSI_TENANTID_UNAVAILABLE，因为扩展无法查询 Azure AD 租户信息。

1. 验证 Azure VM 能否从实例元数据服务检索 TenantID。

   - 以本地管理员身份通过 RDP 连接到 VM，并验证终结点是否返回有效的租户 ID，方法是从 VM 上提升的命令行运行以下命令：
      
      - 卷曲的元数据： true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. VM 管理员尝试安装 AADLoginForWindows 扩展，但系统分配的托管标识尚未启用 VM。 导航到 VM 的 "标识" 边栏选项卡。 从 "系统分配" 选项卡上，验证状态是否已切换为 "开"。

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>问题2： AADLoginForWindows 扩展安装失败，退出代码为：-2145648607

此退出代码转换为 DSREG_AUTOJOIN_DISC_FAILED，因为扩展无法到达 https://enterpriseregistration.windows.net 终结点。

1. 使用命令行验证是否可以从 VM 访问所需的终结点：

   - 卷 https：\//login.microsoftonline.com/-D –
   - 卷 https：\//login.microsoftonline.com/`<TenantID>`/-D –
   
   > [!NOTE]
   > 将 `<TenantID>` 替换为与 Azure 订阅关联的 Azure AD 租户 ID。 如果需要查找租户 ID，你可以将鼠标悬停在帐户名称上以获取目录/租户 ID，或在 Azure 门户中选择 Azure Active Directory > 属性 > 目录 ID。

   - 卷 https：\//enterpriseregistration.windows.net/
   - 卷 https：\//device.login.microsoftonline.com/
   - 卷 https：\//pas.windows.net/

1. 如果任何命令失败，并出现 "无法解析主机 `<URL>`"，请尝试运行以下命令来确定 VM 正在使用的 DNS 服务器。
   
   `nslookup <URL>`

   > [!NOTE] 
   > 将 `<URL>` 替换为终结点所使用的完全限定的域名，例如 "login.microsoftonline.com"。

1. 接下来，请参阅是否指定公共 DNS 服务器允许命令成功：

   `nslookup <URL> 208.67.222.222`

1. 如有必要，请更改分配给 Azure VM 所属的网络安全组的 DNS 服务器。

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>问题3： AADLoginForWindows 扩展安装失败，退出代码为：51

退出代码51转换为 "VM 的操作系统不支持此扩展"。

在公共预览版中，AADLoginForWindows 扩展仅适用于在 Windows Server 2019 或 Windows 10 （版本1809或更高版本）上安装。 确保支持 Windows 的版本。 如果不支持 Windows 的生成，请卸载 VM 扩展。

### <a name="troubleshoot-sign-in-issues"></a>排查登录问题

当你尝试将 RDP 与 Azure AD 凭据结合时，某些常见错误包括未分配的 RBAC 角色、未经授权的客户端或所需的2FA 登录方法。 使用以下信息来更正这些问题。

可以通过运行 `dsregcmd /status`查看设备和 SSO 状态。 目标是将设备状态显示为 `AzureAdJoined : YES`，并 `SSO State` 显示 `AzureAdPrt : YES`。

此外，在 AAD\Operational 事件日志下的事件查看器中捕获使用 Azure AD 帐户的 RDP 登录。

#### <a name="rbac-role-not-assigned"></a>未分配 RBAC 角色

如果你在启动到 VM 的远程桌面连接时看到以下错误消息： 

- 你的帐户已配置为阻止你使用此设备。 有关详细信息，请与系统管理员联系

![你的帐户已配置为阻止你使用此设备。](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

验证你为 VM[配置了 RBAC 策略](../../virtual-machines/linux/login-using-aad.md)，该策略向用户授予 "虚拟机管理员登录名" 或 "虚拟机用户登录" 角色：
 
#### <a name="unauthorized-client"></a>未经授权的客户端

如果你在启动到 VM 的远程桌面连接时看到以下错误消息： 

- 你的凭据不起作用

![你的凭据不起作用](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

验证你用来启动远程桌面连接的 Windows 10 电脑是 Azure AD 联接的，或者是连接到你的 VM 加入同一 Azure AD 目录的混合 Azure AD。 有关设备标识的详细信息，请参阅[什么是设备标识一](https://docs.microsoft.com/azure/active-directory/devices/overview)文。

> [!NOTE]
> Windows 10 20H1 将添加对 Azure AD 已注册电脑的支持，以启动到 VM 的远程桌面连接。 加入 Windows 预览体验计划以试用并探索 Windows 10 的新功能。

此外，请在 Azure AD 联接完成后验证是否未卸载 AADLoginForWindows 扩展。
 
#### <a name="mfa-sign-in-method-required"></a>需要 MFA 登录方法

如果你在启动到 VM 的远程桌面连接时看到以下错误消息： 

- 不允许使用尝试使用的登录方法。 请尝试其他登录方法，或与系统管理员联系。

![不允许使用尝试使用的登录方法。](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

如果你配置了要求多重身份验证（MFA）的条件性访问策略，然后才能访问该资源，则需要确保 Windows 10 电脑启动到 VM 的远程桌面连接使用强身份验证方法，例如 Windows Hello。 如果未对远程桌面连接使用强身份验证方法，则会看到以前的错误。

如果你尚未部署 Windows Hello for Business，并且此选项不是目前的选项，则可以通过配置条件访问策略来排除 MFA 要求，此条件访问策略从需要 MFA 的云应用列表中排除 "Azure Windows VM 登录" 应用。 若要了解有关 Windows Hello 企业版的详细信息，请参阅[Windows Hello 企业版概述](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)。

> [!NOTE]
> Windows 10 针对多种版本支持使用 RDP 进行 windows Hello 企业版 PIN 身份验证，但 Windows 10 版本1809中添加了对使用 RDP 的生物识别身份验证的支持。 在 RDP 期间使用 Windows Hello 企业版身份验证仅适用于使用证书信任模型并且当前不可用于密钥信任模式的部署。
 
## <a name="preview-feedback"></a>预览功能反馈

请在[Azure AD 反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上分享有关此预览功能的反馈或报告问题。

## <a name="next-steps"></a>后续步骤
有关 Azure Active Directory 的详细信息，请参阅[什么是 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
