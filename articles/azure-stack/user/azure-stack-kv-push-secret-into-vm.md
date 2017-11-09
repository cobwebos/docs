---
title: "部署虚拟机与 Azure 堆栈上的安全地存储证书 |Microsoft 文档"
description: "了解如何部署虚拟机和通过 Azure 堆栈中使用密钥保管库推送到它上面的证书"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/03/2017
ms.author: sngun
ms.openlocfilehash: 29ccdc9eca9911b2f550f9e09da83d0b1d30f9db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-and-include-certificate-retrieved-from-a-key-vault"></a>创建虚拟机并将包含从密钥保管库中检索到的证书

本文可帮助你在 Azure 堆栈和放到它上面的推送证书中创建虚拟机。 

## <a name="prerequisites"></a>必备组件

* 你必须必须订阅服务关联，它包含密钥保管库服务。 
* [安装适用于 Azure 堆栈 PowerShell。](azure-stack-powershell-install.md)  
* [配置 Azure 堆栈用户 PowerShell 环境](azure-stack-powershell-configure-user.md)

Azure 堆栈中的密钥保管库用于存储证书。 证书所多种不同方案中很有用。 例如，假设必须用正在运行的应用程序需要证书的 Azure 堆栈中虚拟机。 此证书可用于加密，对 Active Directory 进行身份验证或 SSL 的网站上。 具有密钥保管库可帮助中的证书，请确保它是安全的。

在本文中，我们引导你完成 Azure 堆栈中的推送到 Windows 虚拟机上的证书所需的步骤。 如果你通过 VPN 连接，你可以使用以下步骤从 Azure 堆栈开发工具包中，或者从基于 Windows 的外部客户端。

以下步骤说明将推送到虚拟机的证书所需的过程：

1. 创建密钥保管库密钥。
2. 更新 azuredeploy.parameters.json 文件。
3. 部署模板

## <a name="create-a-key-vault-secret"></a>创建密钥保管库密钥

以下脚本以.pfx 格式创建一个证书，创建密钥保管库，并将该证书存储中作为机密密钥保管库。 必须使用`-EnabledForDeployment`参数在创建密钥保管库时。 此参数可确保密钥保管库可以从 Azure 资源管理器模板来引用。

```powershell

# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret

```

运行前面的脚本时，输出中包括的机密的 URI。 记下此 URI。 你必须引用在[到 Windows 资源管理器模板的推送证书](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate)。 下载[vm 推送证书 windows 模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate)到开发计算机上的文件夹。 此文件夹包含`azuredeploy.json`和`azuredeploy.parameters.json`文件，你将需要在下面的步骤。

修改`azuredeploy.parameters.json`根据你环境的值的文件。 参数感兴趣的是保管库名称、 保管库资源组和机密 URI （如由前面的脚本生成）。 下面的文件是参数文件的示例：

## <a name="update-the-azuredeployparametersjson-file"></a>更新 azuredeploy.parameters.json 文件

使用 vaultName、 机密的 URI、 VmName 和根据你的环境的其他值更新 azuredeploy.parameters.json 文件。 下面的 JSON 文件显示模板参数文件的示例： 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>部署模板

现在使用以下 PowerShell 脚本部署模板：

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

已成功部署模板时，这会导致下面的输出：

![部署输出](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

部署此虚拟机后，Azure 堆栈推送到虚拟机的证书。 在 Windows 中，该证书添加到具有用户提供的证书存储的 LocalMachine 证书位置。 在 Linux，证书位于 /var/lib/waagent 中获得目录下，用文件名称&lt;UppercaseThumbprint&gt;.crt 的 x509 证书文件和&lt;UppercaseThumbprint&gt;.prv 对该私钥。

## <a name="retire-certificates"></a>停用证书

在前面的部分中，我们向您展示如何将推送到虚拟机上的新证书。 旧证书仍位于虚拟机，并且无法删除。 但是，你可以禁用通过使用较旧版本的机密`Set-AzureKeyVaultSecretAttribute`cmdlet。 下面是此 cmdlet 的用法示例。 请确保将保管库名称、 机密名称和根据你的环境的版本值：

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>后续步骤

* [使用密钥保管库密码部署 VM](azure-stack-kv-deploy-vm-with-secret.md)
* [允许应用程序访问密钥保管库](azure-stack-kv-sample-app.md)


