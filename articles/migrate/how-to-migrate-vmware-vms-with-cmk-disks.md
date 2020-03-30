---
title: 使用服务器端加密 （SSE） 和客户管理的密钥 （CMK） 使用 Azure 迁移服务器迁移将 VMware 虚拟机迁移到 Azure
description: 了解如何使用服务器端加密 （SSE） 和客户管理的密钥 （CMK） 使用 Azure 迁移服务器迁移将 VMware VM 迁移到 Azure
author: bsiva
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 03/12/2020
ms.author: raynew
ms.openlocfilehash: c6b791fda43a018a26204b2b43dc1e581ff3a945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269479"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>使用服务器端加密和客户管理的密钥将 VMware VM 迁移到启用的 Azure VM

本文介绍如何使用 Azure 迁移服务器迁移（无代理复制）将 VMware VM 迁移到使用服务器端加密 （SSE） 使用客户托管密钥 （CMK） 加密磁盘的 Azure 虚拟机。

Azure 迁移服务器迁移门户体验允许您[使用无代理复制将 VMware VM 迁移到 Azure。](tutorial-migrate-vmware.md) 门户体验当前无法提供在 Azure 中为复制磁盘打开带有 CMK 的 SSE 的能力。 目前只能通过 REST API 使用 CMK 打开 SSE 以用于复制磁盘。 在本文中，您将了解如何创建和部署[Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)以复制 VMware VM，并将 Azure 中复制的磁盘配置为将 SSE 与 CMK 一起使用。

本文中的示例使用[Azure PowerShell](/powershell/azure/new-azureps-module-az)执行创建和部署资源管理器模板所需的任务。

[详细了解](../virtual-machines/windows/disk-encryption.md)使用托管磁盘的客户托管密钥 （CMK） 的服务器端加密 （SSE）。

## <a name="prerequisites"></a>先决条件

- [查看有关](tutorial-migrate-vmware.md)使用无代理复制将 VMware VM 迁移到 Azure 的教程，以了解工具要求。
- [按照这些说明](how-to-add-tool-first-time.md)创建 Azure 迁移项目，并将**Azure 迁移：服务器迁移**工具添加到项目。
- [请按照这些说明](how-to-set-up-appliance-vmware.md)在本地环境中为 VMware 设置 Azure 迁移设备并完成发现。

## <a name="prepare-for-replication"></a>为复制做准备

VM 发现完成后，"服务器迁移"磁贴上的"发现服务器"行将显示设备发现的 VMware VM 计数。

在开始复制 VM 之前，需要准备复制基础结构。

1. 在目标区域中创建服务总线实例。 本地 Azure 迁移设备使用服务总线与服务器迁移服务通信以协调复制和迁移。
2. 创建存储帐户，用于从复制传输操作日志。
3. 创建 Azure 迁移设备将复制数据上载到的存储帐户。
4. 创建密钥保管库并配置密钥保管库，以管理步骤 3 和 4 中创建的存储帐户上的 Blob 访问的共享访问签名令牌。
5. 为步骤 1 中创建的服务总线生成共享访问签名令牌，并为在上一步中创建的密钥保管库中的令牌创建机密。
6. 创建密钥保管库访问策略，使本地 Azure 迁移设备（使用设备 AAD 应用）和服务器迁移服务对密钥保管库的访问。
7. 创建复制策略，并将服务器迁移服务配置为服务器迁移服务，并包含上一步骤中创建的复制基础结构的详细信息。

复制基础结构必须在迁移的目标 Azure 区域中创建，并且必须在要迁移到 VM 的目标 Azure 订阅中创建。

服务器迁移门户体验通过在项目中首次复制 VM 时自动为您执行此操作，从而简化了复制基础结构的准备。 在本文中，我们将假定您已经使用门户体验复制了一个或多个 VM，并且复制基础结构已经创建。 我们将了解如何发现现有复制基础结构的详细信息，以及如何将这些详细信息用作资源管理器模板的输入，该模板将用于使用 CMK 设置复制。

### <a name="identifying-replication-infrastructure-components"></a>标识复制基础结构组件

1. 在 Azure 门户上，转到资源组页面并选择在其中创建 Azure 迁移项目的资源组。
2. 从左侧菜单**中选择"部署"，** 然后搜索以字符串 *"Microsoft.MigrateV2.VMwareV2EnableMigrate"* 开头的部署名称。 您将看到门户体验为在此项目中为 VM 设置复制而创建的资源管理器模板的列表。 我们将下载一个这样的模板，并将其用作准备使用 CMK 复制的模板的基础。
3. 要下载模板，请选择与上一步中字符串模式匹配的任何部署>从左侧菜单中选择 **"模板**>从顶部菜单单击 **"下载**"。 在本地保存模板.json 文件。 您将在最后一步中编辑此模板文件。

