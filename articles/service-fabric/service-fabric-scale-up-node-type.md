---
title: 纵向扩展 Azure Service Fabric 节点类型
description: 了解如何通过添加虚拟机规模集缩放 Service Fabric 群集。
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 2d700367049e0bf9bf710aad110c850a78c26220
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610687"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>纵向扩展 Service Fabric 群集主节点类型
本文介绍如何通过增加虚拟机资源来纵向扩展 Service Fabric 群集主节点类型。 Service Fabric 群集是一组通过网络连接在一起的虚拟机或物理计算机，微服务会在其中部署和管理。 属于群集一部分的计算机或 VM 称为节点。 虚拟机规模集是一种 Azure 计算资源，用于将一组 VM 作为一个集进行部署和管理。 Azure 群集中定义的每个节点类型[设置为独立的规模集](service-fabric-cluster-nodetypes.md)。 然后可以单独管理每个节点类型。 创建 Service Fabric 群集后，可以纵向缩放群集节点类型（更改节点的资源）或升级节点类型 VM 的操作系统。  随时可以缩放群集，即使该群集上正在运行工作负荷。  在缩放群集的同时，应用程序也会随之自动缩放。

> [!WARNING]
> 如果群集状态为 "不正常"，则不要尝试使用主节点类型 "向上缩放" 过程，因为这将仅进一步破坏群集的稳定性。
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>升级主节点类型 VM 大小和操作系统的过程
以下是主节点类型 VM 的 VM 大小和操作系统的更新过程。  升级后，主节点类型 VM 的大小为标准 D4_V2，并且运行带容器的 Windows Server 2016 Datacenter。

> [!WARNING]
> 在生产群集上尝试执行此过程之前，建议先研究示例模板并对测试群集验证此过程。 该群集也会有段时间不可用。 不能并行对声明为相同 NodeType 的多个 VMSS 执行更改，需要执行单独的部署操作来单独为每个 NodeType VMSS 应用更改。

1. 使用这些示例[模板](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json)和[参数](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json)文件部署包含两种节点类型和两个规模集（每种节点类型一个规模集）的初始群集。  这两个规模集的大小均为标准 D2_V2，并且都运行 Windows Server 2012 R2 Datacenter。  等待群集完成基线升级。   
2. 可选：向群集部署有状态示例。
3. 在决定升级主节点类型 VM 以后，使用这些示例[模板](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json)和[参数](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json)文件向主节点类型添加新的规模集，这样一来，主节点类型现在就有两个规模集。  系统服务和用户应用程序能够在两个不同规模集中的 VM 之间迁移。  新规模集 VM 的大小为标准 D4_V2，并运行带容器的 Windows Server 2016 Datacenter。  添加新的规模集时也会添加新的负载均衡器和公共 IP 地址。  
    若要在模板中查找新的规模集，请搜索由 *vmNodeType2Name* 参数命名的“Microsoft.Compute/virtualMachineScaleSets”资源。  系统使用 properties->virtualMachineProfile->extensionProfile->extensions->properties->settings->nodeTypeRef 设置将新的规模集添加到主节点类型中。
4. 检查群集运行状况并验证所有节点是否都处于正常状态。
5. 禁用主节点类型的旧规模集中的节点，以便删除节点。 可以一次禁用所有节点，并且这些操作会排入队列。 等到所有节点都被禁用，这可能需要一些时间。  由于禁用了节点类型中较旧的节点，因此，系统服务和种子节点会迁移到主节点类型中新规模集的 VM。
6. 从主节点类型中删除较旧的规模集。 （如果在步骤 5 中禁用了节点，则在 Azure 门户的虚拟机规模集边栏选项卡中，逐一从旧节点类型取消分配节点。）
7. 删除与旧规模集关联的负载均衡器。 在为新规模集配置新的公共 IP 地址和负载均衡器时，群集不可用。  
8. 将与旧的主节点类型规模集关联的公共 IP 地址的 DNS 设置存储在变量中，并删除该公共 IP 地址。
9. 将与新的主节点类型规模集关联的公共 IP 地址的 DNS 设置替换为已删除的公共 IP 地址的 DNS 设置。  现在可以再次访问群集。
10. 从群集中删除节点的节点状态。  如果旧规模集的持续性级别为银级或金级，则此步骤由系统自动完成。
11. 如果在之前的步骤中部署了有状态的应用程序，请验证该应用程序能否正常运行。

## <a name="set-up-the-test-cluster"></a>设置测试群集

首先下载本教程需要的两组文件：之前的[模板]()和[参数]()以及之后的[模板]()和[参数]()。

接下来，登录 Azure 帐户。

```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```

