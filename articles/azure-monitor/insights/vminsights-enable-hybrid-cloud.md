---
title: 为混合环境启用 Azure Monitor （预览版） |Microsoft Docs
description: 本文介绍如何为包含一个或多个虚拟机的混合云环境启用用于 VM 的 Azure Monitor。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: bd44eebf8aceaf7fe32cf8cf1b1152db32acb344
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361821"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>启用混合环境的用于 VM 的 Azure Monitor （预览版）

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本文介绍如何为你的数据中心或其他云环境中托管的虚拟机或物理计算机启用用于 VM 的 Azure Monitor （预览版）。 在此过程结束时，你将成功地开始监视环境中的虚拟机，并了解它们是否遇到任何性能或可用性问题。

在开始之前，请务必查看[先决条件](vminsights-enable-overview.md)，并验证你的订阅和资源是否符合要求。 查看 [Log Analytics Linux 和 Windows 代理](../../log-analytics/log-analytics-agent-overview.md)的要求与部署方法。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>用于 VM 的 Azure Monitor 映射依赖项代理本身不传输任何数据，它不需要对防火墙或端口做出任何更改。 如果 IT 安全策略不允许网络上的计算机连接到 internet，则映射数据始终由 Log Analytics 代理直接或通过[Operations Management Suite 网关](../../azure-monitor/platform/gateway.md)传输到 Azure Monitor 服务。

下面总结了完成此任务的步骤：

1. 安装适用于 Windows 或 Linux 的 Log Analytics 代理。 在安装代理之前，请查看[Log Analytics 代理概述](../platform/log-analytics-agent.md)一文以了解系统先决条件和部署方法。

