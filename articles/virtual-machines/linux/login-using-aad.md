---
title: 使用 Azure Active Directory 凭据登录到 Linux VM | Microsoft Docs
description: 本操作指南介绍如何创建和配置 Linux VM，以便使用 Azure Active Directory 身份验证进行用户登录
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/17/2018
ms.author: cynthn
ms.openlocfilehash: 614375c95f4af3a5fbeeb4368ff8c577372e6381
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37933949"
---
# <a name="log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>使用 Azure Active Directory 身份验证（预览版）登录到 Azure 中的 Linux 虚拟机

若要改进 Azure 中 Linux 虚拟机 (VM) 的安全性，可以与 Azure Active Directory (AD) 身份验证集成。 将 Azure AD 身份验证用于 Linux VM 时，可以通过集中控制和强制实施策略来允许或拒绝对 VM 的访问。 本文介绍如何创建和配置 Linux VM，以便使用 Azure AD 身份验证。

> [!NOTE]
> 此功能为预览版，不建议用于生产性虚拟机或工作负荷。 请在测试后就丢弃的测试性虚拟机上使用此功能。

在 Azure 中使用 Azure AD 身份验证登录到 Linux VM 有许多好处，其中包括：

- **提高安全性：**
  - 可以使用公司 AD 凭据登录到 Azure Linux VM。 不需创建本地管理员帐户并管理凭据生存期。
  - 减少对本地管理员帐户的依赖以后，就不需要担心凭据丢失/失窃、用户配置的凭据过弱，等等。
  - 针对 Azure AD 目录配置的密码复杂性和密码生存期策略也有助于维护 Linux VM 安全性。
  - 若要进一步确保 Azure 虚拟机的登录安全性，可以配置多重身份验证。
  - 使用 Azure Active Directory 登录 Linux VM 这一功能也适用于使用[联合身份验证服务](../../active-directory/connect/active-directory-aadconnectfed-whatis.md)的客户。

- **无缝协作：** 可以使用基于角色的访问控制 (RBAC)，指定能够以常规用户或管理员特权用户身份登录到给定 VM 的人员。 当用户加入或离开你的团队时，你可以更新 VM 的 RBAC 策略，根据需要授予访问权限。 与必须通过清理 VM 来删除不必要的 SSH 公钥相比，这种体验要简单得多。 员工在离开你的组织时，其用户帐户会被禁用或从 Azure AD 中删除，然后他们就再也不能访问你的资源。

### <a name="supported-azure-regions-and-linux-distributions"></a>受支持的 Azure 区域和 Linux 发行版

下面是目前（此功能的预览期间）受支持的 Linux 发行版：

| 分发 | 版本 |
| --- | --- |
| CentOS | CentOS 6.9 和 CentOS 7.4 |
| Debian | Debian 9 |
| RedHat Enterprise Linux | RHEL 6 和 RHEL 7 | 
| Ubuntu Server | Ubuntu 14.04 LTS、Ubuntu Server 16.04、Ubuntu Server 17.10 和 Ubuntu Server 18.04 |

下面是目前（此功能的预览期间）受支持的 Azure 区域：

- 所有全局 Azure 区域

>[!IMPORTANT]
> 若要使用此预览版功能，请只部署受支持的 Linux 发行版，并且只在受支持的 Azure 区域部署。 此功能在 Azure 政府版或主权云中不受支持。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.31 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="create-a-linux-virtual-machine"></a>创建 Linux 虚拟机

