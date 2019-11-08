---
title: 创建 Azure Key Vault 证书 |Azure Marketplace
description: 介绍如何从 Azure 部署的 VHD 注册 VM。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 4adc6f716050e2d792e0a5c022972e4340d2846a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823111"
---
# <a name="create-certificates-for-azure-key-vault"></a>创建 Azure Key Vault 证书

本文介绍如何预配自签名证书，建立 Windows 远程管理 (WinRM) 到 Azure 托管虚拟机 (VM) 的连接需要使用这些证书。 此过程包括三个步骤：

1.  创建安全证书。 
2.  创建 Azure Key Vault 来存储此证书。 
3.  将证书存储到此 Key Vault 中。 

为完成此工作，可以使用新的或现有的 Azure 资源组。  以下说明使用了前一种方法。



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>创建证书

编辑并运行以下 Azure Powershell 脚本，以在本地文件夹中创建证书文件 (.pfx)。  需要替换以下参数的值：

|  **Parameter**        |   **说明**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | 用于保存 .pfx 文件的本地文件夹  |
| `$location`    | Azure 标准地理位置之一  |
| `$vmName`      | 计划内 Azure 虚拟机的名称   |
| `$certname`    | 证书名称；必须与计划内 VM 的完全限定的域名匹配  |
| `$certpassword` | 证书的密码必须与计划内 VM 使用的密码匹配  |
|  |  |

```powershell
    # Certification creation script 
    
    # pfx certification stored path
    $certroopath = "C:\certLocation"
    
    #location of the resource group
    $location = "westus"
    
    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"
    
    # Certification name - should match with FQDN of Windows Azure creating VM 
    $certname = "$vmName.$location.cloudapp.azure.com"
    
    # Certification password - should be match with password of Windows Azure creating VM 
    $certpassword = "SecretPassword@123"
    
    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb 

```
> [!TIP]
> 在这些步骤中保持相同的 PowerShell 控制台会话处于活动状态，以便保留各种参数的值。

> [!WARNING]
> 如果保存此脚本，请仅将其存储在安全位置，因为它包含安全信息（密码）。


## <a name="create-the-key-vault"></a>创建 Key Vault

将 [Key Vault 部署模板](./cpp-key-vault-deploy-template.md)的内容复制到本地计算机上的文件中。 （在下面的示例脚本中，此资源 `C:\certLocation\keyvault.json`。） 编辑并运行以下 Azure Powershell 脚本以创建 Azure Key Vault 实例和关联的资源组。  需要替换以下参数的值：

|  **Parameter**        |   **说明**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | 附加到部署标识符的任意数字字符串                     |
| `$rgName`             | 要创建的 Azure 资源组 (RG) 名称                                        |
|  `$location`          | Azure 标准地理位置之一                                  |
| `$kvTemplateJson`     | 包含 Key Vault 的资源管理器模板的文件路径 (keyvault.json) |
| `$kvname`             | 新的 Key Vault 的名称                                                       |
|  |  |

```powershell
    # Creating Key vault in resource group
    
    # "Random" number for deployment identifiers
    $postfix = "0101048"
    
    # Resource group name
    $rgName = "TestRG$postfix"
    
    # Location of Resource Group
    $location = "westus"
    
    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"
    
    # Key vault name
    $kvname = "isvkv$postfix"
    
    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host 
        
        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }
        
        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Id
                              
        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
      }
      catch
      {
      Write-Host $_.Exception.Message    
      Break
      } 

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host
    
        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message    
        Break
        }
    
    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------" 
    
    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId
    
    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all 
        
```

## <a name="store-the-certificate"></a>存储证书

现在，可通过运行以下脚本将 .pfx 文件中包含的证书存储到新的 Key Vault 中。 

```powershell
    #push certificate to key vault secret

     $fileName =$certroopath+"\$certname"+".pfx" 
      
     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    
            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
"@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>后续步骤

接下来，[从用户 VM 映像部署 VM](./cpp-deploy-vm-user-image.md)。
