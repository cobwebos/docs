---
title: 使用模板在 Azure Monitor 中收集 Windows 规模集指标
description: 使用 Windows 虚拟机规模集的资源管理器模板将来宾 OS 指标发送到 Azure Monitor 指标存储
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 9a7aa512c636f700cf9c6d990814d9367007c942
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125768"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>使用 Windows 虚拟机规模集的 Azure 资源管理器模板将来宾 OS 指标发送到 Azure Monitor 指标存储

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

使用 Azure Monitor [Windows Azure 诊断 (WAD) 扩展](diagnostics-extension-overview.md)，可以从作为虚拟机、云服务或 Azure Service Fabric 群集的一部分运行的来宾操作系统（来宾 OS）收集指标和日志。 该扩展可将遥测数据发送到之前链接的文章中列出的许多不同位置。  

本文介绍将 Windows 虚拟机规模集的来宾 OS 性能指标发送到 Azure Monitor 数据存储的过程。 自 Windows Azure 诊断版本 1.11 版起，可将指标直接写入已收集标准平台指标的 Azure Monitor 指标存储。 将它们存储在此位置可以访问可用于平台指标的相同操作。 操作包括近实时警报、图表绘制、路由、从 REST API 访问，等等。 在过去，Windows Azure 诊断扩展将数据写入 Azure 存储而不是 Azure Monitor 数据存储。  

如果你不熟悉资源管理器模板，请了解[模板部署](../../azure-resource-manager/management/overview.md)及其结构和语法。  

## <a name="prerequisites"></a>必备条件

- 你的订阅必须已注册到 [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services)。 

- 需要安装 [Azure PowerShell](/powershell/azure)，但也可以使用 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 

- VM 资源必须位于[支持自定义指标的区域](metrics-custom-overview.md#supported-regions)中。

## <a name="set-up-azure-monitor-as-a-data-sink"></a>将 Azure Monitor 设置为数据接收器 
Azure 诊断扩展使用名为“数据接收器”的功能将指标和日志路由到不同位置。  以下步骤说明如何通过资源管理器模板和 PowerShell 来使用新的 Azure Monitor 数据接收器部署 VM。 

## <a name="author-a-resource-manager-template"></a>创作资源管理器模板 
对于本示例，可以使用公开发布的[示例模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale)：  

- **Azuredeploy.json** 是用于部署虚拟机规模集的预配置资源管理器模板。

- **Azuredeploy.parameters.json** 是一个参数文件，用于存储要为 VM 设置的用户名和密码等信息。 部署期间，资源管理器模板使用此文件中设置的参数。 

下载这两个文件并在本地保存。 

###  <a name="modify-azuredeployparametersjson"></a>修改 azuredeploy.parameters.json
打开 **azuredeploy.parameters.json** 文件：  
 
- 提供要部署的 **vmSKU**。 我们建议使用 Standard_D2_v3。 
- 指定要对虚拟机规模集使用的 **windowsOSVersion**。 我们建议使用 2016-Datacenter。 
- 使用 **vmssName** 属性命名要部署的虚拟机规模集资源。 例如 **VMSS-WAD-TEST**。    
- 使用 **instanceCount** 属性指定要在虚拟机规模集上运行的 VM 数量。
- 输入虚拟机规模集的 **adminUsername** 和 **adminPassword** 值。 这些参数用于对规模集中的 VM 进行远程访问。 为了避免 VM 被劫持，**请勿**使用此模板中的参数。 机器人在 Internet 上扫描公共 GitHub 存储库中的用户名和密码。 它们可能会使用这些默认值测试 VM。 


###  <a name="modify-azuredeployjson"></a>修改 azuredeploy.json
打开 **azuredeploy.json** 文件。 

添加一个变量，用于保存资源管理器模板中的存储帐户信息。 在诊断配置文件中指定的任何日志或性能计数器将写入到 Azure Monitor 指标存储和此处指定的存储帐户： 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
在 resources 节中找到虚拟机规模集定义，并将 **identity** 节添加到配置中。 这可以确保 Azure 为它分配系统标识。 此步骤还确保规模集中的 VM 可将有关自身的来宾指标发送到 Azure Monitor：  

```json
    { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
```

在虚拟机规模集资源中，找到 **virtualMachineProfile** 节。 添加名为 **extensionsProfile** 的新配置文件来管理扩展。  


在 **extensionProfile** 中，将 **VMSS-WAD-extension** 节中所示的新扩展添加到模板。  此节是 Azure 资源扩展的托管标识，确保 Azure Monitor 接受所发出的指标。 **name** 字段可以包含任何名称。 

MSI 扩展中的以下代码还会将诊断扩展和配置作为扩展资源添加到虚拟机规模集资源。 根据需要任意添加或删除性能计数器。 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


为存储帐户添加 **dependsOn**，确保按正确的顺序创建资源： 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

如果模板中尚未创建存储帐户，请创建一个： 

```json
"resources": [
// add this code    
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
    "accountType": "Standard_LRS"
    }
},
// end added code
{ 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

保存并关闭这两个文件。 

## <a name="deploy-the-resource-manager-template"></a>部署资源管理器模板 

> [!NOTE]  
> 必须运行 Azure 诊断扩展 1.5 版或更高版本，**并**在资源管理器模板中将 **autoUpgradeMinorVersion:** 属性设置为 **true**。 Azure 会在启动 VM 后加载适当的扩展。 如果模板中没有这些设置，请进行更改并重新部署模板。 


若要部署资源管理器模板，请使用 Azure PowerShell：  

1. 启动 PowerShell。 
1. 使用 `Login-AzAccount` 登录到 Azure 门户。
1. 使用 `Get-AzSubscription` 获取订阅列表。
1. 设置要在其中创建或更新虚拟机的订阅： 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. 为所要部署的 VM 创建新资源组。 运行以下命令： 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > 请记得使用为自定义指标启用的 Azure 区域。 请记得使用[为自定义指标启用的 Azure 区域](./metrics-custom-overview.md#supported-regions)。
 
1. 运行以下命令以部署 VM：  

   > [!NOTE]  
   > 若要更新现有的规模集，请将 **-Mode Incremental** 添加到该命令的末尾。 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. 部署成功后，Azure 门户中应会显示该虚拟机规模集。 该规模应该会向 Azure Monitor 发出指标。 

   > [!NOTE]  
   > 你可能会遇到与所选 **vmSkuSize** 相关的错误。 如果发生这种情况，请返回到 **azuredeploy.json** 文件并更新 **vmSkuSize** 参数的默认值。 我们建议尝试 **Standard_DS1_v2**。 


## <a name="chart-your-metrics"></a>绘制指标图表 

1. 登录到 Azure 门户。 

1. 在左侧菜单中选择“监视”  。 

1. 在“监视”页上选择“指标”   。 

   ![监视 - 指标页](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. 将聚合时限更改为“过去 30 分钟”  。  

1. 在资源下拉菜单中，选择创建的虚拟机规模集。  

1. 在命名空间下拉菜单中，选择“azure.vm.windows.guest”。  

1. 在指标下拉菜单中，选择“内存” **“已提交的使用字节数”\%** 。  

然后，还可以选择使用此指标中的维度，来为特定的 VM 绘制此指标的图表，或者绘制规模集中每个 VM 的图表。 



## <a name="next-steps"></a>后续步骤
- 详细了解[自定义指标](metrics-custom-overview.md)。


