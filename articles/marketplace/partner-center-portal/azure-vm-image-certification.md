---
title: Azure 虚拟机映像验证-Azure Marketplace
description: 了解如何在商业市场中测试和提交虚拟机产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: fd8f41f88b6184eee15477c460dc9d2e521d25e6
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144130"
---
# <a name="azure-virtual-machine-image-validation"></a>Azure 虚拟机映像验证

本文介绍如何在商业市场中测试和提交虚拟机 (VM) 映像，以确保它满足最新的 Azure 市场发布要求。

在提交 VM 产品/服务之前，请先完成以下步骤：

- 使用通用映像部署 Azure VM。
- 运行验证。

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>使用通用映像部署 Azure VM

本部分介绍如何部署通用化虚拟硬盘 (VHD) 映像来创建新的 Azure VM 资源。 对于此过程，我们将使用提供的 Azure 资源管理器模板和 Azure PowerShell 脚本。

### <a name="prepare-an-azure-resource-manager-template"></a>准备 Azure 资源管理器模板

本部分介绍如何创建和部署用户提供的虚拟机 (VM) 映像。 为此，可以从 Azure 部署的虚拟硬盘提供操作系统和数据磁盘 VHD 映像。 这些步骤使用通用 VHD 部署 VM。

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 将通用操作系统 VHD 和数据磁盘 Vhd 上传到 Azure 存储帐户。
3. 在主页上，选择 " **创建资源**"，搜索 "模板部署"，然后选择 " **创建**"。
4. 选择“在编辑器中生成自己的模板”。

    :::image type="content" source="media/vm/template-deployment.png" alt-text="显示模板的选定内容。":::

