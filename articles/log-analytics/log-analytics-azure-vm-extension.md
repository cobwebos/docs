---
title: "将 Azure 虚拟机连接到 Log Analytics | Microsoft Docs"
description: "对于在 Azure 中运行的 Windows 和 Linux 虚拟机，建议使用的收集日志和指标的方法是安装 Log Analytics Azure VM 扩展。 可以使用 Azure 门户或 PowerShell 将 Log Analytics 虚拟机扩展安装到 Azure VM 上。"
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: ca39e586-a6af-42fe-862e-80978a58d9b1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: richrund
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 5bb3a67c999c1d41c50b2b660a97a53125511633
ms.lasthandoff: 03/25/2017


---
# <a name="connect-azure-virtual-machines-to-log-analytics-with-a-log-analytics-agent"></a>使用 Log Analytics 代理将 Azure 虚拟机连接到 Log Analytics

对于 Windows 和 Linux 计算机，建议使用的收集日志和指标的方法是安装 Log Analytics 代理。

在 Azure 虚拟机上安装 Log Analytics 代理的最简单的方法是通过 Log Analytics VM 扩展。  使用扩展可简化安装流程，并可自动配置代理，以将数据发送至指定的 Log Analytics 工作区。 代理还会自动升级，以确保拥有最新的功能和修补程序。

对于 Windows 虚拟机，要启用 *Microsoft Monitoring Agent* 虚拟机扩展。
对于 Linux 虚拟机，要启用 *OMS Agent For Linux* 虚拟机扩展。

