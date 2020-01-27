---
title: 使用模板部署脚本 | Microsoft Docs
description: 了解如何使用 Azure 资源管理器模板中的部署脚本。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/09/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 459d75bec3d4b4d0cf9057e0c6de238e7f165bfb
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548979"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>教程：使用部署脚本创建自签名证书（预览版）

了解如何使用 Azure 资源管理器模板中的部署脚本。 可以使用部署脚本执行无法由资源管理器模板完成的自定义步骤。 例如，创建自签名证书。  在本教程中，你将创建一个模板来部署 Azure 密钥保管库，然后在同一模板中使用 `Microsoft.Resources/deploymentScripts` 资源来创建证书并将证书添加到密钥保管库。 若要详细了解部署脚本，请参阅[使用 Azure 资源管理器模板中的部署脚本](./deployment-script-template.md)。

> [!NOTE]
> 部署脚本目前为预览版。 若要使用它，必须[注册预览版](https://aka.ms/armtemplatepreviews)。

> [!IMPORTANT]
> 在同一资源组中会创建两个部署脚本资源（一个存储帐户和一个容器实例），用于执行脚本和进行故障排除。 当脚本执行达到某个最终状态时，这些资源通常会被脚本服务删除。 在这些资源删除之前，这些资源会一直向你收费。 若要了解详细信息，请参阅[清理部署脚本资源](./deployment-script-template.md#clean-up-deployment-script-resources)。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 打开快速入门模板
> * 编辑模板
> * 部署模板
> * 调试失败的脚本
> * 清理资源

## <a name="prerequisites"></a>必备条件

若要完成本文，需要做好以下准备：

* **包含资源管理器工具扩展的 [Visual Studio Code](https://code.visualstudio.com/)** 。 请参阅[使用 Visual Studio Code 创建 Azure 资源管理器模板](./use-vs-code-to-create-template.md)。

* **在订阅级别具有参与者角色的用户分配的托管标识**。 此标识用来执行部署脚本。 若要创建一个标识，请参阅[用户分配的托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity)。 部署模板时需要此标识 ID。 标识符的格式为：

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  使用以下 PowerShell 脚本通过提供资源组名称和标识名称来获取 ID。

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

## <a name="open-a-quickstart-template"></a>打开快速入门模板

无需从头开始创建模板，可以通过 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/)打开一个模板。 Azure 快速入门模板是资源管理器模板的存储库。

本快速入门中使用的模板名为[创建 Azure 密钥保管库和机密](https://azure.microsoft.com/resources/templates/101-key-vault-create/)。 该模板将创建一个密钥保管库，然后向该密钥保管库中添加机密。

1. 在 Visual Studio Code 中，选择“文件”>“打开文件”。  
2. 在“文件名”中粘贴以下 URL： 

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. 选择“打开”以打开该文件。 
4. 选择“文件”>“另存为”，将该文件作为 **azuredeploy.json** 保存到本地计算机。  

## <a name="edit-the-template"></a>编辑模板

对模板进行以下更改：

### <a name="clean-up-the-template-optional"></a>清理模板（可选）

原始模板向密钥保管库中添加机密。  为了简化本教程，请删除以下资源：

* **Microsoft.KeyVault/vaults/secrets**

删除以下两个参数定义：

* **secretName**
* **secretValue**

如果选择不删除这些定义，则需要在部署过程中指定参数值。

### <a name="configure-the-key-vault-access-policies"></a>配置密钥保管库访问策略

部署脚本将证书添加到密钥保管库。 配置密钥保管库访问策略，以便向托管标识授予权限：

1. 添加参数以获取托管标识 ID：

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > Visual Studio Code 的资源管理器模板扩展尚不能格式化部署脚本。 不要使用 [SHIFT] + [ALT] + F 设置 deploymentScripts 资源的格式，如下所示。

1. 添加一个参数，用于配置密钥保管库访问策略，以便托管标识可以将证书添加到密钥保管库。

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. 将现有的密钥保管库访问策略更新为：

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    定义了两个策略，一个用于已登录用户，另一个用于托管标识。  已登录用户仅需要“列表”  权限来验证部署。  为了简化本教程，为托管标识和已登录用户分配了相同的证书。

### <a name="add-the-deployment-script"></a>添加部署脚本

1. 添加部署脚本使用的三个参数。

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }

1. Add a deploymentScripts resource:

    > [!NOTE]
    > Because the inline deployment scripts are enclosed in double quotes, the strings inside the deployment scripts need to be enclosed in single quotes instead. The escape character for PowerShell is **&#92;**.

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2019-10-01-preview",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "2.8",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an arguement string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    `deploymentScripts` 资源依赖于密钥保管库资源和角色分配资源。  它具有以下属性：

    * **identity**：部署脚本使用用户分配的托管标识来执行脚本。
    * **kind**：指定脚本类型。 目前仅支持 PowerShell 脚本。
    * **forceUpdateTag**：确定是否应执行部署脚本，即使脚本源未更改。 可以是当前时间戳或 GUID。 若要了解详细信息，请参阅[多次运行脚本](./deployment-script-template.md#run-script-more-than-once)。
    * **azPowerShellVersion**：指定要使用的 Azure PowerShell 模块版本。 目前，部署脚本支持版本 2.7.0、2.8.0 和 3.0.0。
    * **timeout**：指定 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601)中指定的脚本执行最大允许时间。 默认值为 **P1D**。
    * **arguments**：指定参数值。 请以空格分隔这些值。
    * **scriptContent**：指定脚本内容。 若要运行外部脚本，请改用 **primaryScriptURI**。 有关详细信息，请参阅[使用外部脚本](./deployment-script-template.md#use-external-scripts)。
        仅当在本地计算机上测试脚本时，才需要声明 **$DeploymentScriptOutputs**。 通过声明该变量，可在本地计算机和 deploymentScript 资源中运行脚本，而无需进行更改。 分配给 $DeploymentScriptOutputs 的值可用作部署中的输出。 有关详细信息，请参阅[使用部署脚本的输出](./deployment-script-template.md#work-with-outputs-from-deployment-scripts)。
    * **cleanupPreference**：指定有关何时删除部署脚本资源的首选项。  默认值为 **Always**，这意味着不管最终状态如何（成功、失败、已取消），都会删除部署脚本资源。 在本教程中，将使用 **OnSuccess**，以便你有机会查看脚本执行结果。
    * **retentionInterval**：指定服务在达到最终状态后保留脚本资源的时间间隔。 在此持续时间到期时，将删除资源。 持续时间基于 ISO 8601 模式。 本教程使用 P1D，这意味着只有一天的时间。  当 **cleanupPreference** 设置为 **OnExpiration** 时将使用此属性。 当前未启用此属性。

    部署脚本采用三个参数：密钥保管库名称、证书名称和使用者名称。  它将创建一个证书，然后将该证书添加到密钥保管库。

    **$DeploymentScriptOutputs** 用来存储输出值。  若要了解详细信息，请参阅[使用部署脚本的输出](./deployment-script-template.md#work-with-outputs-from-deployment-scripts)。

    可在[此处](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)找到完成的模板。

1. 若要查看调试过程，请将以下行添加到部署脚本，从而在代码中放置一个错误：

    ```powershell
    Write-Output1 $keyVaultName
    ```

    正确的命令是 **Write-Output** 而非 **Write-Output1**。

1. 选择“文件”>“保存”以保存文件。  

## <a name="deploy-the-template"></a>部署模板

参阅 Visual Studio Code 快速入门中的[部署模板](./quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template)部分，以打开 Cloud shell 并将模板文件上传到该 shell。 然后，运行以下 PowerShell 脚本：

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
$identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$resourceGroupName = "${projectName}rg"
$keyVaultName = "${projectName}kv"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

Write-Host "Press [ENTER] to continue ..."
```

部署脚本服务需要为脚本执行创建其他部署脚本资源。 除了实际的脚本执行时间外，准备和清理过程最多可能需要一分钟才能完成。

部署将因为无效的命令而失败，脚本中使用了 **Write-Output1**。 你会收到一个错误，指出：

```error
The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
```

部署脚本执行结果存储在部署脚本资源中以用于排除故障。

## <a name="debug-the-failed-script"></a>调试失败的脚本

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 打开资源组。 资源组是追加了 **rg** 的项目名称。 你会看到，该资源组中总共有两个其他资源。 这些资源称为*部署脚本资源*。

    ![资源管理器模板部署脚本资源](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    这两个文件的后缀都是 **azscripts**。 一个是存储帐户，另一个是容器实例。

    选择“显示隐藏的类型”  来列出 deploymentScripts 资源。

1. 选择带 **azscripts** 后缀的存储帐户。
1. 选择“文件共享”  磁贴。 你将看到一个 **azscripts** 文件夹。  该文件夹包含部署脚本执行文件。
1. 选择“azscripts”  。 你将看到两个文件夹：**azscriptinput** 和 **azscriptoutput**。  输入文件夹包含一个系统 PowerShell 脚本文件和一些用户部署脚本文件。 输出文件夹包含 **executionresult.json** 和脚本输出文件。 可以在 **executionresult.json** 中看到错误消息。 输出文件不在那里，因为执行失败。

删除 **Write-Output1** 行并重新部署模板。

当第二次部署成功运行时，脚本服务将删除部署脚本资源，因为 **cleanupPreference** 属性设置为 **OnSuccess**。

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 
3. 选择资源组名称。  应会看到，该资源组中总共有六个资源。
4. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解了如何使用 Azure 资源管理器模板中的部署脚本。 若要了解如何根据条件部署 Azure 资源，请参阅：

> [!div class="nextstepaction"]
> [使用条件](./template-tutorial-use-conditions.md)