5. 将以下 JSON 模板粘贴到编辑器中，然后选择 " **保存**"。

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "String"
        },
        "userStorageContainerName": {
            "defaultValue": "vhds",
            "type": "String"
        },
        "dnsNameForPublicIP": {
            "type": "String"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "String"
        },
        "adminPassword": {
            "defaultValue": "",
            "type": "SecureString"
        },
        "osType": {
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ],
            "type": "String"
        },
        "subscriptionId": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "vmSize": {
            "type": "String"
        },
        "publicIPAddressName": {
            "type": "String"
        },
        "vmName": {
            "type": "String"
        },
        "virtualNetworkName": {
            "type": "String"
        },
        "nicName": {
            "type": "String"
        },
        "vhdUrl": {
            "type": "String",
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
        "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
        "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2015-06-15",
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
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2015-06-15",
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
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2015-06-15",
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
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2015-06-15",
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
                    "adminPassword": "[parameters('adminPassword')]"
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
                }
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "apiVersion": "2015-06-15",
            "name": "[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "typeHandlerVersion": "1.4",
                "settings": {
                    "fileUris": [
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd"
                    ],
                    "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]"
                }
            }
        }
    ]
}
```

<br>

6. 提供显示的“自定义部署”属性页的参数值。

| ResourceGroupName | 现有 Azure 资源组名称。 通常，使用与密钥保管库相同的 RG。 |
| --- | --- |
| TemplateFile | 文件 VHDtoImage.json 的完整路径名。 |
| userStorageAccountName | 存储帐户的名称。 |
| dnsNameForPublicIP | 公共 IP 的 DNS 名称；必须为小写。 |
| subscriptionId | Azure 订阅标识符。 |
| 位置 | 资源组的标准 Azure 地理位置。 |
| vmName | 虚拟机名称。 |
| vhdUrl | 虚拟硬盘的 Web 地址。 |
| vmSize | 虚拟机实例的大小。 |
| publicIPAddressName | 公共 IP 地址的名称。 |
| virtualNetworkName | 虚拟网络的名称。 |
| nicName | 虚拟网络的网络接口卡的名称。 |
| adminUserName | 管理员帐户的用户名。 |
| adminPassword | 管理员密码。 |
|

7. 提供这些值后，选择“购买”。

Azure 将开始部署。 它将使用指定的非托管 VHD 在指定的存储帐户路径中创建新 VM。 可以选择 Azure 门户左侧的“虚拟机”，在门户中跟踪进度。 创建 VM 后，状态将从“正在启动”更改为“正在运行”。

#### <a name="for-generation-2-vm-deployment-use-this-template"></a>对于第2代 VM 部署，请使用此模板：

```JSON
{
   "$schema":"https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{
      "userStorageAccountName":{
         "type":"String"
      },
      "userStorageContainerName":{
         "type":"String",
         "defaultValue":"vhds-86350-720-f4efbbb2-611b-4cd7-ad1e-afdgfuadfluad"
      },
      "dnsNameForPublicIP":{
         "type":"String"
      },
      "adminUserName":{
         "defaultValue":"isv",
         "type":"String"
      },
      "adminPassword":{
         "type":"securestring",
         "defaultValue":"Certcare@86350"
      },
      "osType":{
         "type":"string",
         "defaultValue":"linux",
         "allowedValues":[
            "windows",
            "linux"
         ]
      },
      "subscriptionId":{
         "type":"string"
      },
      "location":{
         "type":"string"
      },
      "vmSize":{
         "type":"string"
      },
      "publicIPAddressName":{
         "type":"string"
      },
      "vmName":{
         "type":"string"
      },
      "vNetNewOrExisting":{
         "defaultValue":"existing",
         "allowedValues":[
            "new",
            "existing"
         ],
         "type":"String",
         "metadata":{
            "description":"Specify whether to create a new or existing virtual network for the VM."
         }
      },
      "virtualNetworkName":{
         "type":"String",
         "defaultValue":""
      },
      "SubnetName":{
         "defaultValue":"subnet-5",
         "type":"String"
      },
      "SubnetPrefix":{
         "defaultValue":"10.0.5.0/24",
         "type":"String"
      },
      "nicName":{
         "type":"string"
      },
      "vhdUrl":{
         "type":"string",
         "metadata":{
            "description":"VHD Url..."
         }
      },
      "Datadisk1":{
         "type":"string",
         "metadata":{
            "description":"datadisk1 Url..."
         }
      },
      "Datadisk2":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      },
      "Datadisk3":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      }
   },
   "variables":{
      "addressPrefix":"10.0.0.0/16",
      "subnet1Name":"[parameters('SubnetName')]",
      "subnet1Prefix":"[parameters('SubnetPrefix')]",
      "publicIPAddressType":"Static",
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
      "subnet1Ref":"[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
      "osDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]",
      "dataDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'datadisk')]",
      "imageName":"[concat(parameters('vmName'), '-image')]"
   },
   "resources":[
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/publicIPAddresses",
         "name":"[parameters('publicIPAddressName')]",
         "location":"[parameters('location')]",
         "properties":{
            "publicIPAllocationMethod":"[variables('publicIPAddressType')]",
            "dnsSettings":{
               "domainNameLabel":"[parameters('dnsNameForPublicIP')]"
            }
         }
      },
      {
         "type":"Microsoft.Compute/images",
         "apiVersion":"2019-12-01",
         "name":"[variables('imageName')]",
         "location":"[parameters('location')]",
         "properties":{
            "storageProfile":{
               "osDisk":{
                  "osType":"[parameters('osType')]",
                  "osState":"Generalized",
                  "blobUri":"[parameters('vhdUrl')]",
                  "storageAccountType":"Standard_LRS"
               },
               "dataDisks":[
                  {
                     "lun":0,
                     "blobUri":"[parameters('Datadisk1')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":1,
                     "blobUri":"[parameters('Datadisk2')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":2,
                     "blobUri":"[parameters('Datadisk3')]",
                     "storageAccountType":"Standard_LRS"
                  }
               ]
            },
            "hyperVGeneration":"V2"
         }
      },
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/virtualNetworks",
         "name":"[parameters('virtualNetworkName')]",
         "location":"[parameters('location')]",
         "properties":{
            "addressSpace":{
               "addressPrefixes":[
                  "[variables('addressPrefix')]"
               ]
            },
            "subnets":[
               {
                  "name":"[variables('subnet1Name')]",
                  "properties":{
                     "addressPrefix":"[variables('subnet1Prefix')]"
                  }
               }
            ]
         },
         "condition":"[equals(parameters('vNetNewOrExisting'), 'new')]"
      },
      {
         "apiVersion":"2016-09-01",
         "type":"Microsoft.Network/networkInterfaces",
         "name":"[parameters('nicName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
         ],
         "properties":{
            "ipConfigurations":[
               {
                  "name":"ipconfig1",
                  "properties":{
                     "privateIPAllocationMethod":"Dynamic",
                     "publicIPAddress":{
                        "id":"[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                     },
                     "subnet":{
                        "id":"[variables('subnet1Ref')]"
                     }
                  }
               }
            ]
         }
      },
      {
         "apiVersion":"2019-12-01",
         "type":"Microsoft.Compute/virtualMachines",
         "name":"[parameters('vmName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]",
            "[variables('imageName')]"
         ],
         "properties":{
            "hardwareProfile":{
               "vmSize":"[parameters('vmSize')]"
            },
            "osProfile":{
               "computername":"[parameters('vmName')]",
               "adminUsername":"[parameters('adminUsername')]",
               "adminPassword":"[parameters('adminPassword')]"
            },
            "storageProfile":{
               "imageReference":{
                  "id":"[resourceId('Microsoft.Compute/images', variables('imageName'))]"
               },
               "dataDisks":[
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk0')]",
                     "lun":0,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk1')]",
                     "lun":1,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk2')]",
                     "lun":2,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  }
               ],
               "osDisk":{
                  "name":"[concat(parameters('vmName'),'-OSDisk')]",
                  "createOption":"FromImage",
                  "managedDisk":{
                     "storageAccountType":"Standard_LRS"
                  }
               }
            },
            "networkProfile":{
               "networkInterfaces":[
                  {
                     "id":"[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                  }
               ]
            }
         }
      }
   ]
}
```

### <a name="deploy-an-azure-vm-using-powershell"></a>使用 PowerShell 部署 Azure VM

复制并编辑以下脚本以提供 `$storageaccount` 和 `$vhdUrl` 变量的值。 执行它，从现有通用 VHD 创建 Azure VM 资源。

```PowerShell
# storage account of existing generalized VHD

