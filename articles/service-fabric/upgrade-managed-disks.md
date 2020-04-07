---
title: 升级群集节点以使用 Azure 托管磁盘
description: 下面了解如何升级现有 Service Fabric 群集，以使用群集很少或没有停机时间的 Azure 托管磁盘。
ms.topic: how-to
ms.date: 3/01/2020
ms.openlocfilehash: 2bda5572eda5579bb31c3613b220885f27220d99
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758048"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>升级群集节点以使用 Azure 托管磁盘

[Azure 托管磁盘](../virtual-machines/windows/managed-disks-overview.md)是推荐的磁盘存储产品，用于与 Azure 虚拟机一起持久存储数据。 您可以通过升级作为节点类型的基础的虚拟机扩展集来使用托管磁盘，从而提高 Service Fabric 工作负载的弹性。 下面了解如何升级现有 Service Fabric 群集，以使用群集很少或没有停机时间的 Azure 托管磁盘。

将 Service Fabric 群集节点升级到使用托管磁盘的一般策略是：

1. 部署该节点类型的重复虚拟机缩放集，但将[托管磁盘](https://docs.microsoft.com/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters)对象添加到虚拟机缩放集部署模板`osDisk`的部分。 新的规模集应绑定到与原始负载平衡器/IP 相同的负载平衡器/IP，以便您的客户在迁移期间不会遇到服务中断。

2. 一旦原始扩展集和升级的比例集同时运行，请一次禁用一个原始节点实例，以便系统服务（或状态服务的副本）迁移到新的规模集。

3. 验证群集和新节点是否正常，然后删除已删除节点的原始比例集和节点状态。

本文将引导您完成升级示例群集的主节点类型以使用托管磁盘的步骤，同时避免任何群集停机（请参阅下面的注释）。 示例测试群集的初始状态由一个节点类型的[Silver 持久性](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)组成，由具有五个节点的单个比例集支持。

> [!CAUTION]
> 仅当对群集 DNS 具有依赖项（例如访问[服务结构资源管理器](service-fabric-visualizing-your-cluster.md)时）时，才会遇到此过程中断。 [前端服务的体系结构最佳做法](https://docs.microsoft.com/azure/architecture/microservices/design/gateway)是在节点类型前面设置某种[负载均衡器](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)，使节点交换成为可能，而不会中断。

下面是用于完成升级方案的 Azure 资源管理器的[模板和 cmdlet。](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage) 模板更改将在["部署以下主节点类型的升级规模集](#deploy-an-upgraded-scale-set-for-the-primary-node-type)"中解释。

## <a name="set-up-the-test-cluster"></a>设置测试群集

让我们设置初始服务结构测试群集。 首先，[下载](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage)我们将用来完成此方案的 Azure 资源管理器示例模板。

接下来，登录到 Azure 帐户。

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

以下命令将指导您生成新的自签名证书并部署测试群集。 如果您已经拥有要使用的证书，请跳到[使用现有证书来部署群集](#use-an-existing-certificate-to-deploy-the-cluster)。

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>生成自签名证书并部署群集

首先，分配服务结构群集部署所需的变量。 调整`resourceGroupName`、、`certSubjectName``parameterFilePath`以及特定帐户和环境`templateFilePath`的值：

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
> 在运行命令`certOutputFolder`以部署新的 Service Fabric 群集之前，请确保该位置存在于本地计算机上。

接下来打开[*初始 1NodeType-非托管磁盘.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json)文件，并调整 的值`clusterName`，并`dnsName`对应于您在 PowerShell 中设置的动态值并保存更改。

然后部署服务结构测试群集：

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

部署完成后，在本地计算机上找到 *.pfx*文件`$certPfx`（）， 并将其导入证书存储：

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

该操作将返回证书指纹，您将使用该指纹[连接到新群集](#connect-to-the-new-cluster-and-check-health-status)并检查其运行状况。 （跳过以下部分，这是群集部署的替代方法。

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>使用现有证书部署群集

您还可以使用现有的 Azure 密钥保管库证书来部署测试群集。 为此，您需要[获取对密钥保管库](#obtain-your-key-vault-references)和证书指纹的引用。

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

打开[*初始-1NodeType-非托管磁盘.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json)文件，并将值更改为`clusterName`和`dnsName`更改为唯一的值。

最后，为群集指定一个资源组名称，`templateFilePath`并设置初始`parameterFilePath` *1NodeType-非托管磁盘*文件的和位置：

> [!NOTE]
> 指定的资源组必须已经存在，并且位于与密钥保管库相同的区域。

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

连接到群集并确保其所有五个节点都正常运行（替换群集的`clusterName`和`thumb`变量）：

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

这样，我们就可以开始升级过程了。

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>为主节点类型部署升级的规模集

为了升级或*垂直缩放*节点类型，我们需要部署该节点类型的虚拟机比例集的副本，该副本与原始规模集（包括对同一个`nodeTypeRef`的`subnet`引用`loadBalancerBackendAddressPools`）相同，并且除外，它包括所需的升级/更改及其自己的单独的子网和入站 NAT 地址池。 由于我们正在升级主节点类型，因此新比例集将标记为主 （`isPrimary: true`），就像原始比例集一样。 （对于非主节点类型升级，只需省略此。

为方便起见，已在*升级-1NodeType-2ScaleSet-管理磁盘*[模板](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json)和[参数](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json)文件中为您进行了所需的更改。

以下各节将详细解释模板更改。 如果您愿意，可以跳过说明并继续升级[过程的下一步](#obtain-your-key-vault-references)。

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>使用升级的规模集更新群集模板

以下是原始群集部署模板的逐节修改，用于添加主节点类型的升级规模集。

#### <a name="parameters"></a>参数

为新比例集的实例名称、计数和大小添加参数。 请注意，`vmNodeType1Name`新比例集是唯一的，而计数和大小值与原始比例集相同。

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

在部署模板`variables`部分中，为新规模集的入站 NAT 地址池添加一个条目。

**模板文件**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>资源

在部署模板*资源*部分中，添加新的虚拟机规模集，请记住以下事项：

* 新的比例集引用与原始节点类型相同的节点类型：

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* 新的比例集引用相同的负载均衡器后端地址和子网（但使用不同的负载均衡器入站 NAT 池）：

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

* 与原始比例集一样，新比例集标记为主节点类型。 （升级非主节点类型时，省略此更改。

    ```json
    "isPrimary": true,
    ```

* 与原始规模集不同，新规模集将升级为使用托管磁盘。

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

实现模板和参数文件中的所有更改后，请继续下一节以获取密钥保管库引用并将更新部署到群集。

### <a name="obtain-your-key-vault-references"></a>获取密钥保管库引用

要部署更新的配置，首先您将获得对存储在密钥保管库中的群集证书的多个引用。 查找这些值的最简单方法是通过 Azure 门户。 需要：

* **群集证书的密钥保管库 URL。** 从 Azure 门户中的密钥保管库**Certificates** > 中，选择*所需的证书* > **密钥标识符**：

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **群集证书的指纹。** （如果您[连接到初始群集](#connect-to-the-new-cluster-and-check-health-status)以检查其运行状况，则您可能已经拥有此情况。从 Azure 门户中的**Certificates** > 相同证书边栏选项卡（*证书、所需证书*）中复制**X.509 SHA-1 拇指打印（十六进制）：**

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **密钥保管库的资源 ID。** 从 Azure 门户中的关键保管库中，选择**属性** > **资源 ID**：

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>部署已更新的模板

根据需要调整`parameterFilePath``templateFilePath`和，然后运行以下命令：

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

部署完成后，请再次检查群集运行状况，并确保所有 10 个节点（原始节点上的 5 个节点和新规模集中的 5 个节点）都正常。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>将种子节点迁移到新的规模集

现在，我们已准备好开始禁用原始比例集的节点。 当这些节点被禁用时，系统服务和种子节点将迁移到新规模集的 VM，因为它也被标记为主节点类型。

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

使用 Service Fabric 资源管理器监视种子节点迁移到新比例集以及原始比例集中的节点从*禁用*状态到*禁用*状态的进度。

![显示已禁用节点状态的服务结构资源管理器](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> 完成原始规模集的所有节点的禁用操作可能需要一些时间。 为了保证数据的一致性，一次只能更改一个种子节点。 每次种子节点更改都需要群集更新;因此，替换种子节点需要两个群集升级（每个群集升级用于节点添加和删除）。 升级此示例方案中的五个种子节点将导致 10 个群集升级。

## <a name="remove-the-original-scale-set"></a>删除原始比例集

禁用操作完成后，删除缩放集。

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

在 Service Fabric 资源管理器中，已删除的节点（因此是*群集运行状况状态*）现在将显示为*错误*状态。

![服务结构资源管理器显示处于错误状态的禁用节点](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

从 Service Fabric 群集中删除过时的节点，将群集运行状况状态还原为 *"确定*"。

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![已删除关闭节点的维修结构资源管理器](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>后续步骤

在本演练中，您学习了如何升级 Service Fabric 群集的虚拟机缩放集以使用托管磁盘，同时避免在此过程中的服务中断。 有关相关主题的详细信息，请查看以下资源。

了解如何操作：

* [纵向扩展 Service Fabric 群集主节点类型](service-fabric-scale-up-node-type.md)

* [将规模集模板转换为使用托管磁盘](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [删除 Service Fabric 节点类型](service-fabric-how-to-remove-node-type.md)

另请参阅：

* [示例：升级群集节点以使用 Azure 托管磁盘](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage)

* [垂直缩放注意事项](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)