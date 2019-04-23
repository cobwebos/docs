---
title: 在 Azure 中创建运行 Windows 的 Service Fabric 群集 | Microsoft Docs
description: 本教程介绍如何通过使用 PowerShell 将 Windows Service Fabric 群集部署到 Azure 虚拟网络和网络安全组。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/13/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: dabbefa8ca2073e30948f1c70782f730bceae030
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59049994"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>教程：将运行 Windows 的 Service Fabric 群集部署到 Azure 虚拟网络

本教程是一个系列中的第一部分。 其中介绍了如何通过使用 PowerShell 和模板，将运行 Windows 的 Service Fabric 群集部署到 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)和[网络安全组](../virtual-network/virtual-networks-nsg.md)。 完成本教程后，云中会运行一个可在其中部署应用程序的群集。 要创建使用 Azure CLI 的 Linux 群集，请参阅[在 Azure 上创建安全的 Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)。

本教程介绍一个生产方案。 要创建小型群集以供测试，请参阅[创建测试群集](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 PowerShell 在 Azure 中创建 VNET
> * 创建 Key Vault 并上传证书
> * 设置 Azure Active Directory 身份验证
> * 配置诊断集合
> * 设置 EventStore 服务
> * 设置 Azure Monitor 日志
> * 在 Azure PowerShell 中创建安全的 Service Fabric 群集
> * 使用 X.509 证书保护群集
> * 使用 PowerShell 连接到群集
> * 删除群集

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * 在 Azure 上创建安全群集
> * [监视群集](service-fabric-tutorial-monitor-cluster.md)
> * [缩小或扩大群集](service-fabric-tutorial-scale-cluster.md)
> * [升级群集的运行时](service-fabric-tutorial-upgrade-cluster.md)
> * [删除群集](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 安装 [Service Fabric SDK 和 PowerShell 模块](service-fabric-get-started.md)。
* 安装 [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps)。
* 回顾 [Azure 群集](service-fabric-azure-clusters-overview.md)的关键概念。
* 为生产群集部署[计划并准备](service-fabric-cluster-azure-deployment-preparation.md)。

以下步骤将创建一个七节点 Service Fabric 群集。 使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)计算在 Azure 中运行 Service Fabric 群集所产生的成本。

## <a name="download-and-explore-the-template"></a>下载并浏览模板

下载以下 Azure 资源管理器模板文件：

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

此模板将包含七个虚拟机和三个节点类型的安全群集部署到虚拟网络和网络安全组中。  其他示例模板可以在 [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates) 上找到。 [azuredeploy.json][template] 部署若干资源，包括以下资源。

### <a name="service-fabric-cluster"></a>Service Fabric 群集

在 **Microsoft.ServiceFabric/clusters** 资源中，配置了具有以下特征的 Windows 群集：

* 三个节点类型。
* 主节点类型包含五个节点（可在模板参数中配置），其他两个节点类型各包含一个节点。
* OS：包含容器的 Windows Server 2016 Datacenter（可在模板参数中配置）。
* 证书保护（可在模板参数中配置）。
* 已启用[反向代理](service-fabric-reverseproxy.md)。
* 已启用 [DNS 服务](service-fabric-dnsservice.md)。
* 铜级[持久性级别](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)（可在模板参数中配置）。
* 银级[可靠性级别](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)（可在模板参数中配置）。
* 客户端连接终结点：19000（可在模板参数中配置）。
* HTTP 网关终结点：19080（可在模板参数中配置）。

### <a name="azure-load-balancer"></a>Azure 负载均衡器

在 Microsoft.Network/loadBalancers 资源中配置负载均衡器。 为以下端口设置探测和规则：

* 客户端连接终结点：19000
* HTTP 网关终结点：19080
* 应用程序端口：80
* 应用程序端口：443
* Service Fabric 反向代理：19081

如需其他应用程序端口，则需要调整 Microsoft.Network/loadBalancers 资源和 Microsoft.Network/networkSecurityGroups 资源，以允许传入流量。

### <a name="virtual-network-subnet-and-network-security-group"></a>虚拟网络、子网和网络安全组

虚拟网络、子网和网络安全组的名称已在模板参数中声明。 虚拟网络和子网的地址空间也在模板参数中声明，并在 **Microsoft.Network/virtualNetworks** 资源中配置：

* 虚拟网络地址空间：172.16.0.0/20
* Service Fabric 子网地址空间：172.16.2.0/23

在 **Microsoft.Network/networkSecurityGroups** 资源中启用以下入站流量规则。 可以通过更改模板变量来更改端口值。

* ClientConnectionEndpoint (TCP)：19000
* HttpGatewayEndpoint (HTTP/TCP)：19080
* SMB：445
* Internodecommunication：1025、1026、1027
* 临时端口范围：49152 到 65534（至少需要 256 个端口）。
* 应用程序使用的端口：80 和 443
* 应用程序端口范围：49152 到 65534（用于测试服务间的通信。 其他端口不会在负载平衡器上打开）。
* 阻止其他所有端口

如需其他应用程序端口，则需要调整 Microsoft.Network/loadBalancers 资源和 Microsoft.Network/networkSecurityGroups 资源，以允许传入流量。

### <a name="windows-defender"></a>Windows Defender
默认情况下，[Windows Defender 防病毒程序](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)已安装在 Windows Server 2016 上并在其上运行。 用户界面默认安装在一些 SKU 上，但不是必需的。 对于在模板中声明的每个节点类型/VM 规模集，将会使用 [Azure VM 防病毒扩展](/azure/virtual-machines/extensions/iaas-antimalware-windows)排除 Service Fabric 目录和进程：

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>设置模板参数

[azuredeploy.parameters.json][parameters] 参数文件声明用于部署群集和关联资源的多个值。 下面是要为部署修改的参数：

**Parameter** | **示例值** | **说明** 
|---|---|---|
|adminUserName|vmadmin| 群集 VM 的管理员用户名。 [VM 的用户名要求](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm)。 |
|adminPassword|Password#1234| 群集 VM 的管理员密码。 [VM 的密码要求](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm)。|
|clusterName|mysfcluster123| 群集的名称。 仅可包含字母和数字。 长度可介于 3 到 23 个字符之间。|
|位置|southcentralus| 群集的位置。 |
|certificateThumbprint|| <p>如果创建自签名证书或提供证书文件，则值应为空。</p><p>若要使用之前上传到密钥保管库的现有证书，请填写证书 SHA1 指纹值。 例如“6190390162C988701DB5676EB81083EA608DCCF3”。</p> |
|certificateUrlValue|| <p>如果创建自签名证书或提供证书文件，则值应为空。 </p><p>若要使用之前上传到 Key Vault 的现有证书，请填写证书 URL。 例如，“https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346”。</p>|
|sourceVaultValue||<p>如果创建自签名证书或提供证书文件，则值应为空。</p><p>若要使用之前上传到 Key Vault 的现有证书，请填写源保管库值。 例如“/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”。</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>设置 Azure Active Directory 客户端身份验证
如果将 Service Fabric 群集部署在某个公共网络中，而该网络托管在 Azure 上，则对于客户端到节点型相互身份验证，建议如下：
* 对客户端标识使用 Azure Active Directory。
* 对服务器标识使用证书，并对 HTTP 通信进行 SSL 加密。

必须在[创建群集](#createvaultandcert)之前设置 Azure Active Directory (Azure AD)，以便针对 Service Fabric 群集对客户端进行身份验证。 通过 Azure AD，组织（称为租户）可管理用户对应用程序的访问。 

Service Fabric 群集提供其管理功能的各种入口点，包括基于 Web 的 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 和 [Visual Studio](service-fabric-manage-application-in-visual-studio.md)。 因此，需要创建两个 Azure AD 应用程序来控制对群集的访问：一个 Web 应用程序和一个本机应用程序。  创建应用程序后，将用户分配到只读和管理员角色。

> [!NOTE]
> 在创建群集之前，请完成以下步骤。 因为脚本需要群集名称和终结点，这些值应是规划的值，而不是已创建的值。

本文假设已创建了一个租户。 如果未创建，请先阅读[如何获取 Azure Active Directory 租户](../active-directory/develop/quickstart-create-new-tenant.md)。

为了简化涉及到配置 Azure AD 与 Service Fabric 群集的步骤，我们创建了一组 Windows PowerShell 脚本。 [将脚本下载](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool)到计算机。

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>创建 Azure AD 应用程序并为用户分配角色
创建两个 Azure AD 应用程序来控制对群集的访问权限：一个 Web 应用程序和一个本机应用程序。 创建用于表示群集的应用程序后，请将用户分配到 [Service Fabric 支持的角色](service-fabric-cluster-security-roles.md)：只读和管理员。

运行 `SetupApplications.ps1` 并提供租户 ID、群集名称和 Web 应用程序回复 URL 作为参数。 请指定用户的用户名和密码。 例如：

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> 对于区域云（例如，Azure 政府、Azure 中国、Azure 德国），请指定 `-Location` 参数。

可在 [Azure 门户](https://portal.azure.com)中找到 *TenantId* 或目录 ID。 选择“Azure Active Directory” > “属性”并复制“目录 ID”值。

将 ClusterName 用作脚本创建的 Azure AD 应用程序的前缀。 无需完全匹配实际的群集名称。 只是为了操作更加简便，可将 Azure AD 项目映射到正在使用的 Service Fabric 群集。

WebApplicationReplyUrl 是 Azure AD 在完成登录过程之后返回给用户的默认终结点。 将此终结点设置为群集的 Service Fabric Explorer 的终结点，默认值为：

https://&lt;cluster_domain&gt;:19080/Explorer

系统会提示登录到具有 Azure AD 租户管理权限的帐户。 完成此操作后，脚本会创建 Web 和本机应用程序来代表 Service Fabric 群集。 在 [Azure 门户](https://portal.azure.com)中的租户的应用程序中，应会看到两个新条目：

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

创建群集时该脚本显示资源管理器模板所需的 JSON，因此最好不要关闭 PowerShell 窗口。

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>添加 Azure AD 配置以使用 Azure AD 访问客户端
在 [azuredeploy.json][template] 的 **Microsoft.ServiceFabric/clusters** 节中配置 Azure AD。 为租户 ID、群集应用程序 ID 和客户端应用程序 ID 添加参数。  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

在 [azuredeploy.parameters.json][parameters] 参数文件中添加参数值。 例如：

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```
<a id="configurediagnostics" name="configurediagnostics_anchor"></a>

## <a name="configure-diagnostics-collection-on-the-cluster"></a>在群集上配置诊断集合
运行 Service Fabric 群集时，最好是从一个中心位置的所有节点中收集日志。 将日志放在中心位置可帮助分析和排查群集中的问题，或该群集中运行的应用程序与服务的问题。

上传和收集日志的方式之一是使用可将日志上传到 Azure 存储、也能选择发送日志到 Azure Application Insights 或事件中心的 Azure 诊断 (WAD) 扩展。 也可以使用外部进程读取存储中的事件，并将其放在分析平台产品（例如 Azure Monitor 日志或其他日志分析解决方案）中。

如果是按照本教程执行的操作，则已在[模板][template] 中配置了诊断集合。

如果存在尚未部署诊断的现有群集，可以通过群集模板来添加或更新该扩展。 修改用于创建现有群集的资源管理器模板，或者从门户下载该模板。 执行以下任务来修改 template.json 文件：

将新存储资源添加到模板中的资源部分：
```json
"resources": [
...
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
...
]
```

下一步，将存储帐户名称和类型的参数添加到模板的参数部分。 将占位符文本 storage account name goes here 替换为所需的存储帐户的名称。

```json
"parameters": {
...
"applicationDiagnosticsStorageAccountType": {
    "type": "string",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
    "description": "Replication option for the application diagnostics storage account"
    }
},
"applicationDiagnosticsStorageAccountName": {
    "type": "string",
    "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
    "metadata": {
    "description": "Name for the storage account that contains application diagnostics data from the cluster"
    }
},
...
}
```

下一步，将 IaaSDiagnostics 扩展名添加到群集中每个 Microsoft.Compute/virtualMachineScaleSets 资源的 VirtualMachineProfile 属性的扩展数组中。  如果使用的是[示例模板][template]，则有三个虚拟机规模集（每个节点类型对应集群中的一个规模集）。

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
                    "properties": {
                        "type": "IaaSDiagnostics",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                        "storageAccountEndPoint": "https://core.windows.net/"
                        },
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "settings": {
                        "WadCfg": {
                            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
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
                            }
                        },
                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                        },
                        "typeHandlerVersion": "1.5"
                    }
                }
            ...
            ]
        }
    }
}
```
<a id="configureeventstore" name="configureeventstore_anchor"></a>

## <a name="configure-the-eventstore-service"></a>配置 EventStore 服务
EventStore 服务是 Service Fabric 中的监视选项。 EventStore 提供了在给定时间点中了解群集或工作负载的状态的方法。 EventStore 是有状态 Service Fabric 服务，它维护群集中的事件。 事件通过 Service Fabric Explorer、REST 和 API 公开。 EventStore 直接查询群集来获取关于群集中的任何实体的诊断数据，并且应当用来帮助执行以下操作：

* 在开发或测试时或者当可能使用监视管道时对问题进行诊断
* 确认正在正确处理对群集执行的管理操作
* 获取 Service Fabric 如何与特定实体进行交互的“快照”



要在群集上启用 EventStore 服务，请将以下内容添加到 Microsoft.ServiceFabric/clusters 资源的 fabricSettings 属性中：

```json
"apiVersion": "2018-02-01",
"type": "Microsoft.ServiceFabric/clusters",
"name": "[parameters('clusterName')]",
"properties": {
    ...
    "fabricSettings": [
        ...
        {
            "name": "EventStoreService",
            "parameters": [
                {
                "name": "TargetReplicaSetSize",
                "value": "3"
                },
                {
                "name": "MinReplicaSetSize",
                "value": "1"
                }
            ]
        }
    ]
}
```
<a id="configureloganalytics" name="configureloganalytics_anchor"></a>

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>为群集设置 Azure Monitor 日志

要监视群集级别的事件，建议使用 Azure Monitor 日志。 要设置 Azure Monitor 日志来监视群集，需要[启用诊断功能以查看群集级别事件](#configure-diagnostics-collection-on-the-cluster)。  

需要将工作区连接到来自群集的诊断数据。  此日志数据存储在 applicationDiagnosticsStorageAccountName 存储帐户、WADServiceFabric*EventTable、WADWindowsEventLogsTable 和 WADETWEventTable 表中。

添加 Azure Log Analytics 工作区并将解决方案添加到该工作区：

```json
"resources": [
    ...
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Memory",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Available MBytes"
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter2",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Service Fabric Service",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Average milliseconds per request"
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
]
```

下一步，添加参数
```json
"parameters": {
    ...
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "mysfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "West Europe",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
}
```

下一步，添加变量：
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

将 Log Analytics 代理扩展添加到群集中的每个虚拟机规模集，并将代理连接到 Log Analytics 工作区。 这可收集关于容器、应用程序和性能监视的诊断数据。 通过将其作为扩展添加到虚拟机规模集资源，Azure 资源管理器可确保它安装在每个节点上，即使在缩放群集时也是如此。

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "MicrosoftMonitoringAgent",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                        }
                    }
                }
            ...
            ]
        }
    }
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>部署虚拟网络和群集