使用 [az group create](/cli/azure/group#az-group-create) 创建资源组，然后使用 [az vm create](/cli/azure/vm#az-vm-create) 创建 VM。这两项操作都是使用支持的发行版在支持的区域进行。 以下示例将使用 *Ubuntu 16.04 LTS* 的名为 *myVM* 的 VM 部署到 *southcentralus* 区域的名为 *myResourceGroup* 的资源组中。 在以下示例中，可以根据需要提供自己的资源组和 VM 名称。

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

创建 VM 和支持资源需要几分钟时间。

## <a name="install-the-azure-ad-login-vm-extension"></a>安装 Azure AD 登录 VM 扩展

若要使用 Azure AD 凭据登录到 Linux VM，请安装 VM 扩展中的 Azure Active Directory 日志。 VM 扩展是小型应用程序，可在 Azure 虚拟机上提供部署后配置和自动化任务。 请使用 [az vm extension set](/cli/azure/vm/extension#az-vm-extension-set) 在 *myResourceGroup* 资源组中名为 *myVM* 的 VM 上安装 *AADLoginForLinux* 扩展：

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

在 VM 上安装扩展以后，就会显示 *provisioningState* 为 *Succeeded*。

## <a name="configure-role-assignments-for-the-vm"></a>为 VM 配置角色分配

Azure 基于角色的访问控制 (RBAC) 策略决定了谁能登录到 VM。 使用两个 RBAC 角色来授权 VM 登录：

- **虚拟机管理员登录名**：分配了此角色的用户可以使用 Windows 管理员或 Linux root 用户权限登录到 Azure 虚拟机。
- **虚拟机用户登录名**：分配了此角色的用户可以使用常规用户权限登录到 Azure 虚拟机。

> [!NOTE]
> 若要允许用户通过 SSH 登录到 VM，必须分配“虚拟机管理员登录名”或“虚拟机用户登录名”角色。 分配了 VM“所有者”或“参与者”角色的 Azure 用户不会自动获得通过 SSH 登录到 VM 的权限。

以下示例使用 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) 为当前的 Azure 用户分配登录到 VM 所需的“虚拟机管理员登录名”角色。 活动 Azure 帐户的用户名是使用 [az account show](/cli/azure/account#az-account-show) 获得的，而 *scope* 则设置为在前面的步骤中使用 [az vm show](/cli/azure/vm#az-vm-show) 创建的 VM。 也可在资源组或订阅级别设置 scope，这种情况下会应用正常的 RBAC 继承权限。 有关详细信息，请参阅[基于角色的访问控制](../../azure-resource-manager/resource-group-overview.md#access-control)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> 如果 AAD 域和登录用户名域不匹配，则必须使用 *--assignee-object-id* 指定用户帐户的对象 ID，而不是仅指定 *--assignee* 的用户名。 可以使用 [az ad user list](/cli/azure/ad/user#az-ad-user-list) 获取用户帐户的对象 ID。

若要详细了解如何使用 RBAC 来管理对 Azure 订阅资源的访问权限，请参阅 [Azure CLI 2.0](../../role-based-access-control/role-assignments-cli.md)、[Azure 门户](../../role-based-access-control/role-assignments-portal.md)或 [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md) 的使用指南。

也可对 Azure AD 进行配置，要求特定的用户通过多重身份验证登录到 Linux 虚拟机。 有关详细信息，请参阅[云中的 Azure 多重身份验证入门](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)。

## <a name="log-in-to-the-linux-virtual-machine"></a>登录到 Linux 虚拟机

首先，使用 [az vm show](/cli/azure/vm#az-vm-show) 查看 VM 的 公共 IP 地址：

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

使用 Azure AD 凭据登录到 Azure Linux 虚拟机。 可以通过 `-l` 参数指定自己的 Azure AD 帐户地址。 将 VM 的公共 IP 地址指定为上一命令中的输出：

```azurecli-interactive
ssh -l azureuser@contoso.onmicrosoft.com publicIps
```

系统会提示你在 [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) 中使用一次性的使用代码登录到 Azure AD。 将一次性的使用代码复制并粘贴到设备登录页中，如以下示例所示：

```bash
~$ ssh -l azureuser@contoso.onmicrosoft.com 13.65.237.247
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJS3K6X4D to authenticate. Press ENTER when ready.
```

出现提示时，请在登录页中输入 Azure AD 登录凭据。 成功地进行身份验证以后，Web 浏览器中会显示以下消息：

    You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.

关闭浏览器窗口，返回到 SSH 提示符窗口，然后按 **Enter** 键。 现在，你已使用分配的角色权限（例如“VM 用户”或“VM 管理员”）登录到 Azure Linux 虚拟机。 如果你的用户帐户分配了“虚拟机管理员登录名”角色，则可使用 `sudo` 运行需要 root 权限的命令。

## <a name="troubleshoot-sign-in-issues"></a>排查登录问题

尝试使用 Azure AD 凭据进行 SSH 登录时，会出现一些常见的错误，其中包括：未分配 RBAC 角色、系统重复提示你登录。 请根据以下部分的说明来纠正这些问题。

### <a name="access-denied-rbac-role-not-assigned"></a>访问被拒绝：未分配 RBAC 角色

如果在 SSH 提示符窗口中看到以下错误，请验证是否已为授予用户“虚拟机管理员登录名”或“虚拟机用户登录名”角色的 VM [配置 RBAC 策略](#configure-rbac-policy-for-the-virtual-machine)：

```bash
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>持续的 SSH 登录提示

如果你在 Web 浏览器中成功完成了身份验证步骤，系统会立即提示你使用新的代码再次登录。 出现此错误通常是由于在 SSH 提示符窗口中指定的登录名称与登录到 Azure AD 时使用的帐户不符。 若要纠正此问题，请执行以下操作：

- 验证在 SSH 提示符窗口中指定的登录名是否正确。 登录名拼写错误可能导致在 SSH 提示符窗口中指定的登录名称与登录到 Azure AD 时使用的帐户不符。 例如，键入了 *azuresuer@contoso.onmicrosoft.com* 而非 *azureuser@contoso.onmicrosoft.com*。
- 如果有多个用户帐户，请确保登录到 Azure AD 时在浏览器窗口中提供的用户帐户是相同的。
- Linux 是区分大小写的操作系统。 “Azureuser@contoso.onmicrosoft.com”和“azureuser@contoso.onmicrosoft.com”是不同的，会导致不匹配。 请确保在 SSH 提示符窗口中使用正确的大小写指定 UPN。

## <a name="preview-feedback"></a>预览功能反馈

在 [Azure AD 反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上分享此预览功能的反馈或报告使用时存在的问题

## <a name="next-steps"></a>后续步骤

有关 Azure Active Directory 的详细信息，请参阅[什么是 Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) 和 [Azure Active Directory 入门方式](../../active-directory/fundamentals/get-started-azure-ad.md)