## <a name="create-a-disk-encryption-set"></a>创建磁盘加密集

磁盘加密集对象将托管磁盘映射到密钥保管库，该密钥保管库包含要用于 SSE 的 CMK。 要使用 CMK 复制 VM，您将创建一个磁盘加密集，并将其作为输入传递给复制操作。

请[按照此处](../virtual-machines/windows/disk-encryption.md#powershell)的示例创建使用 Azure PowerShell 的磁盘加密集。 确保磁盘加密集是在要迁移到 VM 的目标订阅中创建的，以及迁移的目标 Azure 区域中创建的。

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>获取要迁移的 VMware VM 的详细信息

在此步骤中，您将使用 Azure PowerShell 获取需要迁移的 VM 的详细信息。 这些详细信息将用于构造用于复制的资源管理器模板。 具体来说，感兴趣的两个属性是：

- 已发现的 VM 的计算机资源 ID。
- VM 的磁盘列表及其磁盘标识符。

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

复制与发现 VM 的 Azure 迁移设备对应的 SiteId 字符串的值。 在上面的示例中，SiteId 是 *"/订阅/509099b2-9d2c-4636-b43e-bd5cafb6be69/资源组/ContosoVMwareCMK/提供商/微软。OffAzure/VMwareSites/VMwaresites/VMwareasites/VMwareasites/VMwareasites/VMwareaca8basite"*

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

复制要迁移的计算机的资源 Id、名称和磁盘 uuid 值。
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>创建用于复制的资源管理器模板

- 在您选择的编辑器中打开您在 **"识别复制基础结构组件**"步骤中下载的资源管理器模板文件。
- 从模板中删除所有资源定义，但类型为 *"Microsoft.恢复服务/保管库/复制结构/复制保护容器/复制迁移项目"* 的资源除外
- 如果上述类型的多个资源定义，请删除除一个之外的所有资源定义。 从资源定义中删除任何**依赖On**属性定义。
- 在此步骤的末尾，应该有一个文件，它类似于下面的示例，并且具有相同的属性集。

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- 编辑资源定义中**的名称**属性。 将名称属性中最后一个 "/"后面的字符串替换为 *$machine的值。名称*（从上一步）。
- 更改**属性的值.提供程序特定详细信息.vmwareMachineId**属性的值为 *$machine。资源 Id（* 从上一步开始）。
- 分别为目标**资源**组**ID、****目标子网名称、目标子网名称**、目标虚拟网络资源 ID 和目标子网名称设置目标资源组 ID 的值。
- 将**许可证类型**的值设置为"WindowsServer"，以便为此 VM 应用 Azure 混合权益。 如果此 VM 不符合 Azure 混合权益条件，则将**许可证类型**的值设置为"无许可证类型"。
- 将**目标 VmName**属性的值更改为迁移 VM 所需的 Azure 虚拟机名称。
- 可以选择在**目标 VmName**属性下方添加名为**targetVmSize**的属性。 将**目标 VmSize**属性的值设置为迁移 VM 所需的 Azure 虚拟机大小。
- **磁盘到Include**属性是用于复制的磁盘输入的列表，每个列表项代表一个本地磁盘。 创建与本地 VM 上的磁盘数一样多的列表项。 将列表项中的**diskId**属性替换为上一步骤中标识的磁盘的 uuid。 将**isOSDisk**值设置为 VM 的 OS 磁盘的"true"，将所有其他磁盘的"false"设置为"false"。 保持**日志存储帐户 Id**和**日志存储帐户Sas秘密名称**属性不变。 将**diskType**值设置为 Azure 托管磁盘类型 *（Standard_LRS、Premium_LRS、StandardSSD_LRS*）以用于磁盘。 对于需要使用 CMK 加密的磁盘，添加名为**disk，Setid**的属性，并将该值设置为创建的磁盘加密集的资源**ID（$des。id**） 在*创建磁盘加密集*步骤中
- 保存已编辑的模板文件。 对于上面的示例，编辑的模板文件如下所示：

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>设置复制

现在，您可以将编辑的资源管理器模板部署到项目资源组，以设置 VM 的复制。 了解如何使用[Azure 资源管理器模板和 Azure PowerShell 部署资源](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>后续步骤

通过门户体验[监视复制](tutorial-migrate-vmware.md#track-and-monitor)状态，并执行测试迁移和迁移。
