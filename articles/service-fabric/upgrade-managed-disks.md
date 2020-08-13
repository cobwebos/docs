---
title: 将群集节点升级为使用 Azure 托管磁盘
description: 本文介绍了如何在只需群集短暂停机甚至无需其停机的前提下，将现有 Service Fabric 群集升级为使用 Azure 托管磁盘。
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: 1ca85af86df28691e2194c40e1cdde1abd7c8a4d
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192300"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>将群集节点升级为使用 Azure 托管磁盘

[Azure 托管磁盘](../virtual-machines/windows/managed-disks-overview.md)是推荐用于 Azure 虚拟机的磁盘存储产品/服务，可以持久存储数据。 将你的节点类型所基于的虚拟机规模集升级为使用托管磁盘，可以改善 Service Fabric 工作负荷的复原能力。 本文介绍了如何在只需群集短暂停机甚至无需其停机的前提下，将现有 Service Fabric 群集升级为使用 Azure 托管磁盘。

将 Service Fabric 群集节点升级为使用托管磁盘的一般策略是：

1. 部署该节点类型的另一重复虚拟机规模集，但在虚拟机规模集部署模板的 `osDisk` 节中添加 [managedDisk](/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) 对象。 新规模集应绑定到原始规模集所用的同一负载均衡器/IP，使客户在迁移期间不会遇到服务中断。

2. 在原始规模集和升级后的规模集一起运行后，逐个禁用原始节点实例，以便将系统服务（或有状态服务的副本）迁移到新规模集。

3. 验证群集和新节点是否正常，然后删除原始规模集，以及已删除的节点的节点状态。

本文将引导你完成将示例群集的主要节点类型升级为使用托管磁盘的步骤，同时避免发生任何群集停机（参阅下面的注释）。 示例测试群集的初始状态包括一个[银级持久性](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)的节点类型，该节点类型由包含五个节点的单个规模集提供支持。

> [!NOTE]
> 基本 SKU 负载均衡器的限制阻止添加其他规模集。 建议改用标准 SKU 负载均衡器。 有关详细信息，请参阅 [两个 sku 的比较](/azure/load-balancer/skus)。

> [!CAUTION]
> 仅当你依赖于群集 DNS 时（例如，在访问 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 时），才会在此过程中遇到服务中断。 [适用于前端服务的体系结构最佳做法](/azure/architecture/microservices/design/gateway)要求在你的节点类型的前面使用某种[负载均衡器](/azure/architecture/guide/technology-choices/load-balancing-overview)，以便无需中断服务即可进行节点交换。