接下来，设置网络拓扑并部署 Service Fabric 群集。 [azuredeploy.json][template] 资源管理器模板针对 Service Fabric 创建虚拟网络、子网和网络安全组。 该模板还会部署一个已启用证书安全性的群集。 对于生产群集，请使用证书颁发机构提供的证书作为群集证书。 可以使用自签名证书来保护测试群集。

本文中的模板部署一个群集，该群集使用证书指纹来标识群集证书。 两个证书不能有相同的指纹，否则会增加证书管理的难度。 将已部署的群集从证书指纹切换为证书公用名称可简化证书管理。 若要了解如何更新群集，以便使用证书公用名称进行证书管理，请阅读[将群集更改为使用证书公用名称进行管理](service-fabric-cluster-change-cert-thumbprint-to-cn.md)。

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>通过使用现有证书创建群集

以下脚本使用 [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) cmdlet 和模板在 Azure 中部署新群集。 该 cmdlet 在 Azure 中创建新的密钥保管库，并上传证书。

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # Must match the clustername parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>通过使用新的自签名证书创建群集

以下脚本使用 [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) cmdlet 和模板在 Azure 中部署新群集。 该 cmdlet 在 Azure 中创建新的 Key Vault、向 Key Vault 添加新的自签名证书，并将证书文件下载到本地。

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>连接到安全群集

