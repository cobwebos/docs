---
title: 测试从 VHD 部署的虚拟机（VM）-Azure Marketplace
description: 了解如何在商业市场中测试和提交虚拟机产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: 3d4ec077ac0e92d26cf82ba96593a76a21ed885f
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324669"
---
# <a name="test-virtual-machine-vm-deployed-from-vhd"></a>测试从 VHD 部署的虚拟机（VM）

本文介绍如何从上一节（[创建 AZURE VM 技术资产）](create-azure-vm-technical-asset.md)中创建的通用 VHD 映像部署和测试 Azure 虚拟机（VM），以确保 VHD 映像满足 Azure Marketplace 发布要求。

完成这些步骤以生成兼容性报告，该报告可证明你的 VHD 映像可在 Azure Marketplace 上使用。

1. 创建和部署远程 VM 管理所需的证书以 Azure Key Vault。
2. 从[创建 AZURE vm 技术资产](create-azure-vm-technical-asset.md)中创建的通用 VHD 映像部署 Azure VM。
3. 在部署的 VM 上运行测试，以确保 VHD 映像已准备就绪，可供发布并用于部署 Vm。

## <a name="running-scripts"></a>运行脚本

本文包含三个要在 PowerShell 中运行的脚本。 桌面 PowerShell 效果最好，但也可将 Azure Cloud Shell 用于选定的 PowerShell 选项（窗口的左上角）。

1. 确保将 PowerShell 配置为运行脚本。

    - 始终以 "以**管理员身份运行**" 选项打开 PowerShell。
    - 确保可以运行以下脚本： `Set-ExecutionPolicy` 和 `RemoteSigned` 。