下面是用于完成升级方案的 Azure 资源管理器的[模板和 cmdlet](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)。 下文中的[为主要节点类型部署升级规模集](#deploy-an-upgraded-scale-set-for-the-primary-node-type)将介绍模板更改。

## <a name="set-up-the-test-cluster"></a>设置测试群集

接下来让我们设置初始 Service Fabric 测试群集。 首先，[下载](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)用于完成此方案的 Azure 资源管理器示例模板。

然后，登录到 Azure 帐户。

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

以下命令将引导你生成新的自签名证书并部署测试群集。 如果你已有一个想要使用的证书，请跳到[使用现有证书部署群集](#use-an-existing-certificate-to-deploy-the-cluster)。

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>生成自签名证书并部署群集

首先，分配 Service Fabric 群集部署所需的变量。 根据具体的帐户和环境调整 `resourceGroupName`、`certSubjectName`、`parameterFilePath` 和 `templateFilePath` 的值：

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> 在运行该命令部署新的 Service Fabric 群集之前，请确保 `certOutputFolder` 位置存在于你的本地计算机上。

接下来，打开 [*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) 文件，并调整 `clusterName` 和 `dnsName` 的值，使之对应于你在 PowerShell 中设置的动态值，然后保存更改。

接下来部署 Service Fabric 测试群集：

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

部署完成后，在本地计算机上找到 *.pfx* 文件 (`$certPfx`) 并将其导入到证书存储中：

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

该操作将返回证书指纹，在[连接到新群集](#connect-to-the-new-cluster-and-check-health-status)以及检查其运行状况时将要使用该指纹。 （请跳过以下部分，其中介绍的是替代的群集部署方法。）

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>使用现有证书部署群集

还可以使用现有 Azure Key Vault 证书来部署测试群集。 为此，需要[获取对 Key Vault 和证书指纹的引用](#obtain-your-key-vault-references)。

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

打开 [*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) 文件，并将 `clusterName` 和 `dnsName` 的值更改为唯一值。

最后，指定群集的资源组名称，并设置 *Initial-1NodeType-UnmanagedDisks* 文件的 `templateFilePath` 和 `parameterFilePath` 位置：

> [!NOTE]
> 指定的资源组必须已存在，并且与 Key Vault 位于同一区域。

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

最后，运行以下命令以部署初始测试群集：

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>连接到新群集并检查运行状况

连接到群集，并确保其所有五个节点均正常（请针对你的群集替换 `clusterName` 和 `thumb` 变量）：

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

完成上述操作后，便可以开始执行升级过程了。

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>为主要节点类型部署升级的规模集

若要升级或纵向缩放某个节点类型，需要部署该节点类型的虚拟机规模集的副本，该副本与原始规模集相同（包括对相同 `nodeTypeRef`、`subnet` 和 `loadBalancerBackendAddressPools` 的引用），只不过，其中还包含所需的升级/更改及其自身的单独子网和入站 NAT 地址池。 由于我们要升级主要节点类型，因此，新规模集将如同原始规模集一样标记为主节点类型 (`isPrimary: true`)。 （对于非主节点类型升级，请省略此项更改。）

为方便起见，*Upgrade-1NodeType-2ScaleSets-ManagedDisks* [模板](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) 和 [parameters](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json) 文件中已经为你做出了所需的更改。

以下部分将详细说明模板更改。 如果需要，可以跳过说明并转到[升级过程的下一步骤](#obtain-your-key-vault-references)。

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>使用升级的规模集更新群集模板

下面是用来为主要节点类型添加已升级规模集的原始群集部署模板的各个节的修改。

#### <a name="parameters"></a>parameters

添加新规模集的实例名称、计数和大小的参数。 请注意，`vmNodeType1Name` 对于新规模集是唯一的，而计数和大小值与原始规模集相同。

**模板文件**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**参数文件**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>变量

在部署模板的 `variables` 节中，为新规模集的入站 NAT 地址池添加一个条目。

**模板文件**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>资源

在部署模板的 *resources* 节中添加新的虚拟机规模集，同时请注意以下事项：

* 新规模集引用与原始规模集相同的节点类型：

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* 新规模集引用相同的负载均衡器后端地址和子网（但使用不同的负载均衡器入站 NAT 池）：

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* 与原始规模集一样，新规模集将标记为主要节点类型。 （升级非主要节点类型时，请省略此项更改。）

    ```json
    "isPrimary": true,
    ```

* 与原始规模集不同，新规模集将升级为使用托管磁盘。

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

实现模板和 parameters 文件中的所有更改后，转到下一部分获取 Key Vault 引用并将更新部署到群集。

### <a name="obtain-your-key-vault-references"></a>获取 Key Vault 引用

若要部署已更新的配置，首先需要获取对 Key Vault 中存储的群集证书的多个引用。 查找这些值的最简单方法是使用 Azure 门户。 需要：

* **群集证书的 Key Vault URL。** 在 Azure 门户上你的 Key Vault 中，选择“证书” > <所需的证书> > “机密标识符”：

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **群集证书的指纹。** （如果已[连接到初始群集](#connect-to-the-new-cluster-and-check-health-status)来检查其运行状况，则可能已获取此指纹。）在 Azure 门户上的同一证书边栏选项卡（“证书” > <所需的证书>）中，复制“X.509 SHA-1 指纹(十六进制)”：

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **Key Vault 的资源 ID。** 在 Azure 门户上你的 Key Vault 中，选择“属性” > “资源 ID”： 

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>部署已更新的模板

根据需要调整 `parameterFilePath` 和 `templateFilePath`，然后运行以下命令：

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

部署完成后，再次检查群集运行状况，并确保所有 10 个节点（原始规模集和新规模集上各有 5 个）均正常。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>将种子节点迁移到新规模集

现在，我们已准备好开始禁用原始规模集的节点。 由于这些节点将被禁用，而新规模集也会标记为主要节点类型，因此，系统服务和种子节点将迁移到新规模集的 VM。

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

使用 Service Fabric Explorer 来监视将种子节点迁移到新规模集的过程，以及原始规模集中的节点从“正在禁用”到“已禁用”状态的转换进度。 

![显示已禁用节点状态的 Service Fabric Explorer](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> 在原始规模集的所有节点上完成禁用操作可能需要一段时间。 为了保证数据一致性，每次只能更改一个种子节点。 每次进行种子节点更改都需要更新群集；因此，替换种子节点需要执行群集升级两次（添加和删除节点各需要执行一次）。 在此示例方案中升级 5 个种子节点需要执行群集升级 10 次。

## <a name="remove-the-original-scale-set"></a>删除原始规模集

禁用操作完成后，请删除规模集。

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

在 Service Fabric Explorer 中，已删除的节点（因此也包括“群集运行状况”）现在会显示为“错误”状态。 

![显示处于“错误”状态的已禁用节点的 Service Fabric Explorer](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

从 Service Fabric 群集中删除已过时的节点可将“群集运行状况”还原为“正常”。

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Service Fabric Explorer，其中包含处于“错误”状态的已关闭节点](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>后续步骤

在本演练中，你已了解如何将 Service Fabric 群集的虚拟机规模集升级为使用托管磁盘，并在此过程中避免发生服务中断。 如需相关主题的详细信息，请查看以下资源。

了解如何：

* [纵向扩展 Service Fabric 群集主节点类型](service-fabric-scale-up-node-type.md)

* [将规模集模板转换为使用托管磁盘](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [删除 Service Fabric 节点类型](service-fabric-how-to-remove-node-type.md)

另请参阅：

* [示例：将群集节点升级为使用 Azure 托管磁盘](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)

* [纵向缩放注意事项](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)
