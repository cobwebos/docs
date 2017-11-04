---
title: "部署 Azure 堆栈上的安全地存储密码的虚拟机 |Microsoft 文档"
description: "了解如何使用 Azure 堆栈密钥保管库中存储的密码部署 VM"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 23322a49-fb7e-4dc2-8d0e-43de8cd41f80
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/08/2017
ms.author: sngun
ms.openlocfilehash: 3292a2dfefc17e5034c66122a3eab24d6c03e694
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-by-retrieving-the-password-stored-in-a-key-vault"></a>通过检索密钥保管库中存储的密码创建虚拟机

当你需要在部署期间将安全的值，如密码时，可以将该值存储在 Azure 堆栈密钥保管库中的机密形式，并在 Azure 资源管理器模板中引用它。 进行不需要手动输入机密部署的资源中，每次还可以指定哪些用户或服务主体可以访问机密。 

在本文中，我们引导你完成通过检索密钥保管库中存储的密码中部署 Azure 堆栈中的 Windows 虚拟机所需的步骤。 因此密码永远不会放在模板参数文件中的纯文本。 如果你通过 VPN 连接，你可以使用以下步骤从 Azure 堆栈开发工具包中，或者从外部客户端。

## <a name="prerequisites"></a>必备组件
 
* 你必须必须订阅服务关联，它包含密钥保管库服务。  
* [安装适用于 Azure 堆栈 PowerShell。](azure-stack-powershell-install.md)  
* [配置 Azure 堆栈用户 PowerShell 环境。](azure-stack-powershell-configure-user.md)

以下步骤说明通过检索密钥保管库中存储的密码创建虚拟机所需的过程：

1. 创建密钥保管库密钥。
2. 更新 azuredeploy.parameters.json 文件。
3. 部署模板。

## <a name="create-a-key-vault-secret"></a>创建密钥保管库密钥

以下脚本创建密钥保管库，并将密码存储在密钥保管库的机密形式。 使用`-EnabledForDeployment`参数在创建密钥保管库时。 此参数可确保密钥保管库可以从 Azure 资源管理器模板来引用。

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

运行前面的脚本时，输出中包括的机密的 URI。 记下此 URI。 你必须引用在[密钥保管库模板中具有密码的部署 Windows 虚拟机](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv)。 下载[101-vm 的安全的密码](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv)到开发计算机上的文件夹。 此文件夹包含`azuredeploy.json`和`azuredeploy.parameters.json`文件，你将需要在下面的步骤。

修改`azuredeploy.parameters.json`根据你环境的值的文件。 参数感兴趣的是保管库名称、 保管库资源组和机密 URI （如由前面的脚本生成）。 下面的文件是参数文件的示例：

## <a name="update-the-azuredeployparametersjson-file"></a>更新 azuredeploy.parameters.json 文件

更新使用 KeyVault 的 URI、 secretName，根据你的环境的虚拟机值 adminUsername azuredeploy.parameters.json 文件。 下面的 JSON 文件显示模板参数文件的示例： 

```json
{
    "$schema":  "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>模板部署

现在使用以下 PowerShell 脚本部署模板：

```powershell
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```
已成功部署模板时，这会导致下面的输出：

![部署输出](media/azure-stack-kv-deploy-vm-with-secret/deployment-output.png)


## <a name="next-steps"></a>后续步骤
[部署包含密钥保管库的示例应用程序](azure-stack-kv-sample-app.md)

[将使用密钥保管库证书 VM 部署](azure-stack-kv-push-secret-into-vm.md)