$storageaccount = "testwinrm11815" # generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" - userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" - vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id -vhdUrl"$vhdUrl" - vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" - adminPassword$pwd
```

## <a name="run-validations"></a>运行验证

可以通过两种方式对已部署的映像运行验证。

### <a name="use-certification-test-tool-for-azure-certified"></a>使用 Azure 认证的认证测试工具

#### <a name="download-and-run-the-certification-test-tool"></a>下载并运行认证测试工具

“Azure 认证”的认证测试工具在本地 Windows 计算机上运行，但可以测试基于 Azure 的 Windows 或 Linux VM。 它可证明用户 VM 映像可与 Microsoft Azure 一起使用，并且已满足有关准备 VHD 的指南和要求。

1. 下载并安装最新的[“Azure 认证”的认证测试工具](https://www.microsoft.com/download/details.aspx?id=44299)。
2. 打开认证工具，然后选择“启动新测试”。
3. 在“测试信息”屏幕中，为测试运行输入测试名称。
4. 为 VM 选择平台，无论是 **Windows Server** 还是 **Linux**。 所选的平台会影响剩余的选项。
5. 如果 VM 使用此数据库服务，请选中“针对 Azure SQL 数据库进行测试”复选框。

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>将认证工具连接到 VM 映像

1. 选择“SSH 身份验证”模式：密码身份验证或密钥文件身份验证。
2. 如果使用基于密码的身份验证，请输入 " **VM DNS 名称**"、" **用户名**" 和 " **密码**" 的值。 还可以更改默认的“SSH 端口”号。

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="显示 VM 测试信息的选择。":::

3. 如果使用基于密钥文件的身份验证，请输入“VM DNS 名称”、“用户名”和“私钥”位置的值。   还可以包含通行短语，或更改默认的“SSH 端口”号 。
4. 输入完全限定的VM DNS 名称（例如 MyVMName.Cloudapp.net）。
5. 输入 **用户名** 和 **密码**。

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="显示 VM 用户名和密码的选择。":::

6. 选择“**下一步**”。

#### <a name="run-a-certification-test"></a>运行认证测试

在认证工具中为 VM 映像提供参数值后，请选择 "测试连接" 以创建到 VM 的有效连接。 验证连接后，选择“下一步”启动测试。 测试完成后，结果将显示在表中。 “状态”列显示每个测试（通过/失败/警告）。 如有任何测试失败，则不会认证该映像。 在这种情况下，请查看要求和失败消息，根据建议进行更改，然后再次运行测试。

自动测试完成后，请在“调查表”屏幕的两个选项卡“常规评估”和“内核自定义”上提供有关 VM 映像的其他信息，然后选择“下一步”   。

使用最后一个屏幕可以提供详细信息，例如 Linux VM 映像的 SSH 访问信息，以及在查找异常时对任何失败评估的说明。

最后，请选择“生成报告”下载已执行的测试案例的测试结果和日志文件，以及调查表的答案。 将结果保存在与 VHD 相同的容器中。

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>如何使用 PowerShell 来使用自测 API

### <a name="on-linux-os"></a>在 Linux 操作系统上

在 PowerShell 中调用 API：

1. 使用 WebRequest 命令调用 API。
2. 方法是 Post，内容类型是 JSON，如以下代码示例和屏幕截图所示。
3. 采用 JSON 格式指定正文参数。

下面的示例演示对 API 的 PowerShell 调用：

```POWERSHELL
$accesstoken = “token”
$headers = New-Object “System.Collections.Generic.Dictionary[[String],[String]]”
$headers.Add(“Authorization”, “Bearer $accesstoken”)
$DNSName = “\&lt;\&lt;Machine DNS Name\&gt;\&gt;”
$UserName = “\&lt;\&lt;User ID\&gt;\&gt;”
$Password = “\&lt;\&lt;Password\&gt;\&gt;”
$OS = “Linux”
$PortNo = “22”
$CompanyName = “ABCD”
$AppID = “\&lt;\&lt;Application ID\&gt;\&gt;”
$TenantId = “\&lt;\&lt;Tenant ID\&gt;\&gt;”