2. [安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

3. 安装 Azure PowerShell Az Module。
    1. **选项 A**：尚未安装任何模块。
        - `Install-Module -Name Az -AllowClobber -Scope AllUsers`

        有关详细信息，请参阅[Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-4.2.0)。

    2. **选项 B**：目前正在使用 AzureRM 模块。

        - 卸载-AzureRM
        - 安装模块名称 Az-AllowClobber
        - AzureRmAlias-Scope CurrentUser

        有关详细信息，请参阅[将 Azure PowerShell 从 AzureRM 迁移到 Az](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-4.2.0)。

4. 保存会话参数。

本部分中的脚本使用会话变量/参数。 如果关闭会话，则将清除参数。 建议使用一个会话来运行所有脚本，以避免参数值错误。 如果无法做到这一点，则必须在打开新会话时重新初始化参数，特别是对于后面的脚本。

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>创建和部署 Azure Key Vault 证书

本部分介绍如何创建和部署自签名证书，设置 Windows 远程管理 (WinRM) 到 Azure 托管虚拟机的连接需要使用这些证书。

### <a name="create-certificates-for-azure-key-vault"></a>创建 Azure Key Vault 证书

此过程包括三个步骤：

1. 创建安全证书。
2. 创建 Azure Key Vault 来存储证书。
3. 将证书存储到密钥保管库中。

为完成此工作，可以使用新的或现有的 Azure 资源组。

#### <a name="create-the-security-certificate"></a>创建安全证书

运行此脚本以在本地文件夹中创建证书文件（.pfx）。 此证书属于将从 VHD 映像部署的计划的 Azure VM。 VM 需要由脚本参数指定的名称、位置和密码。 编辑以下 Azure PowerShell**证书创建脚本**，为表中所示的脚本参数指定正确的值。

| **参数** | **说明** |
| --- | --- |
| $certroopath | 用于保存 .pfx 文件的本地文件夹。 |
| $location | Azure 标准地理位置之一。 |
| $vmName | 计划内 Azure 虚拟机的名称。 |
| $certname | 证书名称；必须与计划内 VM 的完全限定的域名匹配。 |
| $certpassword | 证书的密码，必须与计划内 VM 使用的密码匹配。 |
| | |

```PowerShell
   # Certification creation script

    # pfx certification stored path
    $certroopath = "C:\certLocation"

    # location of the resource group
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
> 在这些步骤中使相同的 PowerShell 控制台会话保持打开和运行状态，以保留各种参数的值。

> [!WARNING]
> 如果保存此脚本，请仅将其保存在安全位置，因为它包含安全信息（密码）。

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>创建 Azure 密钥保管库来存储证书

将以下模板的内容复制到本地计算机上的文件中。 在下面的示例脚本中，此资源为 `C:\certLocation\keyvault.json`。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "defaultValue":"isvkv0001",
      "metadata": {
        "description": "Name of the Vault"
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue":"72f988bf-86f1-41af-91ab-2d7cd011db47",
      "metadata": {
        "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
      }
    },
    "objectId": {
      "type": "string",
      "defaultValue":"d55739bf-d5d6-4ce0-be1c-49ade53c4315",
      "metadata": {
        "description": "Object Id of the AD user. Get using Get-AzureADUser or Get-AzureADServicePrincipal cmdlets"
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to secrets in the vault. Valid values are: all, get, set, list, and delete."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "SKU for the vault"
      }
    },
    "enableVaultForDeployment": {
      "type": "bool",
      "defaultValue": true,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies if the vault is enabled for a VM deployment"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "[parameters('keyVaultName')]",
      "apiVersion": "2015-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "tenantId": "[parameters('tenantId')]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        }
      }
    }
  ]
}

```

编辑并运行以下 Azure PowerShell 脚本，以创建 Azure Key Vault 和关联的资源组。 替换下表中显示的参数的值

| **Parameter** | **说明** |
| --- | --- |
| $postfix | 附加到部署标识符的随机数字字符串。 |
| $rgName | 要创建的 Azure 资源组 (RG) 名称。 |
| $location | Azure 标准地理位置之一。 |
| $kvTemplateJson | 包含密钥保管库的资源管理器模板的文件路径 (keyvault.json)。 |
| $kvname | 新密钥保管库的名称。|
|   |   |

```PowerShell
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
     az group create --name $rgName --location $location
     Write-Host "-----------------------------------"

    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId

    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys Decrypt,Encrypt,UnwrapKey,WrapKey,Verify,Sign,Get,List,Update,Create,Import,Delete,Backup,Restore,Recover,Purge -PermissionsToSecrets Get,List,Set,Delete,Backup,Restore,Recover,Purge

```

#### <a name="store-the-certificates-to-the-key-vault"></a>将证书存储到密钥保管库中

使用此脚本将 .pfx 文件中包含的证书存储到新的密钥保管库：

```PowerShell
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

## <a name="deploy-an-azure-vm-from-your-generalized-vhd-image"></a>从通用 VHD 映像部署 Azure VM

本部分介绍如何部署通用 VHD 映像，以创建新的 Azure VM 资源。 在此过程中，我们将使用提供的 Azure 资源管理器模板和 Azure PowerShell 脚本。

### <a name="prepare-an-azure-resource-manager-template"></a>准备 Azure 资源管理器模板

将以下 Azure 资源管理器模板之一（适用于 Windows 或 Linux）复制到名为 VHDtoImage.js的本地文件。 下一个脚本将请求本地计算机上的位置使用此 JSON。

#### <a name="for-windows-based-vms"></a>对于基于 Windows 的 Vm

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
        "variables": {
            "addressPrefix": "10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix": "10.0.0.0/24",
            "subnet2Prefix": "10.0.1.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('virtualNetworkName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnet1Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet1Prefix')]"
                            }
                        },
                        {
                            "name": "[variables('subnet2Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet2Prefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[parameters('nicName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                                },
                                "subnet": {
                                    "id": "[variables('subnet1Ref')]"
                                }
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[parameters('vmName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
                ],
                "properties": {
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[parameters('vmName')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
                    },
                    "storageProfile": {
                        "osDisk": {
                            "name": "[concat(parameters('vmName'),'-osDisk')]",
                            "osType": "[parameters('osType')]",
                            "caching": "ReadWrite",
                            "image": {
                                "uri": "[parameters('vhdUrl')]"
                            },
                            "vhd": {
                                "uri": "[variables('osDiskVhdName')]"
                            },
                            "createOption": "FromImage"
                        }
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                            }
                        ]
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

#### <a name="for-linux-based-vms"></a>对于基于 Linux 的 Vm

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "linux",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
        "variables": {
            "addressPrefix": "10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix": "10.0.0.0/24",
            "subnet2Prefix": "10.0.1.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('virtualNetworkName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnet1Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet1Prefix')]"
                            }
                        },
                        {
                            "name": "[variables('subnet2Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet2Prefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[parameters('nicName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                                },
                                "subnet": {
                                    "id": "[variables('subnet1Ref')]"
                                }
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[parameters('vmName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
                ],
                "properties": {
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[parameters('vmName')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
                    },
                    "storageProfile": {
                        "osDisk": {
                            "name": "[concat(parameters('vmName'),'-osDisk')]",
                            "osType": "[parameters('osType')]",
                            "caching": "ReadWrite",
                            "image": {
                                "uri": "[parameters('vhdUrl')]"
                            },
                            "vhd": {
                                "uri": "[variables('osDiskVhdName')]"
                            },
                            "createOption": "FromImage"
                        }
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                            }
                        ]
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": false,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

复制并编辑以下脚本，为这些参数提供值：

| **参数** | **说明** |
| --- | --- |
| ResourceGroupName | 现有 Azure 资源组名称。 通常，使用与密钥保管库相同的 RG。 |
| TemplateFile | 文件 VHDtoImage.json 的完整路径名。 |
| userStorageAccountName | 存储帐户的名称。 |
| sNameForPublicIP | 公共 IP 的 DNS 名称；必须为小写。 |
| subscriptionId | Azure 订阅标识符。 |
| 位置 | 资源组的标准 Azure 地理位置。 |
| vmName | 虚拟机名称。 |
| vaultName | 密钥保管库的名称。 |
| vaultResourceGroup | Key Vault 的资源组。 |
| certificateUrl | 证书的 Web 地址 (URL)，包括存储在密钥保管库中的版本，例如：`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`。 |
| vhdUrl | 虚拟硬盘的 Web 地址。 |
| vmSize | 虚拟机实例的大小。 |
| publicIPAddressName | 公共 IP 地址的名称。 |
| virtualNetworkName | 虚拟网络的名称。 |
| nicName | 虚拟网络的网络接口卡的名称。 |
| adminUserName | 管理员帐户的用户名。 |
| adminPassword | 管理员密码。 |
|   |   |

### <a name="deploy-an-azure-vm"></a>部署 Azure VM

复制并编辑以下脚本以提供 `$storageaccount` 和 `$vhdUrl` 变量的值。 执行它，从现有通用 VHD 创建 Azure VM 资源。

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL

$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

# Full pathname to the file VHDtoImage.json. inserted these highlighted lines
$templateFile = "$certroopath\VHDtoImage.json"

# Size of the virtual machine instance.
$vmSize = "Standard_D2s_v3"

# Name of the public IP address.
$publicIPAddressName = "myPublicIP1"

# Name of the virtual network
$virtualNetworkName = "myVNET1"

# Name of the network interface card for the virtual network
$nicName = "myNIC1"

# Username of the administrator account
$adminUserName = "isv"

# The OS of the virtual machine
$osType = "windows"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl “$objAzureKeyVaultSecret.Id” -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName"myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword “$pwd"

```

## <a name="run-tests-on-the-deployed-vm"></a>在部署的 VM 上运行测试

### <a name="download-and-run-the-certification-test-tool"></a>下载并运行认证测试工具

适用于 Azure 认证的认证测试工具是一种在本地 Windows 计算机上运行，但测试基于 Azure 的 Windows 或 Linux VM 的自我测试工具。 它可证明用户 VM 映像可与 Microsoft Azure 一起使用，并且已满足有关准备 VHD 的指南和要求。 此工具可确保 VM 随时可以按 Azure Marketplace 要求发布。 "

1. 下载并安装最新的[“Azure 认证”的认证测试工具](https://www.microsoft.com/download/details.aspx?id=44299)。
2. 打开认证工具，然后选择“启动新测试”。
3. 在“测试信息”屏幕中，为测试运行输入**测试名称**。
4. 为 VM 选择平台，可以是 Windows Server 或 Linux。 所选的平台会影响剩余的选项。
5. 如果 VM 使用此数据库服务，请选中“针对 Azure SQL 数据库进行测试”复选框。

### <a name="connect-the-certification-tool-to-a-vm-image"></a>将认证工具连接到 VM 映像

该工具通过 [Azure PowerShell](https://docs.microsoft.com/powershell/) 连接到基于 Windows 的 VM，通过 [SSH.Net](https://www.ssh.com/ssh/protocol/) 连接到 Linux VM。 选择以下两个选项之一，无论是 Linux 还是 Windows。

#### <a name="option-1-connect-the-certification-tool-to-a-linux-vm-image"></a>选项1：将认证工具连接到 Linux VM 映像

1. 选择“SSH 身份验证”模式：密码身份验证或密钥文件身份验证。
2. 如果使用基于密码的身份验证，请输入“VM DNS 名称”、“用户名”和“密码”的值  。 还可以更改默认的“SSH 端口”号。

    ![Azure 认证测试工具、Linux VM 映像的密码验证](media/avm-cert2.png)

3. 如果使用基于密钥文件的身份验证，请输入“VM DNS 名称”、“用户名”和“私钥”位置的值。   还可以包含通行短语，或更改默认的“SSH 端口”号 。

#### <a name="option-2-connect-the-certification-tool-to-a-windows-based-vm-image"></a>选项2：将认证工具连接到基于 Windows 的 VM 映像

1. 输入完全限定的 VM DNS 名称（例如 MyVMName.Cloudapp.net）。
2. 输入“用户名”和“密码”的值。 

    ![Azure 认证测试工具、基于 Windows 的 VM 映像的密码验证](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>运行认证测试

在认证工具中提供 VM 映像的参数值后，选择“测试连接”以创建与 VM 的有效连接。 验证连接后，选择“下一步”启动测试。 测试完成后，测试结果将显示在表中。 “状态”列显示每个测试（通过/失败/警告）。 如有任何测试失败，则不会认证该映像。 在这种情况下，请查看要求和失败消息，根据建议进行更改，然后再次运行测试。

自动测试完成后，请在“调查表”屏幕的两个选项卡“常规评估”和“内核自定义”上提供有关 VM 映像的其他信息，然后选择“下一步”   。

在最后一个屏幕中可以提供更多信息，例如 Linux VM 映像的 SSH 访问信息，以及在查找异常时对任何失败评估的说明。

最后，请选择“生成报告”下载已执行的测试案例的测试结果和日志文件，以及调查表的答案。 将结果保存在与 VHD 相同的容器中。

## <a name="next-step"></a>后续步骤

- [常见 SAS URI 问题和修复](common-sas-uri-issues.md)