详细了解 [Azure 虚拟机扩展](../virtual-machines/virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)和 [Linux 代理](../virtual-machines/virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

使用基于代理的日志数据集合时，必须配置 [Log Analytics 中的数据源](log-analytics-data-sources.md) 指定要收集的日志和指标。

> [!IMPORTANT]
> 如果将 Log Analytics 配置为使用“[Azure 诊断](log-analytics-azure-storage.md)”索引日志数据并配置代理收集相同的日志，则日志被收集了两次。 会向你收取两种数据源的费用。 如果已安装代理，则应只使用代理收集日志数据 - 不要将 Log Analytics 配置为通过 Azure 诊断收集日志数据。
>
>

有三种简单简单方法方法可启用 Log Analytics 虚拟机扩展：

* 使用 Azure 门户
* 使用 Azure PowerShell
* 使用 Azure Resource Manager 模板

## <a name="enable-the-vm-extension-in-the-azure-portal"></a>在 Azure 门户中启用 VM 扩展
可以使用 [Azure 门户](https://portal.azure.com) 安装 Log Analytics 的代理并连接运行代理的 Azure 虚拟机。

### <a name="to-install-the-log-analytics-agent-and-connect-the-virtual-machine-to-a-log-analytics-workspace"></a>安装 Log Analytics 代理并将虚拟机连接到 Log Analytics 工作区
1. 登录到 [Azure 门户](http://portal.azure.com)。
2. 在门户左侧选择“**浏览**”，然后转到 **Log Analytics (OMS)** 并选中。
3. 在 Log Analytics 工作区列表中，选择要和 Azure VM 一起使用的工作区。  
   ![OMS 工作区](./media/log-analytics-azure-vm-extension/oms-connect-azure-01.png)
4. 在“**Log Analytics 管理**”下，选择“**虚拟机**”。  
   ![虚拟机](./media/log-analytics-azure-vm-extension/oms-connect-azure-02.png)
5. 在“**虚拟机**”列表中，选择想要在其中安装代理的虚拟机。 VM 的“**OMS 连接状态**”指示其“**未连接**”。  
   ![VM 未连接](./media/log-analytics-azure-vm-extension/oms-connect-azure-03.png)
6. 在虚拟机的详细信息中，选择“**连接**”。 则自动会为 Log Analytics 工作区安装并配置代理。 此过程需要几分钟的时间，在此期间，“OMS 连接”状态为“*正在连接...*”  
   ![连接 VM](./media/log-analytics-azure-vm-extension/oms-connect-azure-04.png)
7. 安装并连接代理后，将会更新“**OMS 连接**”状态以显示“**此工作区**”。  
   ![已连接](./media/log-analytics-azure-vm-extension/oms-connect-azure-05.png)

## <a name="enable-the-vm-extension-using-powershell"></a>使用 PowerShell 启用 VM 扩展
使用 PowerShell 配置虚拟机时，需要提供 **workspaceId** 和 **workspaceKey**。 请注意，json 配置中的属性名称**区分大小写**。

可以在 OMS 门户的“**设置**”页面，或按照上述示例所示使用 PowerShell 来查看 ID 和关键值。

![工作区 ID 和主键](./media/log-analytics-azure-vm-extension/oms-analyze-azure-sources.png)

存在各种用于 Azure 经典虚拟机和资源管理器虚拟机的命令。 以下是用于经典和资源管理器虚拟机的示例。

对于经典虚拟机，使用以下 PowerShell 示例：

```
Add-AzureAccount

$workspaceId = "enter workspace ID here"
$workspaceKey = "enter workspace key here"
$hostedService = "enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService

# For Windows VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

# For Linux VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'OmsAgentForLinux' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

对于资源管理器虚拟机，使用以下 PowerShell 示例：

```
Login-AzureRMAccount
Select-AzureSubscription -SubscriptionId "**"

$workspaceName = "your workspace name"
$VMresourcegroup = "**"
$VMresourcename = "**"

$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq $workspaceName})

if ($workspace.Name -ne $workspaceName)
{
    Write-Error "Unable to find OMS Workspace $workspaceName. Do you need to run Select-AzureRMSubscription?"
}

$workspaceId = $workspace.CustomerId
$workspaceKey = (Get-AzureRmOperationalInsightsWorkspaceSharedKeys -ResourceGroupName $workspace.ResourceGroupName -Name $workspace.Name).PrimarySharedKey

$vm = Get-AzureRmVM -ResourceGroupName $VMresourcegroup -Name $VMresourcename
$location = $vm.Location

# For Windows VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"

# For Linux VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'OmsAgentForLinux' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'OmsAgentForLinux' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"


```

## <a name="deploy-the-vm-extension-using-a-template"></a>使用模板部署 VM 扩展
使用 Azure 资源管理器可以创建一个简单的模板（采用 JSON 格式），用于定义应用程序的部署和配置。 此模板称为资源管理器模板，让你以声明性方式定义部署。 使用模板可以在整个应用程序生命周期内反复部署该应用程序，并确保以一致的状态部署资源。

通过将 Log Analytics 代理作为资源管理器模板的一部分，可以确保将每个虚拟机预配置为向 Log Analytics 工作区报告。

有关资源管理器模板的详细信息，请参阅 [创作 Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)。

以下是用于部署已安装 Microsoft Monitoring Agent 扩展并运行 Windows 的虚拟机的资源管理器模板示例。 该模板是典型的虚拟机模板，具有以下添加件：

* workspaceId 和 workspaceName 参数
* Microsoft.EnterpriseCloud.Monitoring 资源扩展部分
* 查找 workspaceId 和 workspaceSharedKey 的输出

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "dnsLabelPrefix": {
       "type": "string",
       "metadata": {
          "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
       }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "OMS workspace ID"
      }
    },
    "workspaceName": {
      "type": "string",
      "metadata": {
         "description": "OMS workspace name"
      }
    },
    "windowsOSVersion": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "Windows-Server-Technical-Preview"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]",
    "apiVersion": "2015-06-15",
    "imagePublisher": "MicrosoftWindowsServer",
    "imageOffer": "WindowsServer",
    "OSDiskName": "osdiskforwindowssimple",
    "nicName": "myVMNic",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "storageAccountType": "Standard_LRS",
    "publicIPAddressName": "myPublicIP",
    "publicIPAddressType": "Dynamic",
    "vmStorageAccountContainerName": "vhds",
    "vmName": "MyWindowsVM",
    "vmSize": "Standard_DS1",
    "virtualNetworkName": "MyVNET",
    "resourceId": "[resourceGroup().id]",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsLabelPrefix')]"
        }
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[variables('vmSize')]"
        },
        "osProfile": {
          "computername": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "[variables('imagePublisher')]",
            "offer": "[variables('imageOffer')]",
            "sku": "[parameters('windowsOSVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
             "enabled": "true",
             "storageUri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net')]"
          }
        }
      },
      "resources": [
        {
          "type": "extensions",
          "name": "Microsoft.EnterpriseCloud.Monitoring",
          "apiVersion": "[variables('apiVersion')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
          ],
          "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "workspaceId": "[parameters('workspaceId')]"
            },
            "protectedSettings": {
              "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-03-20').primarySharedKey]"
            }
          }
        }
      ]
    }
  ],
  "outputs": {
      "sharedKeyOutput": {
         "value": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').primarySharedKey]",
         "type": "string"
      },
      "workspaceIdOutput": {
         "value": "[reference(concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').customerId]",
        "type" : "string"
      }
  }
}
```

可以使用以下 PowerShell 命令部署模板：

```
New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath
```

## <a name="troubleshooting-the-log-analytics-vm-extension"></a>Log Analytics VM 扩展故障排除
通常，当功能不正常工作时，将从 Azure 门户或 Azure powershell 收到一条消息。

1. 登录到 [Azure 门户](http://portal.azure.com)。
2. 找到 VM 并打开 VM 详细信息。
3. 单击“扩展”以查看 OMS 扩展是否已启用。

   ![VM 扩展视图](./media/log-analytics-azure-vm-extension/oms-vmview-extensions.png)

4. 单击 *MicrosoftMonitoringAgent*(Windows) 或 *OmsAgentForLinux*(Linux) 扩展，查看详细信息。 

   ![VM 扩展详细信息](./media/log-analytics-azure-vm-extension/oms-vmview-extensiondetails.png)

### <a name="troubleshooting-windows-virtual-machines"></a>Windows 虚拟机故障排除
如果 *Microsoft Monitoring Agent* VM 代理扩展未安装或未报告，可以执行以下步骤来解决此问题。

1. 使用 [KB 2965986](https://support.microsoft.com/kb/2965986#mt1) 中的步骤检查是否已安装 Azure VM 代理或者其是否正常工作。
   * 还可以查看 VM 代理日志文件 `C:\WindowsAzure\logs\WaAppAgent.log`
   * 如果此日志不存在，则未安装 VM 代理。
     * [在经典 VM 上安装 Azure VM 代理](../virtual-machines/windows/classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
2. 使用以下步骤确认 Microsoft Monitoring Agent 扩展检测信号任务正在运行：
   * 登录到虚拟机
   * 打开任务计划程序并找到 `update_azureoperationalinsight_agent_heartbeat` 任务
   * 确认任务已启用并且一直在运行
   * 查看 `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log` 中的检测信号日志文件
3. 查看 `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent` 中的 Microsoft Monitoring Agent VM 扩展日志文件
4. 确保虚拟机可以运行 PowerShell 脚本
5. 确保 C:\Windows\temp 上的权限未被更改
6. 通过在虚拟机上的 PowerShell 特权窗口中键入以下内容来查看 Microsoft Monitoring Agent 的状态：`  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. 查看 `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs` 中的 Microsoft Monitoring Agent 安装日志文件

有关详细信息，请参阅 [Windows 扩展故障排除](../virtual-machines/virtual-machines-windows-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

### <a name="troubleshooting-linux-virtual-machines"></a>Linux 虚拟机故障排除
如果 *OMS Agent for Linux* VM 代理扩展未安装或未报告，可以执行以下步骤来解决此问题。

1. 如果扩展状态为“*未知*”，则通过查看 VM 代理日志文件 `/var/log/waagent.log` 来检查是否已安装 Azure VM 代理且正常工作
   * 如果此日志不存在，则未安装 VM 代理。
   * [在 Linux VM 上安装 Azure VM 代理](../virtual-machines/virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
2. 对于其他不正常状态，请查看 `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` 和 `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log` 中的 OMS Agent for Linux VM 扩展日志文件
3. 如果扩展状态正常，但是未上载数据，则查看 `/var/opt/microsoft/omsagent/log/omsagent.log` 中的 OMS Agent for Linux 日志文件

有关详细信息，请参阅 [Linux 扩展故障排除](../virtual-machines/virtual-machines-linux-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="next-steps"></a>后续步骤
* 配置 [Log Analytics 中的数据源](log-analytics-data-sources.md) 指定要收集的日志和指标。
* [从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md) 以从虚拟机中收集数据。
* 对于 Azure 中运行的其他资源，[使用 Azure 诊断收集数据](log-analytics-azure-storage.md)。

对于未处于 Azure 中的计算机，可以使用下文中描述的方法安装 Log Analytics 代理：

* [将 Windows 计算机连接到 Log Analytics](log-analytics-windows-agents.md)
* [将 Linux 计算机连接到 Log Analytics](log-analytics-linux-agents.md)

