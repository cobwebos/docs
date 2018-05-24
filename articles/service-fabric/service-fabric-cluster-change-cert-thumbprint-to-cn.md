---
title: 更新 Azure Service Fabric 群集以使用证书公用名称 | Microsoft Docs
description: 了解如何将 Service Fabric 群集从使用证书指纹切换为使用证书公用名称。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: 39dc5800edd743cdc950c7a96f7633fb4c0a7c45
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>将群集从证书指纹更改为公用名称
两个证书不能具有相同的指纹，具有相同的指纹会使群集证书滚动更新或管理变得困难。 但是，多个证书可以具有相同的公用名称或使用者。  将已部署的群集从使用证书指纹切换为使用证书公用名称会使证书管理更加简单。 本文介绍了如何将正在运行的 Service Fabric 群集更新为使用证书公用名称而非证书指纹。
 
## <a name="get-a-certificate"></a>获取证书
首先，从[证书颁发机构 (CA)](https://wikipedia.org/wiki/Certificate_authority) 获取证书。  证书的公用名称应当是群集的主机名。  例如，“myclustername.southcentralus.cloudapp.azure.com”。  

对于测试用途，可以从免费或开放的证书颁发机构获取由 CA 签名的证书。

> [!NOTE]
> 不支持自签名证书，包括在 Azure 门户中部署 Service Fabric 群集时生成的证书。

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>上传证书并将其安装在规模集中
在 Azure 中，Service Fabric 群集部署在虚拟机规模集上。  将证书上传到密钥保管库，然后将其安装在运行群集的虚拟机规模集上。

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgropu"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzureRmVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

## <a name="download-and-update-the-template-from-the-portal"></a>从门户中下载并更新模板
证书已安装在基础规模集上，但还需要将 Service Fabric 群集更新为使用该证书及其公用名称。  现在，为群集部署下载模板。  登录到 [Azure 门户](https://portal.azure.com)并导航到承载着群集的资源组。  在“设置”中，选择“部署”。  选择最新部署并单击“查看模板”。

![查看模板][image1]

将模板和参数 JSON 文件下载到本地计算机。

首先，在文本编辑器中打开参数文件并添加以下参数值：
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

接下来，在文本编辑器中打开模板文件并进行三项更新以支持证书公用名称。

1. 在 **parameters** 部分中，添加 *certificateCommonName* 参数：
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    ```

    另请考虑删除 *certificateThumbprint*，可能不再需要此项。

2. 在 **Microsoft.Compute/virtualMachineScaleSets** 资源中，更新虚拟机扩展以在证书设置中使用公用名称而非指纹。  在 **virtualMachineProfile**->**extenstionProfile**->**extensions**->**properties**->**settings**->**certificate** 中，添加 `"commonNames": ["[parameters('certificateCommonName')]"],` 并删除 `"thumbprint": "[parameters('certificateThumbprint')]",`。
    ```json
    "virtualMachineProfile": {
              "extensionProfile": {
                "extensions": [
                  {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": true,
                      "protectedSettings": {
                        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                        "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                      },
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[variables('vmNodeType0Name')]",
                        "dataPath": "D:\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "enableParallelJobs": true,
                        "nicPrefixOverride": "[variables('subnet0Prefix')]",
                        "certificate": {
                          "commonNames": ["[parameters('certificateCommonName')]"],                          
                          "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
                      "typeHandlerVersion": "1.0"
                    }
                  },
    ```

3.  在 **Microsoft.ServiceFabric/clusters** 资源中，将 API 版本更新为“2018-02-01”。  另请添加包含 **commonNames** 属性的 **certificateCommonNames** 设置，并删除 **certificate** 设置（包含指纹属性），如以下示例中所示：
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
        "addonFeatures": [
            "DnsService",
            "RepairManager"
        ],        
        "certificateCommonNames": {
            "commonNames": [
            {
                "certificateCommonName": "[parameters('certificateCommonName')]",
                "certificateIssuerThumbprint": ""
            }
            ],
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
        ...
    ```

## <a name="deploy-the-updated-template"></a>部署已更新的模板
在进行更改后，重新部署已更新的模板。

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"

New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" -Verbose
```

## <a name="next-steps"></a>后续步骤
* 了解[群集安全性](service-fabric-cluster-security.md)。
* 了解如何[滚动更新群集证书](service-fabric-cluster-rollover-cert-cn.md)
* [更新和管理群集证书](service-fabric-cluster-security-update-certs-azure.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png