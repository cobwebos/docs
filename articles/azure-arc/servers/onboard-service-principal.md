---
title: 将混合计算机大规模连接到 Azure
description: 本文介绍如何使用支持 Azure Arc 的服务器 (预览版) 使用服务主体将计算机连接到 Azure。
ms.date: 07/23/2020
ms.topic: conceptual
ms.openlocfilehash: 07266ce7fb9579e1d4fb1b65394e0b7fdf7aa13d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88211410"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>将混合计算机大规模连接到 Azure

你可以在环境中启用启用了 Azure Arc 的服务器 (预览) ，其中包含几个灵活的选项，具体取决于你的要求。 使用我们提供的模板脚本，可以自动完成每个安装步骤，包括与 Azure Arc 建立连接。但是，必须使用在目标计算机和 Azure 中拥有提升权限的帐户以交互方式执行此脚本。 若要将计算机连接到已启用 Azure Arc 的服务器 (预览版) ，可以使用 Azure Active Directory [服务主体](../../active-directory/develop/app-objects-and-service-principals.md) ，而不是使用特权标识以 [交互方式连接计算机](onboard-portal.md)。 服务主体是一种特殊的受限管理标识，它只被授予了使用 `azcmagent` 命令将计算机连接到 Azure 所需的最低权限。 这比使用较高特权的帐户（例如租户管理员）更安全，并且可以遵循我们的访问控制安全性最佳做法。 服务主体只会在加入期间使用，不会用于任何其他目的。  

安装和配置 Connected Machine 代理的安装方法要求你在计算机上拥有管理员权限。 在 Linux 上，需使用 root 帐户；在 Windows 上，需要以“本地管理员组”的成员身份使用这些方法。

在开始之前，请务必查看[先决条件](agent-overview.md#prerequisites)，并验证你的订阅和资源是否符合要求。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

在此过程结束时，已成功将混合计算机连接到已启用 Azure Arc 的服务器 (预览) 。

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>创建服务主体以用于大规模加入

可以在 [Azure PowerShell](/powershell/azure/install-az-ps) 中使用 [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) cmdlet 创建服务主体。 或者，可以按照[使用 Azure 门户创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)中列出的步骤完成此任务。

> [!NOTE]
> 创建服务主体时，你的帐户必须是要用于加入的订阅中的“所有者”或“用户访问管理员”。 如果你没有足够的权限创建角色分配，则可能会创建服务主体，但它将无法加入计算机。
>

若要使用 PowerShell 创建服务主体，请执行以下操作。

1. 运行以下命令。 必须在变量中存储 [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) cmdlet 的输出，否则无法检索需要在后续步骤中使用的密码。

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. 若要检索 `$sp` 变量中存储的密码，请运行以下命令：

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. 在输出中，找到并复制 **password** 字段下的密码值。 另外，还请找到并复制 **ApplicationId** 字段下的值。 请在安全的位置保存这些值，供稍后使用。 如果忘记或丢失了服务主体密码，可以使用 [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) cmdlet 重置它。

以下属性中的值将与传递给 `azcmagent` 的参数配合使用：

* **ApplicationId** 属性中的值用作 `--service-principal-id` 参数值
* **password** 属性中的值用作连接代理时所用的 `--service-principal-secret` 参数。

> [!NOTE]
> 请确保使用服务主体 **ApplicationId** 属性，而不是 **Id** 属性。
>

“Azure Connected Machine 加入”角色只包含加入计算机时所需的权限。 可以分配服务主体权限，以允许其范围包含资源组或订阅。 若要添加角色分配，请参阅[使用 Azure RBAC 和 Azure 门户添加或删除角色分配](../../role-based-access-control/role-assignments-portal.md)或者[使用 Azure RBAC 和 Azure CLI 添加或删除角色分配](../../role-based-access-control/role-assignments-cli.md)。

## <a name="install-the-agent-and-connect-to-azure"></a>安装代理并连接到 Azure

以下步骤使用脚本模板在混合计算机上安装并配置 Connected Machine 代理，该模板执行的步骤与[从 Azure 门户将混合计算机连接到 Azure](onboard-portal.md) 一文中所述的步骤类似。 不同之处在于，最后一步是通过 `azcmagent` 命令使用服务主体与 Azure Arc 建立连接。

下面是配置用于服务主体的 `azcmagent` 命令时需要指定的设置。

* `tenant-id`：表示 Azure AD 专用实例的唯一标识符 (GUID)。
* `subscription-id`：计算机要属于的 Azure 订阅的订阅 ID (GUID)。
* `resource-group`：连接的计算机要属于的资源组的名称。
* `location`：请参阅[支持的 Azure 区域](overview.md#supported-regions)。 此位置可以与资源组的位置相同或不同。
* `resource-name`：（可选）用于本地计算机的 Azure 资源表示。 如果未指定此值，将使用计算机主机名。

若要详细了解 `azcmagent` 命令行工具，请查看 [Azcmagent 参考](./manage-agent.md)。

### <a name="windows-installation-script"></a>Windows 安装脚本

下面是适用于 Windows 的 Connected Machine 代理安装脚本示例，该脚本已修改为使用服务主体来支持完全自动化的非交互式代理安装。

```
 # Download the package
function download() {$ProgressPreference="SilentlyContinue"; Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi}
download

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String

# Run connect command
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
```

>[!NOTE]
>此脚本仅支持在64位版本的 Windows PowerShell 中运行。
>

### <a name="linux-installation-script"></a>Linux 安装脚本

下面是适用于 Linux 的 Connected Machine 代理安装脚本示例，该脚本已修改为使用服务主体来支持完全自动化的非交互式代理安装。

```
# Download the installation package
wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh

# Install the hybrid agent
bash ~/install_linux_azcmagent.sh

# Run connect command
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
```

>[!NOTE]
>若要运行**azcmagent**，必须具有 Linux 计算机上的*根*访问权限。

安装代理并将其配置为连接到已启用 Azure Arc 的服务器 (预览) ，请参阅 Azure 门户，验证服务器是否已成功连接。 在 [Azure 门户](https://aka.ms/hybridmachineportal)中查看计算机。

![服务器连接成功](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>后续步骤

- 了解如何使用 [Azure Policy](../../governance/policy/overview.md) 管理计算机，例如，进行 VM [来宾配置](../../governance/policy/concepts/guest-configuration.md)，验证计算机是否向预期的 Log Analytics 工作区报告，使用[用于 VM 的 Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md) 启用监视等。

- 详细了解 [Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)。 若要主动监视计算机上运行的 OS 和工作负荷、使用自动化 Runbook 或更新管理等解决方案对其进行管理，或使用其他 Azure 服务（例如 [Azure 安全中心](../../security-center/security-center-intro.md)），需要安装适用于 Windows 和 Linux 的 Log Analytics 代理。