---
title: 大规模将混合计算机连接到 Azure
description: 本文介绍如何使用服务主体在服务器（预览版）中使用 Azure Arc 将计算机连接到 Azure。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192267"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>大规模将混合计算机连接到 Azure

你可以为环境中的多个 Windows 或 Linux 计算机启用适用于服务器（预览版）的 Azure Arc，其中包含几个灵活的选项，具体取决于你的要求。 使用我们提供的模板脚本，你可以自动执行安装的每个步骤，包括建立与 Azure Arc 的连接。但是，你需要使用在目标计算机和 Azure 中具有提升权限的帐户以交互方式执行此脚本。 若要将计算机连接到服务器的 Azure Arc，可以使用 Azure Active Directory[服务主体](../../active-directory/develop/app-objects-and-service-principals.md)，而不是使用特权标识以[交互方式连接计算机](onboard-portal.md)。 服务主体是一个特殊的有限管理标识，只授予使用 `azcmagent` 命令将计算机连接到 Azure 所需的最小权限。 这比使用较高特权帐户（如租户管理员）更安全，并遵循我们的访问控制安全最佳实践。 仅在载入期间使用服务主体，而不会将其用于任何其他目的。  

安装和配置连接的计算机代理的安装方法要求使用的自动方法在计算机上具有管理员权限。 在 Linux 上，通过使用根帐户和 Windows，作为本地管理员组的成员。

在开始之前，请务必查看[先决条件](overview.md#prerequisites)，并验证你的订阅和资源是否满足要求。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

在此过程结束时，你将成功地将混合计算机连接到 Azure Arc for server。

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>创建服务主体以进行大规模载入

你可以使用[Azure PowerShell](/powershell/azure/install-az-ps)使用[AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) cmdlet 来创建服务主体。 或者，你可以按照[使用 Azure 门户创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)下列出的步骤来完成此任务。

> [!NOTE]
> 在创建服务主体时，您的帐户必须是要用于载入的订阅中的 "所有者" 或 "用户访问管理员"。 如果你没有足够的权限创建角色分配，则可能会创建服务主体，但它将无法加入计算机。
>

若要使用 PowerShell 创建服务主体，请执行以下各项。

1. 运行以下命令。 必须将[`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) cmdlet 的输出存储在一个变量中，否则将无法检索后续步骤中所需的密码。

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

3. 在输出中，找到 "密码" 和 "**密码**" 下面的 "密码" 值。 另外，还可以在字段 " **ApplicationId** " 下找到值并复制它。 稍后将其保存在安全的位置。 如果忘记或丢失了服务主体密码，可以使用[`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) cmdlet 重置。

以下属性中的值用于传递到 `azcmagent`的参数：

* **ApplicationId**属性中的值用于 `--service-principal-id` 参数值
* **Password**属性中的值用于用于连接代理的 `--service-principal-secret` 参数。

> [!NOTE]
> 请确保使用服务主体**ApplicationId**属性，而不是**Id**属性。
>

**Azure 连接的计算机加入**角色只包含载入计算机所需的权限。 您可以分配服务主体权限以允许其作用域包含资源组或订阅。 若要添加角色分配，请参阅[使用 AZURE rbac 和 Azure 门户添加或删除角色分配](../../role-based-access-control/role-assignments-portal.md)，或[使用 azure Rbac 和 Azure CLI 添加或删除角色分配](../../role-based-access-control/role-assignments-cli.md)。

## <a name="install-the-agent-and-connect-to-azure"></a>安装代理并连接到 Azure

以下步骤通过使用脚本模板在混合计算机上安装和配置已连接的计算机代理，该模板执行 Azure 门户文章将[混合计算机连接到 Azure](onboard-portal.md) "一文中所述的类似步骤。 不同之处在于，最后一步是使用使用服务主体的 `azcmagent` 命令建立与 Azure Arc 的连接。 

下面是配置用于服务主体 `azcmagent` 命令的设置。

* `tenant-id`：代表 Azure AD 专用实例的唯一标识符（GUID）。
* `subscription-id`：你要将计算机置于其中的 Azure 订阅的订阅 ID （GUID）。
* `resource-group`：希望连接的计算机所属的资源组名称。
* `location`：请参阅[支持的 Azure 区域](overview.md#supported-regions)。 此位置可以与资源组的位置相同或不同。
* `resource-name`：（*可选*）用于本地计算机的 Azure 资源表示形式。 如果未指定此值，则使用计算机主机名。

若要详细了解 `azcmagent` 命令行工具，请查看[Azcmagent 引用](azcmagent-reference.md)。

### <a name="windows-installation-script"></a>Windows 安装脚本

下面是已被修改为使用服务主体来支持完全自动、非交互式的代理安装的已连接计算机代理的示例。

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

### <a name="linux-installation-script"></a>Linux 安装脚本

下面是适用于 Linux 的已连接计算机代理安装脚本的示例，已修改为使用服务主体来支持完全自动、非交互式的代理安装。

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

安装代理并将其配置为连接到用于服务器（预览版）的 Azure Arc 后，请切换到 Azure 门户验证是否已成功连接服务器。 在[Azure 门户](https://aka.ms/hybridmachineportal)中查看计算机。

![成功的服务器连接](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>后续步骤

- 了解如何使用[Azure 策略](../../governance/policy/overview.md)管理计算机，例如 VM[来宾配置](../../governance/policy/concepts/guest-configuration.md)，验证计算机是否向预期的 Log Analytics 工作区进行报告，使用[vm Azure Monitor](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)启用监视等操作。

- 详细了解[Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)。 如果希望主动监视计算机上运行的操作系统和工作负荷，请使用自动化 runbook 或解决方案（如更新管理）或使用其他 Azure 服务（如[Azure 安全中心](../../security-center/security-center-intro.md)）来管理该计算机，则需要使用适用于 Windows 和 Linux 的 Log Analytics 代理。
