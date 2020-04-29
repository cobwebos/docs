---
title: 升级群集节点以使用 Azure 托管磁盘
description: 下面介绍如何升级现有 Service Fabric 群集，以使用 Azure 托管磁盘，使群集的停机时间很短或不停机。
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: 5f4698718a35970e47de2a0ee6d053802c8ef919
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80991205"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>升级群集节点以使用 Azure 托管磁盘

[Azure 托管磁盘](../virtual-machines/windows/managed-disks-overview.md)是推荐用于 azure 虚拟机的磁盘存储产品，用于持久存储数据。 你可以通过升级虚拟机规模集（该规模集以你的节点类型为使用托管磁盘）来改善 Service Fabric 工作负荷的复原能力。 下面介绍如何升级现有 Service Fabric 群集，以使用 Azure 托管磁盘，使群集的停机时间很短或不停机。

将 Service Fabric 群集节点升级为使用托管磁盘的常规策略是：

1. 部署该节点类型的其他重复虚拟机规模集，但将[managedDisk](https://docs.microsoft.com/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters)对象添加到了虚拟机`osDisk`规模集部署模板的部分。 新的规模集应绑定到与原始服务器相同的负载均衡器/IP，以便您的客户在迁移过程中不会出现服务中断。

2. 初始和升级规模集并行运行后，一次禁用一个原始节点实例，以便将系统服务（或有状态服务的副本）迁移到新的规模集。

3. 验证群集和新节点是否运行正常，然后删除已删除节点的原始规模集和节点状态。

本文将指导你完成将示例群集的主节点类型升级到使用托管磁盘的步骤，同时避免任何群集停机时间（请参阅下面的注释）。 示例测试群集的初始状态包括：[银持久性](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)的一个节点类型，由具有五个节点的单个规模集提供支持。

> [!CAUTION]
> 只有在群集 DNS 上有依赖项（例如访问[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)）时，此过程才会出现中断。 [前端服务的体系结构最佳实践](https://docs.microsoft.com/azure/architecture/microservices/design/gateway)是在节点类型前面使用某种[负载均衡器](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)，以便在不中断的情况下进行节点交换。

下面是用于完成升级方案的 Azure 资源管理器的[模板和 cmdlet](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) 。 在[为以下主节点类型部署升级规模集](#deploy-an-upgraded-scale-set-for-the-primary-node-type)中将介绍模板更改。

## <a name="set-up-the-test-cluster"></a>设置测试群集

接下来，设置初始 Service Fabric 测试群集。 首先，[下载](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)用于完成此方案的 Azure 资源管理器示例模板。

接下来，登录到 Azure 帐户。

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

以下命令将引导你完成生成新的自签名证书并部署测试群集的步骤。 如果你已经有想要使用的证书，请跳到[使用现有证书来部署群集](#use-an-existing-certificate-to-deploy-the-cluster)。

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>生成自签名证书并部署群集

首先，为 Service Fabric 群集部署分配所需的变量。 针对特定帐户和`resourceGroupName`环境`certSubjectName`调整`parameterFilePath`、、 `templateFilePath`和的值：

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
> 请确保在`certOutputFolder`运行该命令之前，该位置存在于本地计算机上，以便部署新的 Service Fabric 群集。

接下来，打开[*1NodeType-UnmanagedDisks*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json)文件，并调整`clusterName`和`dnsName`的值，使其与在 PowerShell 中设置的动态值相对应，并保存所做的更改。

然后部署 Service Fabric 测试群集：

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

部署完成后，在本地计算机上找到 *.pfx*文件（`$certPfx`），并将其导入到证书存储中：

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

该操作将返回证书指纹，你将使用该指纹[连接到新群集](#connect-to-the-new-cluster-and-check-health-status)并检查其运行状况状态。 （跳过以下部分，这是群集部署的替代方法。）

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>使用现有证书部署群集

你还可以使用现有的 Azure Key Vault 证书来部署测试群集。 为此，需要[获取对 Key Vault](#obtain-your-key-vault-references)和证书指纹的引用。

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

打开[*1NodeType-UnmanagedDisks*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json)文件，并将`clusterName`和`dnsName`的值更改为唯一的值。

最后，指定群集的资源组名称，并设置*1NodeType-UnmanagedDisks*文件的`templateFilePath`和`parameterFilePath`位置：

> [!NOTE]
> 指定的资源组必须已经存在，并且与 Key Vault 位于同一区域。

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

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>连接到新群集并检查运行状况状态

连接到群集，并确保其所有节点都处于正常状态（替换群集的`clusterName`和`thumb`变量）：

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

为此，我们已准备好开始升级过程。

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>为主节点类型部署升级规模集

若要升级或*垂直缩放*节点类型，我们将需要部署该节点类型的虚拟机规模集的副本，此副本与原始规模集（包括对相同`nodeTypeRef`、 `subnet`和`loadBalancerBackendAddressPools`的引用）不同，只不过它包含所需的升级/更改和自己的单独子网和入站 NAT 地址池。 由于我们要升级主节点类型，因此，新的规模集将标记为主节点（`isPrimary: true`），就像原规模集一样。 （对于非主节点类型升级，只需省略此项即可。）

为方便起见，已在*1NodeType-2ScaleSets-ManagedDisks* [模板](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json)和[参数](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json)文件中为你完成了所需的更改。

以下部分将详细介绍模板更改。 如果愿意，可以跳过说明，继续执行[升级过程的下一步](#obtain-your-key-vault-references)。

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>更新具有升级规模集的群集模板

下面是用于为主节点类型添加升级规模集的原始群集部署模板的节部分修改。

#### <a name="parameters"></a>参数

添加新规模集的实例名称、计数和大小的参数。 请注意`vmNodeType1Name` ，对于新规模集是唯一的，而 "计数" 和 "大小" 值与原始规模集完全相同。

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

在 "部署模板`variables` " 部分中，为新规模集的入站 NAT 地址池添加一个条目。

**模板文件**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>资源

在 "部署模板*资源*" 部分中，添加新的虚拟机规模集，请记住以下事项：

* 新的规模集引用与原节点类型相同的节点类型：

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

* 与原始规模集一样，新的规模集将标记为主节点类型。 （升级非主节点类型时，请省略此更改。）

    ```json
    "isPrimary": true,
    ```

* 与原始规模集不同，新的规模集会升级为使用托管磁盘。

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

实现模板和参数文件中的所有更改后，请转到下一节获取 Key Vault 引用并将更新部署到群集。

### <a name="obtain-your-key-vault-references"></a>获取 Key Vault 引用

若要部署已更新的配置，你将首先获取对存储在 Key Vault 中的群集证书的多个引用。 查找这些值的最简单方法是通过 Azure 门户。 需要：

* **群集证书的 Key Vault URL。** 从 Azure 门户的 Key Vault 中，选择 "证书"，选择 "**证书** > *Your desired certificate* > **"：**

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **群集证书的指纹。** （如果已[连接到初始群集](#connect-to-the-new-cluster-and-check-health-status)来检查其运行状况，则可能已拥有此选项。）在 Azure 门户中，从同一 "证书" 边栏选项卡（**证书** > *所需的证书*）复制**x.509 sha-1 指纹（十六进制）**：

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **Key Vault 的资源 ID。** 从 Azure 门户的 Key Vault 中，选择 "**属性** > " "**资源 ID**"：

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>部署已更新的模板

根据需要`parameterFilePath`调整`templateFilePath`和，并运行以下命令：

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

部署完成后，再次检查群集运行状况，并确保所有10个节点（在原始规模集上为五个节点，在新规模集上为5个）正常。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>将种子节点迁移到新规模集

现在，我们已准备好开始禁用原始规模集的节点。 由于这些节点被禁用，系统服务和种子节点会迁移到新规模集的 Vm，因为它也被标记为主节点类型。

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

使用 Service Fabric Explorer 来监视种子节点到新规模集的迁移，以及从*禁用*到*禁用*状态的原始规模集中节点的进度。

![显示禁用节点状态的 Service Fabric Explorer](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> 可能需要一段时间才能完成原始规模集的所有节点上的禁用操作。 为了保证数据一致性，一次只能更改一个种子节点。 每个种子节点更改需要群集更新;因此，替换种子节点需要两个群集升级（每个升级用于节点的添加和删除）。 在此示例方案中升级五种子节点将导致10个群集升级。

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

在 Service Fabric Explorer 中，已删除的节点（因而*群集运行状况状态*）现在将显示为 "*错误*" 状态。

![显示处于错误状态的已禁用节点 Service Fabric Explorer](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

从 Service Fabric 群集中删除已过时的节点，以将群集运行状况状态还原为 *"正常"*。

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![删除了处于错误状态的关闭节点 Service Fabric Explorer](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>后续步骤

在本演练中，你学习了如何将 Service Fabric 群集的虚拟机规模集升级为使用托管磁盘，同时避免了在此过程中服务中断。 有关相关主题的详细信息，请参阅以下资源。

了解如何操作：

* [纵向扩展 Service Fabric 群集主节点类型](service-fabric-scale-up-node-type.md)

* [将规模集模板转换为使用托管磁盘](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [删除 Service Fabric 节点类型](service-fabric-how-to-remove-node-type.md)

另请参阅：

* [示例：将群集节点升级为使用 Azure 托管磁盘](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)

* [垂直缩放注意事项](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)