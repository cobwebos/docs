---
title: 创建使用证书公用名称的 Azure Service Fabric 群集 | Microsoft Docs
description: 了解如何基于模板创建使用证书公用名称的 Service Fabric 群集。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: aljo
ms.openlocfilehash: bf28ddf7facbc742a107f67f3d7e81eca5a5c950
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59045382"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>部署使用证书公用名称而非指纹的 Service Fabric 群集
两个证书不能具有相同的指纹，具有相同的指纹会使群集证书滚动更新或管理变得困难。 但是，多个证书可以具有相同的公用名称或使用者。  使用证书公用名称会使群集的证书管理更加简单。 本文介绍了如何部署 Service Fabric 群集来使用证书公用名称而非证书指纹。
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>获取证书
首先，从[证书颁发机构 (CA)](https://wikipedia.org/wiki/Certificate_authority) 获取证书。  证书的公用名称应该是你拥有的自定义域，并从域注册机构购买。 例如，“azureservicefabricbestpractices.com”；非 Microsoft 员工无法为 MS 域预配证书，因此不能将 LB 或流量管理器的 DNS 名称用作证书的公用名称，如果要使自定义域可在 Azure 中解析，则需要预配 [Azure DNS 区域](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)。 如果希望门户反映群集的自定义域别名，还需要将自己的自定义域声明为群集的“managementEndpoint”。

对于测试用途，可以从免费或开放的证书颁发机构获取由 CA 签名的证书。

> [!NOTE]
> 不支持自签名证书，包括在 Azure 门户中部署 Service Fabric 群集时生成的证书。 

## <a name="upload-the-certificate-to-a-key-vault"></a>将证书上传到密钥保管库中
在 Azure 中，Service Fabric 群集部署在虚拟机规模集上。  将证书上传到密钥保管库中。  在群集部署时，证书将安装在运行群集的虚拟机规模集上。

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
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
```

## <a name="download-and-update-a-sample-template"></a>下载并更新示例模板
本文使用了 [5 节点安全群集示例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure)模板和模板参数。 将 *azuredeploy.json* 和 *azuredeploy.parameters.json* 文件下载到计算机。

### <a name="update-parameters-file"></a>更新参数文件
首先，在文本编辑器中打开 *azuredeploy.parameters.json* 文件并添加以下参数值：
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

接下来，将 *certificateCommonName*、*sourceVaultValue* 和 *certificateUrlValue* 参数值设置为前面的脚本返回的值：
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>更新模板文件
接下来，在文本编辑器中打开 *azuredeploy.json* 文件并进行三项更新以支持证书公用名称。

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

2. 将 *sfrpApiVersion* 变量的值设置为“2018-02-01”：
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. 在 **Microsoft.Compute/virtualMachineScaleSets** 资源中，更新虚拟机扩展以在证书设置中使用公用名称而非指纹。  在“virtualMachineProfile”->“extensionProfile”->“扩展”->“属性”->“设置”->“证书”中，添加 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    并删除 `"thumbprint": "[parameters('certificateThumbprint')]",`。

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
                  "commonNames": [
                     "[parameters('certificateCommonName')]"
                  ],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4. 在 **Microsoft.ServiceFabric/clusters** 资源中，将 API 版本更新为“2018-02-01”。  另请添加包含 **commonNames** 属性的 **certificateCommonNames** 设置，并删除 **certificate** 设置（包含指纹属性），如以下示例中所示：
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
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > 可以使用“certificateIssuerThumbprint”字段通过给定的使用者公用名指定证书的预期颁发者。 此字段接受以逗号分隔的 SHA1 指纹枚举。 请注意，这是对证书验证的加强 - 在未指定颁发者或颁发者为空的情况下，如果可以构建证书的链并最终得到验证程序信任的根证书，则会接受该证书以用于身份验证。 当指定了颁发者时，如果直接颁发者的指纹与此字段中指定的任意值匹配，则无论根证书是否受信任，都会接受该证书。 请注意，PKI 可以使用不同的证书颁发机构来为同一使用者颁发证书，因此，为给定的使用者指定所有预期的颁发者指纹非常重要。
   >
   > 指定颁发者被认为是最佳做法，但对于可以链接成受信任的根证书的证书，省略颁发者也是可行的，此行为存在限制，在不久的将来可能会被淘汰。 另请注意，如果在 Azure 中部署的群集受由某个专用 PKI 颁发且通过使用者声明的 X509 证书保护，并且该 PKI 的证书策略不可发现、不可用且无法访问，则 Azure Service Fabric 服务可能无法对群集进行验证（对于群集到服务通信）。 

## <a name="deploy-the-updated-template"></a>部署已更新的模板
在进行更改后，重新部署已更新的模板。

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzResourceGroup -Name $groupname -Location $clusterloc

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>后续步骤
* 了解[群集安全性](service-fabric-cluster-security.md)。
* 了解如何[滚动更新群集证书](service-fabric-cluster-rollover-cert-cn.md)
* [更新和管理群集证书](service-fabric-cluster-security-update-certs-azure.md)
* 通过[将群集从证书指纹更改为通用名称](service-fabric-cluster-change-cert-thumbprint-to-cn.md)来简化证书管理

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
ic-cluster-change-cert-thumbprint-to-cn.md))

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
