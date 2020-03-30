---
title: 大规模将混合计算机连接到 Azure
description: 在本文中，您将了解如何使用 Azure Arc 将计算机连接到 Azure 的服务器（预览版）。使用服务主体。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192267"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>大规模将混合计算机连接到 Azure

您可以为环境中的多个 Windows 或 Linux 计算机启用 Azure Arc 服务器（预览），并根据您的要求提供多个灵活的选项。 使用我们提供的模板脚本，您可以自动执行安装的每一步，包括建立与 Azure Arc 的连接。但是，您需要使用在目标计算机和 Azure 中具有提升权限的帐户以交互方式执行此脚本。 要将计算机连接到服务器的 Azure Arc，可以使用 Azure 活动目录[服务主体](../../active-directory/develop/app-objects-and-service-principals.md)，而不是使用特权标识[以交互连接计算机](onboard-portal.md)。 服务主体是一种特殊的有限管理标识，仅授予使用`azcmagent`命令将计算机连接到 Azure 所需的最低权限。 这比使用租户管理员等更高特权帐户更安全，并且遵循我们的访问控制安全最佳实践。 服务主体仅在载入期间使用，不用于任何其他目的。  

安装和配置连接计算机代理的安装方法要求您使用的自动方法对计算机具有管理员权限。 在 Linux 上，通过使用根帐户和 Windows 作为本地管理员组的成员。

在开始之前，请务必查看[先决条件](overview.md#prerequisites)，并验证你的订阅和资源是否符合要求。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

此过程结束时，您将成功地将混合计算机连接到用于服务器的 Azure Arc。

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>创建大规模载入的服务主体

您可以使用[Azure PowerShell](/powershell/azure/install-az-ps)使用[New-AzADService 主要](/powershell/module/Az.Resources/New-AzADServicePrincipal)cmdlet 创建服务主体。 或者，您可以[按照使用 Azure 门户创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)下列出的步骤来完成此任务。

> [!NOTE]
> 创建服务主体时，您的帐户必须是要用于载入的订阅中的所有者或用户访问管理员。 如果你没有足够的权限创建角色分配，则可能会创建服务主体，但它将无法加入计算机。
>

要使用 PowerShell 创建服务主体，请执行以下操作。

1. 运行以下命令。 您必须将[`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal)cmdlet 的输出存储在变量中，否则将无法检索后续步骤中所需的密码。

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

2. 要检索存储在变量中的`$sp`密码，请运行以下命令：

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. 在输出中，查找字段**密码**下的密码值并复制它。 还会在"**应用程序 Id"** 字段下查找该值，并复制它。 将它们保存在安全的地方，以便以后使用。 如果您忘记或丢失了服务主体密码，则可以使用[`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential)cmdlet 重置它。

以下属性中的值与传递给 的`azcmagent`参数一起使用：

* **来自应用程序 Id**属性的值用于`--service-principal-id`参数值
* **密码**属性中的值用于用于连接代理的`--service-principal-secret`参数。

> [!NOTE]
> 请确保使用服务主体**应用程序 Id**属性，而不是**Id**属性。
>

**Azure 连接的计算机载入**角色仅包含载入计算机所需的权限。 您可以分配服务主体权限，以允许其范围包括资源组或订阅。 要添加角色分配，请参阅[使用 Azure RBAC 和 Azure 门户添加或删除角色分配](../../role-based-access-control/role-assignments-portal.md)，或使用[Azure RBAC 和 Azure CLI 添加或删除角色分配](../../role-based-access-control/role-assignments-cli.md)。

## <a name="install-the-agent-and-connect-to-azure"></a>安装代理并连接到 Azure

以下步骤通过使用脚本模板在混合计算机上安装和配置已连接的计算机代理，该模板执行 Azure 门户文章中在["将混合计算机连接到 Azure"](onboard-portal.md)中描述的类似步骤。 区别在于使用服务主体的命令建立与 Azure Arc`azcmagent`的连接的最后一步。 

以下是将`azcmagent`命令配置为用于服务主体的设置。

* `tenant-id`：表示 Azure AD 专用实例的唯一标识符 （GUID）。
* `subscription-id`：要在 中的计算机的 Azure 订阅的订阅 ID （GUID）。
* `resource-group`：您希望连接的计算机所属的资源组名称。
* `location`：请参阅[支持的 Azure 区域](overview.md#supported-regions)。 此位置可以与资源组的位置相同或不同。
* `resource-name`： （*可选*） 用于本地计算机的 Azure 资源表示形式。 如果不指定此值，则使用计算机主机名。

您可以通过查看`azcmagent`[Azcmagent 参考](azcmagent-reference.md)来了解有关命令行工具的更多信息。

### <a name="windows-installation-script"></a>窗口安装脚本

下面是 Windows 安装脚本的已连接计算机代理的示例，该代理已被修改为使用服务主体来支持代理的完全自动化非交互式安装。

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

下面是 Linux 安装脚本的已连接计算机代理的示例，该代理已被修改为使用服务主体来支持代理的完全自动化、非交互式安装。

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

安装代理并将其配置为连接到 Azure Arc for servers（预览版）后，请转到 Azure 门户，以验证是否已成功连接服务器。 在 [Azure 门户](https://aka.ms/hybridmachineportal)中查看计算机。

![服务器连接成功](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>后续步骤

- 了解如何使用[Azure 策略](../../governance/policy/overview.md)管理计算机，用于 VM[来宾配置](../../governance/policy/concepts/guest-configuration.md)、验证计算机是否报告到预期的日志分析工作区、使用 VM 启用 Azure[监视器](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)等操作。

- 了解有关[日志分析代理](../../azure-monitor/platform/log-analytics-agent.md)的更多信息。 若要主动监视计算机上运行的 OS 和工作负荷、使用自动化 Runbook 或更新管理等解决方案对其进行管理，或使用其他 Azure 服务（例如 [Azure 安全中心](../../security-center/security-center-intro.md)），需要安装适用于 Windows 和 Linux 的 Log Analytics 代理。