本教程将指导你完成创建自签名证书的方案。 要使用 Azure 密钥保管库中的现有证书，请跳过下面的步骤，改为按照[使用现有证书部署群集](https://docs.microsoft.com/azure/service-fabric/upgrade-managed-disks#use-an-existing-certificate-to-deploy-the-cluster)中的步骤进行操作。

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>生成自签名证书并部署群集

首先，为 Service Fabric 群集部署分配所需的变量。 针对特定帐户和环境调整 `resourceGroupName`、`certSubjectName`、`parameterFilePath`和 `templateFilePath` 的值：

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.parameters.json"
```

> [!NOTE]
> 请确保在运行命令之前，`certOutputFolder` 位置存在于本地计算机上，以便部署新的 Service Fabric 群集。

接下来，打开 Deploy-2NodeTypes-2ScaleSets.parameters.json 文件，并调整 `clusterName` 和 `dnsName` 的值，使其与 PowerShell 中设置的动态值相对应，并保存所做的更改。

然后，部署 Service Fabric 测试群集：

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

部署完成后，在本地计算机上找到 .pfx 文件 (`$certPfx`)，然后将其导入证书存储：

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

该操作将返回证书指纹，你将使用该指纹连接到新群集并检查其运行状况。

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>连接到新群集并检查运行状况

连接到群集，并确保其所有节点都正常运行（替换群集的 `clusterName` 和 `thumb` 变量）：

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

我们已准备好开始升级过程。

## <a name="upgrade-the-primary-node-type-vms"></a>升级主节点类型 VM

在决定升级主节点类型 VM 以后，向主节点类型添加新的规模集，确保主节点类型现在有两个规模集。 提供了示例[模板](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json)和[参数](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json)文件来显示必要的更改。 新规模集的 VM 大小为 Standard D4_V2，并运行包含容器的 Windows Server 2016 Datacenter。 添加新的规模集时也会添加新的负载均衡器和公共 IP 地址。 

要在模板中查找新规模集，请搜索以 vmNodeType2Name 参数命名的“Microsoft.Compute/virtualMachineScaleSets”资源。 系统使用 properties->virtualMachineProfile->extensionProfile->extensions->properties->settings->nodeTypeRef 设置将新的规模集添加到主节点类型中。

### <a name="deploy-the-updated-template"></a>部署已更新的模板

根据需要调整 `parameterFilePath` 和 `templateFilePath`，然后运行以下命令：

```powershell
# Deploy the new scale set into the primary node type along with a new load balancer and public IP
$templateFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

部署完成后，再次检查群集运行状况，并确保原始和新规模集上的所有节点都正常运行。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-nodes-to-the-new-scale-set"></a>将节点迁移到新规模集

现在，我们准备开始禁用原始规模集的节点。 禁用这些节点后，系统服务和种子节点将迁移到新规模集的 VM，因为新规模集也被标记为主节点类型。

对于向上缩放非主节点类型，在此步骤中，你将修改服务放置约束以包括新的虚拟机规模集/节点类型，然后将旧的虚拟机规模集实例计数减少到零，一次一个节点（以确保删除节点不会影响群集的可靠性）。

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

使用 Service Fabric Explorer 监视种子节点到新规模集的迁移以及原始规模集中的节点从“正在禁用”到“已禁用”状态的进度 。

> [!NOTE]
> 在原始规模集的所有节点上完成禁用操作可能需要一些时间。 为了保证数据一致性，一次只能更改一个种子节点。 每次更改种子节点都需要更新群集，因此，替换种子节点需要升级两次群集（一次是在添加节点时，一次是在删除节点时）。 本示例方案中的升级 5 个种子节点将需要升级 10 次群集。

## <a name="remove-the-original-scale-set"></a>删除原始规模集

禁用操作完成后，删除规模集。

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

在 Service Fabric Explorer 中，已删除的节点（以及群集运行状况）现在将显示为“错误”状态 。

## <a name="remove-the-old-load-balancer-and-update-dns-settings"></a>删除旧的负载均衡器并更新 DNS 设置

现在，可以从负载均衡器和旧的公共 IP 开始，删除与旧的主节点类型相关的资源。 

```powershell
$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

接下来，我们更新新公共 IP 的 DNS 设置，以反映旧主节点类型公共 IP 中的设置。

```powershell
# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

再次查看群集运行状况

```powershell
# Check the cluster health
Get-ServiceFabricClusterHealth
```

最后，删除每个相关节点的节点状态。 如果旧规模集的持续性级别为银级或金级，则将自动删除。

```powershell
# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

群集的主节点类型现已升级。 验证已部署的所有应用程序是否正常运行以及群集运行状况是否正常。

## <a name="next-steps"></a>后续步骤
* 了解如何[向群集添加节点类型](virtual-machine-scale-set-scale-node-type-scale-out.md)
* 了解[应用程序可伸缩性](service-fabric-concepts-scalability.md)。
* [横向扩展或缩减 Azure 群集](service-fabric-tutorial-scale-cluster.md)
* 使用 fluent Azure 计算 SDK [以编程方式缩放 Azure 群集](service-fabric-cluster-programmatic-scaling.md)。
* [横向扩展或缩减独立群集](service-fabric-cluster-windows-server-add-remove-nodes.md)。

