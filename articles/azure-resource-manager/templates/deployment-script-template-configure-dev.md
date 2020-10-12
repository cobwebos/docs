---
title: 为模板中的部署脚本配置开发环境 |Microsoft Docs
description: 为 Azure 资源管理器模板中的部署脚本配置开发环境。
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: jgao
ms.openlocfilehash: 232a1ae5d125a2ea1d5723e85073fb3dd02420cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87294003"
---
# <a name="configure-development-environment-for-deployment-scripts-in-templates-preview"></a>为模板中的部署脚本配置开发环境（预览版）

了解如何通过部署脚本映像创建用于开发和测试部署脚本的开发环境。 你可以创建 [Azure 容器实例](../../container-instances/container-instances-overview.md)或使用 [Docker](https://docs.docker.com/get-docker/)。 本文介绍了这两种方法。

## <a name="prerequisites"></a>先决条件

如果没有部署脚本，则可创建包含以下内容的 **hello.ps1** 文件：

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

## <a name="use-azure-container-instance"></a>使用 Azure 容器实例

若要在你的计算机上创建脚本，需要创建一个存储帐户，并将存储帐户装载到容器实例。 这样，你就可以将脚本上传到存储帐户，并在容器实例上运行该脚本。

> [!NOTE]
> 为测试你的脚本而创建的存储帐户与部署脚本服务用来执行脚本的存储帐户不是同一个。 部署脚本服务会在每次执行时创建唯一名称作为文件共享。

### <a name="create-an-azure-container-instance"></a>创建 Azure 容器实例

以下 ARM 模板创建一个容器实例和文件共享，然后将文件共享装载到容器映像。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "deploymentScript",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('projectName'), 'store')]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/sh",
                "-c",
                "pwsh -c 'Start-Sleep -Seconds 1800'"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```
装载路径的默认值是 **deploymentScript**。  这是容器实例中将它装载到文件共享的路径。

模板中指定的默认容器映像为 **mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3**。  有关受支持的 Azure PowerShell 版本和 Azure CLI 版本的列表，请参阅 [Azure PowerShell 或 Azure CLI](./deployment-script-template.md#prerequisites)。

模板将容器实例暂停 1800 秒。 在容器实例进入终端状态并且会话结束之前，你有 30 分钟的时间。

若要部署模板：

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-azResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-deployment-script"></a>上传部署脚本

将部署脚本上传到存储帐户。 下面是一个 PowerShell 示例：

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

你也可使用 Azure 门户和 Azure CLI 来上传证书。

### <a name="test-the-deployment-script"></a>测试部署脚本

1. 在 Azure 门户中，打开部署了容器实例和存储帐户的资源组。
1. 打开容器组。 默认资源组名称是追加了 **cg** 的项目名称。 你应该会看到容器实例处于“正在运行”状态。
1. 从左侧菜单中选择“容器”。 你应该会看到一个容器实例。  容器实例名称是追加了 **container** 的项目名称。

    ![部署脚本连接容器实例](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. 依次选择“连接”、“连接” 。 如果无法连接到容器实例，请重启容器组，然后重试。
1. 在控制台窗格中运行以下命令：

    ```
    cd deploymentScript
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    输出为 **Hello John Dole**。

    ![部署脚本容器实例测试](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-docker"></a>使用 Docker

可以使用预配置的 docker 容器映像作为部署脚本开发环境。 若要安装 Docker，请参阅[获取 Docker](https://docs.docker.com/get-docker/)。
你还需要配置文件共享以将包含部署脚本的目录装载到 Docker 容器中。

1. 将部署脚本容器映像拉取到本地计算机：

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    此示例使用 PowerShell 4.3.0 版本。

    从 Microsoft 容器注册表 (MCR) 拉取 CLI 映像：

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    此示例使用 CLI 2.0.80 版本。 部署脚本使用[此处](https://hub.docker.com/_/microsoft-azure-cli)的默认 CLI 容器映像。

1. 在本地运行 Docker 映像。

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    将“&lt;主机驱动器号>”和“&lt;主机目录名>”替换为共享驱动器上的现有文件夹**** ****。  它将文件夹映射到容器中的/data 文件夹****。 例如，要映射 D:\docker：

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **-it** 意味着使容器映像保持活动状态。

    CLI 示例：

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. 以下屏幕截图显示了如何运行 PowerShell 脚本，假设你在共享驱动器中具有 helloworld.ps1 文件。

    ![资源管理器模板部署脚本 docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

成功测试脚本后，可以将其用作模板中的部署脚本。

## <a name="next-steps"></a>后续步骤

本教程已介绍部署脚本的用法。 完成部署脚本教程：

> [!div class="nextstepaction"]
> [教程：使用 Azure 资源管理器模板中的部署脚本](./template-tutorial-deployment-script.md)