2. 下载并安装适用于 [Windows](https://aka.ms/dependencyagentwindows) 或 [Linux](https://aka.ms/dependencyagentlinux) 的用于 VM 的 Azure Monitor 映射依赖项代理。

3. 启用性能计数器收集。

4. 部署用于 VM 的 Azure Monitor。

>[!NOTE]
>本文中介绍的用于部署依赖关系代理的信息也适用于[服务映射解决方案](service-map.md)。  

## <a name="install-the-dependency-agent-on-windows"></a>在 Windows 上安装依赖项代理

可通过运行 `InstallDependencyAgent-Windows.exe` 在 Windows 计算机上手动安装 Dependency Agent。 如果在没有任何选项的情况下运行此可执行文件，它将启动一个安装向导，以交互方式指导用户安装代理。

>[!NOTE]
>需要*管理员*特权才能安装或卸载代理。

下表突出显示了通过命令行安装代理时支持的参数。

| 参数 | 说明 |
|:--|:--|
| /? | 返回命令行选项的列表。 |
| /S | 执行无需用户交互的无提示安装。 |

例如，若要用 `/?` 参数运行安装程序，请输入 " **installdependencyagent-windows.exe/？"** 。

默认情况下，Windows Dependency Agent 的文件在 *C:\Program Files\Microsoft Dependency Agent* 中安装。 如果在安装完成后依赖关系代理无法启动，请检查日志以获取详细的错误信息。 日志目录为 *%Programfiles%\Microsoft Dependency Agent\logs*。

## <a name="install-the-dependency-agent-on-linux"></a>在 Linux 上安装 Dependency Agent

通过 *InstallDependencyAgent-Linux64.bin*（具有自解压二进制文件的 Shell 脚本）在 Linux 服务器上安装 Dependency Agent。 可使用 `sh` 来运行该文件或将执行权限添加到文件本身。

>[!NOTE]
> 需要根目录访问才能安装或配置代理。
>

| 参数 | 说明 |
|:--|:--|
| -help | 获取命令行选项列表。 |
| -S | 执行无提示安装，无用户提示。 |
| --check | 检查权限和操作系统，但不安装代理。 |

例如，若要用 `-help` 参数运行安装程序，请输入**installdependencyagent-linux64.bin-help**。

通过运行命令 `sh InstallDependencyAgent-Linux64.bin`，将 Linux 依赖关系代理安装为根。

如果 Dependency Agent 无法启动，请检查日志以获取详细的错误信息。 在 Linux 代理上，日志目录是 */var/opt/microsoft/dependency-agent/log*。

Dependency Agent 的文件放置在以下目录中：

| 文件 | 位置 |
|:--|:--|
| 核心文件 | /opt/microsoft/dependency-agent |
| 日志文件 | /var/opt/microsoft/dependency-agent/log |
| 配置文件 | /etc/opt/microsoft/dependency-agent/config |
| 服务可执行文件 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 二进制存储文件 | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>安装脚本示例

要在多台服务器上同时轻松部署 Dependency Agent，可以使用以下脚本示例在 Windows 或 Linux 上下载和安装 Dependency Agent。

### <a name="powershell-script-for-windows"></a>适用于 Windows 的 PowerShell 脚本

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>适用于 Linux 的 Shell 脚本

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Desired State Configuration

若通过期望状态配置 (DSC) 部署 Dependency Agent，可通过如下示例代码使用 xPSDesiredStateConfiguration 模块：

```powershell
configuration ServiceMap {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="enable-performance-counters"></a>启用性能计数器

如果解决方案引用的 Log Analytics 工作区尚未配置为收集解决方案所需的性能计数器，则需要启用性能计数器。 可以通过以下两种方式之一执行此操作：
* 手动方式，如 [Log Analytics 中的 Windows 和 Linux 性能数据源](../../azure-monitor/platform/data-sources-performance-counters.md)所述
* 下载并运行[Azure PowerShell 库](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)中提供的 PowerShell 脚本

## <a name="deploy-azure-monitor-for-vms"></a>部署用于 VM 的 Azure Monitor

此方法包含一个 JSON 模板，其中指定了用于在 Log Analytics 工作区中启用解决方案组件的配置。

如果不知道如何使用模板部署资源，请参阅：
* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../../azure-resource-manager/templates/deploy-powershell.md)
* [使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/templates/deploy-cli.md)

若要使用 Azure CLI，首先需要在本地安装并使用 CLI。 必须运行 Azure CLI 2.0.27 版或更高版本。 若要确定版本，请运行 `az --version`。 若要安装或升级 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="create-and-execute-a-template"></a>创建和执行模板

1. 将以下 JSON 语法复制并粘贴到文件中：

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. 使用 *installsolutionsforvminsights.json* 文件名将此文件保存到某个本地文件夹中。

1. 捕获 *WorkspaceName*、*ResourceGroupName* 和 *WorkspaceLocation* 的值。 *WorkspaceName* 的值为 Log Analytics 工作区的名称。 *WorkspaceLocation* 的值是在其中定义工作区的区域。

1. 现在，可以使用以下 PowerShell 命令部署此模板：

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    配置更改可能需要几分钟才能完成。 完成后，将显示一条类似于以下内容的消息，其中包括结果：

    ```powershell
    provisioningState       : Succeeded
    ```
   启用监视后，可能需要约 10 分钟才能查看混合计算机的运行状况和指标。

## <a name="troubleshooting"></a>故障排除

### <a name="vm-doesnt-appear-on-the-map"></a>VM 未出现在地图上

如果依赖关系代理安装已成功，但未在地图上看到计算机，请按照以下步骤来诊断问题。

1. Dependency Agent 是否已安装成功？ 可通过检查是否已安装并运行服务来验证这一点。

    **Windows**：查找名为“Microsoft Dependency Agent”的服务。

    **Linux**：查找正在运行的进程“microsoft-dependency-agent”

2. 你是否处于[Log Analytics 的免费定价层](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)？ 免费计划最多允许五台唯一的计算机。 即使前面的五个计算机不再发送数据，任何后续计算机也不会显示在映射上。

3. 计算机是否将日志和性能数据发送到 Azure Monitor 日志？ 对计算机执行以下查询：

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    它是否返回了一个或多个结果？ 是否为最新数据？ 如果是这样，则 Log Analytics 代理正在正常运行并与服务进行通信。 如果不是，请在服务器上检查代理：[用于 Windows 的 Log Analytics 代理故障排除](../platform/agent-windows-troubleshoot.md)或[用于 Linux 的 Log Analytics 代理故障排除](../platform/agent-linux-troubleshoot.md)。

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>计算机出现在地图上，但没有任何进程

如果你在地图上看到你的服务器，但它没有进程或连接数据，则表明依赖关系代理已安装并正在运行，但内核驱动程序未加载。

请检查 C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file（针对 Windows）或 /var/opt/microsoft/dependency-agent/log/service.log file（针对 Linux）。 文件的最后几行应指出为何未加载内核。 例如，如果更新内核，则内核在 Linux 上可能不受支持。


## <a name="next-steps"></a>后续步骤

为虚拟机启用监视后，可以使用用于 VM 的 Azure Monitor 分析此信息。

- 若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。

- 若要确定虚拟机的性能瓶颈和总体利用率，请参阅[查看 AZURE vm 性能](vminsights-performance.md)。