通过使用连同 Service Fabric SDK 一起安装的 Service Fabric PowerShell 模块连接到群集。  首先，将证书安装到计算机上当前用户的“个人(我的)”存储中。 运行以下 PowerShell 命令：

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

现在可以连接到安全群集了。

**Service Fabric** PowerShell 模块提供许多 cmdlet 用于管理 Service Fabric 群集、应用程序和服务。 使用 [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) cmdlet 连接到安全群集。 可在上一步骤的输出中找到证书 SHA1 指纹和连接终结点详细信息。

如果以前设置过 Azure AD 客户端身份验证，请运行以下命令： 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

如果没有设置过 Azure AD 客户端身份验证，请运行以下命令：
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

通过使用 [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) cmdlet 检查是否已连接并且群集是否正常运行。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>清理资源

本教程系列中的其他文章将会使用本文中创建的群集。 如果不立即转到下一篇文章，可能需要[删除该群集](service-fabric-cluster-delete.md)，以避免产生费用。

## <a name="next-steps"></a>后续步骤

请转到以下教程了解如何缩放群集。

> [!div class="checklist"]
> * 使用 PowerShell 在 Azure 中创建 VNET
> * 创建 Key Vault 并上传证书
> * 设置 Azure Active Directory 身份验证
> * 配置诊断集合
> * 设置 EventStore 服务
> * 设置 Azure Monitor 日志
> * 在 Azure PowerShell 中创建安全的 Service Fabric 群集
> * 使用 X.509 证书保护群集
> * 使用 PowerShell 连接到群集
> * 删除群集

下一步，请转到以下教程了解如何监视群集。
> [!div class="nextstepaction"]
> [监视群集](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