$body =
@{
DNSName = $DNSName
UserName = $UserName
Password = $Password
OS = $OS
PortNo = $PortNo
CompanyName = $CompanyName
AppID = $AppID
TenantId = $TenantId
}| ConvertTo-Json

$body

$uri = “URL”

$res = (Invoke-WebRequest -Method “Post” -Uri $uri -Body $body -ContentType “application/json” -Headers $headers).Content
```

<br>下面是在 PowerShell 中调用 API 的示例：

[![在 PowerShell 中调用 API 的屏幕示例。](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>使用前面的示例，可以检索 JSON 并解析它以获取以下详细信息：

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>此示例屏幕显示了 `$res.Content` JSON 格式的测试结果的详细信息：

[![在 PowerShell 中调用 API 的屏幕示例，其中包含测试结果的详细信息。](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>下面是在联机 JSON 查看器 (如 [代码 Beautify](https://codebeautify.org/jsonviewer) 或 [JSON 查看器](https://jsonformatter.org/json-viewer)) 中查看的 json 测试结果的示例。

![联机 JSON 查看器中的更多测试结果。](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>在 Windows 操作系统上

在 PowerShell 中调用 API：

1. 使用 WebRequest 命令调用 API。
2. 方法为 Post 并且内容类型为 JSON，如下面的代码示例和示例屏幕中所示。
3. 创建 JSON 格式的正文参数。

此代码示例演示对 API 的 PowerShell 调用：

```PowerShell
$accesstoken = “Get token for your Client AAD App”$headers = New-Object”System.Collections.Generic.Dictionary[[String],[String]]”$headers.Add(“Authorization”, “Bearer $accesstoken”)$Body = @{ “DNSName” = “XXXX.westus.cloudapp.azure.com”“UserName” = “XXX”“Password” = “XXX@123456”“OS” = “Windows”“PortNo” = “5986”“CompanyName” = “ABCD” “AppID” = “XXXX-XXXX-XXXX” “TenantId” = “XXXX-XXXX-XXXX” } | ConvertTo-Json$res = Invoke-WebRequest -Method”Post” -Uri$uri -Body$Body -ContentType”application/json” –Headers $headers;$Content = $res | ConvertFrom-Json
```

以下示例屏幕显示了在 PowerShell 中调用 API 的示例：

**带有 SSH 密钥**：

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="使用 SSH 密钥在 PowerShell 中调用 API。":::

**密码**：

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="使用密码在 PowerShell 中调用 API。":::

<br>使用前面的示例，可以检索 JSON 并解析它以获取以下详细信息：

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>此屏幕显示了 `$res.Content` JSON 格式的测试结果的详细信息：

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="JSON 格式的测试结果的详细信息。":::

<br>下面是在联机 JSON 查看器中查看的测试结果的示例 (如 [Code Beautify](https://codebeautify.org/jsonviewer) 或 [JSON viewer](https://jsonformatter.org/json-viewer)) 。

![联机 JSON 查看器中的测试结果。](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>如何使用卷在 Linux 操作系统上使用自测 API

在卷中调用 API：

1. 使用 curl 命令调用 API。
2. 方法是 Post，内容类型是 JSON，如以下代码片段示例所示。

```JSON
CURL POST -H “Content-Type:application/json”

-H “Authorization: Bearer XXXXXX-Token-XXXXXXXX”

[https://isvapp.azure-api.net/selftest-vm](https://isvapp.azure-api.net/selftest-vm)

-d ‘{ “DNSName”:”XXXX.westus.cloudapp.azure.com”, “UserName”:”XXX”, “Password”:”XXXX@123456”, “OS”:”Linux”, “PortNo”:”22”, “CompanyName”:”ABCD”, “AppId”:”XXXX-XXXX-XXXX”, “TenantId “XXXX-XXXX-XXXX”}’
```

<br>下面是使用卷调用 API 的示例：

![使用卷调用 API 的示例。](media/vm/use-curl-call-api.png)

<br>下面是来自卷曲调用的 JSON 结果：

![来自卷调用的 JSON 结果。](media/vm/test-results-json-viewer-3.png)

## <a name="next-step"></a>后续步骤

- 阅读 [常见的 SAS URI 问题和修补程序](common-sas-uri-issues.md)。
