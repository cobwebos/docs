---
title: 为混合环境中启用 Azure Monitor （预览版） |Microsoft Docs
description: 本文介绍如何在 Azure Monitor 为 Vm 启用为混合云环境，其中包含一个或多个虚拟机。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/07/2019
ms.author: magoedte
ms.openlocfilehash: bc26cc0654aac9416bf31ffccf426648e3a8b8d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122540"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>为 Vm （预览版） 的 Azure Monitor 启用为混合环境

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本文介绍如何为 Vm （预览版） 中启用 Azure Monitor 为虚拟机或托管在你的数据中心或其他云环境中的物理计算机。 在此过程结束时，你将具有已成功开始监视你的环境中的虚拟机，并了解他们遇到的任何性能或可用性问题。 

在开始之前，请确保查看[先决条件](vminsights-enable-overview.md)和验证你的订阅和资源符合的要求。 查看 [Log Analytics Linux 和 Windows 代理](../../log-analytics/log-analytics-agent-overview.md)的要求与部署方法。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>用于 VM 的 Azure Monitor 映射依赖项代理本身不传输任何数据，它不需要对防火墙或端口做出任何更改。 映射数据始终由 Log Analytics 代理到 Azure Monitor 服务，直接或通过传输[Operations Management Suite 网关](../../azure-monitor/platform/gateway.md)如果 IT 安全策略不允许为网络上的计算机连接到 internet。

若要完成此任务的步骤总结如下：

1. 安装适用于 Windows 或 Linux 的 Log Analytics 代理。 安装代理之前，请查看[Log Analytics 代理概述](../platform/log-analytics-agent.md)一文以了解系统先决条件和部署方法。

2. 下载并安装适用于 [Windows](https://aka.ms/dependencyagentwindows) 或 [Linux](https://aka.ms/dependencyagentlinux) 的用于 VM 的 Azure Monitor 映射依赖项代理。

3. 启用性能计数器收集。

4. 部署用于 VM 的 Azure Monitor。

## <a name="install-the-dependency-agent-on-windows"></a>在 Windows 上安装依赖项代理
可通过运行 `InstallDependencyAgent-Windows.exe` 在 Windows 计算机上手动安装 Dependency Agent。 如果在没有任何选项的情况下运行此可执行文件，它将启动一个安装向导，以交互方式指导用户安装代理。

>[!NOTE]
>需要*管理员*特权才能安装或卸载代理。

下表突出显示了通过命令行安装代理时支持的参数。

| 参数 | 描述 |
|:--|:--|
| /? | 返回命令行选项的列表。 |
| /S | 执行无需用户交互的无提示安装。 |

例如，若要运行安装程序，并`/?`参数，输入**InstallDependencyAgent Windows.exe /？** 。

默认情况下，Windows Dependency Agent 的文件在 *C:\Program Files\Microsoft Dependency Agent* 中安装。 如果依赖关系代理无法启动安装程序结束后，，检查日志了解详细的错误的信息。 日志目录为 *%Programfiles%\Microsoft Dependency Agent\logs*。

## <a name="install-the-dependency-agent-on-linux"></a>在 Linux 上安装 Dependency Agent
通过 *InstallDependencyAgent-Linux64.bin*（具有自解压二进制文件的 Shell 脚本）在 Linux 服务器上安装 Dependency Agent。 可使用 `sh` 来运行该文件或将执行权限添加到文件本身。

>[!NOTE]
> 需要根目录访问才能安装或配置代理。
>

| 参数 | 描述 |
|:--|:--|
| -help | 获取命令行选项列表。 |
| -s | 执行无提示安装，无用户提示。 |
| --check | 检查权限和操作系统，但不安装代理。 |

例如，若要运行安装程序，并`-help`参数，输入**InstallDependencyAgent-Linux64.bin-帮助**。

通过运行该命令将 Linux 依赖关系代理安装为根`sh InstallDependencyAgent-Linux64.bin`。

如果 Dependency Agent 无法启动，请检查日志以获取详细的错误信息。 在 Linux 代理上，日志目录是 */var/opt/microsoft/dependency-agent/log*。

Dependency Agent 的文件放置在以下目录中：

| 文件 | 位置 |
|:--|:--|
| 核心文件 | /opt/microsoft/dependency-agent |
| 日志文件 | /var/opt/microsoft/dependency-agent/log |
| 配置文件 | /etc/opt/microsoft/dependency-agent/config |
| 服务可执行文件 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 二进制存储文件 | /var/opt/microsoft/dependency-agent/storage |

## <a name="enable-performance-counters"></a>启用性能计数器
如果解决方案引用的 Log Analytics 工作区尚未配置为收集解决方案所需的性能计数器，则需要启用性能计数器。 您可以执行操作在两种方式之一：
* 手动方式，如 [Log Analytics 中的 Windows 和 Linux 性能数据源](../../azure-monitor/platform/data-sources-performance-counters.md)所述
* 通过下载并运行 PowerShell 脚本，可从[Azure PowerShell 库](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>部署用于 VM 的 Azure Monitor
此方法包含一个 JSON 模板，其中指定了用于在 Log Analytics 工作区中启用解决方案组件的配置。

如果不知道如何使用模板部署资源，请参阅：
* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../../azure-resource-manager/resource-group-template-deploy.md)
* [使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/resource-group-template-deploy-cli.md)

若要使用 Azure CLI，首先需要本地安装并使用 CLI。 必须运行 Azure CLI 2.0.27 版或更高版本。 若要确定版本，请运行 `az --version`。 若要安装或升级 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

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
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
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

    配置更改可能需要几分钟才能完成。 完成后，则显示一条消息，类似于以下和包括的结果：

    ```powershell
    provisioningState       : Succeeded
    ```
   启用监视后，可能需要约 10 分钟才能查看混合计算机的运行状况和指标。

## <a name="next-steps"></a>后续步骤

现在，你的虚拟机启用监视，此信息是可用于分析的 Vm 的 Azure monitor。
 
- 若要了解如何使用运行状况的功能，请参阅[查看 Azure Vm 运行状况的监视器](vminsights-health.md)。
- 若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。
- 若要标识瓶颈和 VM 的性能的整体利用率，请参阅[查看 Azure VM 性能](vminsights-performance.md)。
- 若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。