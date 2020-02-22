---
title: 加密部署数据
description: 了解为容器实例资源保留的数据加密，以及如何使用客户管理的密钥对数据进行加密
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: 41c7fc7380ca2b58326c4a35a3b5fdab1c64c4a3
ms.sourcegitcommit: 78f367310e243380b591ff10f2500feca93f5d0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77544311"
---
# <a name="encrypt-deployment-data"></a>加密部署数据

在云中运行 Azure 容器实例（ACI）资源时，ACI 服务会收集并保留与你的容器相关的数据。 当此数据保存在云中时，ACI 会自动对其进行加密。 此加密可保护你的数据，以帮助你满足组织的安全性和符合性承诺。 ACI 还提供了用自己的密钥对数据进行加密的选项，使您可以更好地控制与 ACI 部署相关的数据。

## <a name="about-aci-data-encryption"></a>关于 ACI 数据加密 

使用256位 AES 加密对 ACI 中的数据进行加密和解密。 它适用于所有 ACI 部署，无需修改部署或容器即可利用此加密。 这包括有关部署、环境变量、要传入容器的密钥的元数据，以及在停止容器后保存的日志，以便仍可查看这些信息。 加密不会影响容器组的性能，并且无需额外付费。

## <a name="encryption-key-management"></a>加密密钥管理

你可以依赖于 Microsoft 托管的密钥来加密容器数据，也可以通过自己的密钥来管理加密。 下表比较了这些选项： 

|    |    Microsoft 托管的密钥     |     客户管理的密钥     |
|----|----|----|
|    加密/解密操作    |    Azure    |    Azure    |
|    密钥存储    |    Microsoft 密钥存储    |    Azure Key Vault    |
|    关键轮换责任    |    Microsoft    |    客户    |
|    密钥访问权限    |    仅限 Microsoft    |    Microsoft，客户    |

本文档的其余部分涵盖了用密钥（客户管理的密钥）对 ACI 部署数据进行加密所需的步骤。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>使用客户管理的密钥加密数据

### <a name="create-service-principal-for-aci"></a>为 ACI 创建服务主体

第一步是确保[azure 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)具有分配给 Azure 容器实例服务授予权限的服务主体。 

以下 CLI 命令将在你的 Azure 环境中设置 ACI SP：

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

运行此命令的输出应显示已设置了 "displayName" 的服务主体："Azure 容器实例服务"。

### <a name="create-a-key-vault-resource"></a>创建 Key Vault 资源

使用[Azure 门户](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault)、 [CLI](https://docs.microsoft.com/azure/key-vault/quick-create-cli)或[PowerShell](https://docs.microsoft.com/azure/key-vault/quick-create-powershell)创建 Azure Key Vault。 

对于密钥保管库的属性，请使用以下准则： 
* 姓名：必须提供唯一的名称。 
* 订阅：选择订阅。
* 在 "资源组" 下，选择现有资源组，或创建新资源组，并输入资源组名称。
* 在“位置”下拉菜单中选择一个位置。
* 可以将其他选项保留为其默认值，也可以根据其他要求进行选择。

> [!IMPORTANT]
> 使用客户管理的密钥加密 ACI 部署模板时，建议在 key vault 上设置以下两个属性，软删除并不清除。 默认情况下不启用这些属性，但可以使用 PowerShell 或 Azure CLI 在新的或现有的密钥保管库上启用。

### <a name="generate-a-new-key"></a>生成新密钥 

创建密钥保管库后，请导航到 Azure 门户中的资源。 在资源边栏选项卡的左侧导航菜单中的 "设置" 下，单击 "**密钥**"。 在 "项" 的视图上，单击 "生成/导入" 生成新密钥。 此密钥的任何唯一名称，以及任何其他基于要求的首选项。 

![生成新密钥](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>设置访问策略

创建新的访问策略，以允许 ACI 服务访问你的密钥。

* 生成密钥后，返回到密钥保管库资源边栏选项卡的 "设置" 下，单击 "**访问策略**"。
* 在密钥保管库的 "访问策略" 页上，单击 "**添加访问策略**"。
* 设置*密钥权限*，使其包含**Get**和**解包密钥**![设置密钥权限](./media/container-instances-encrypt-data/set-key-permissions.png)
* 对于 "*选择主体*"，请选择 " **Azure 容器实例服务**"
* 单击底部的 "**添加**" 

访问策略现在应显示在密钥保管库的访问策略中。

![新建访问策略](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>修改 JSON 部署模板

> [!IMPORTANT]
> 使用客户管理的密钥加密部署数据在当前推出的最新 API 版本（2019-12-01）中提供。在部署模板中指定此 API 版本。 如果你有任何问题，请联系 Azure 支持部门。

设置密钥保管库密钥和访问策略后，将以下属性添加到 ACI 部署模板。 有关使用 [教程中的模板部署 ACI 资源的详细信息，请参阅：使用资源管理器模板](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)部署多容器组。 
* 在 "`resources`" 下，将 `apiVersion` 设置为 "`2019-12-01`"。
* 在部署模板的容器组属性部分下，添加一个 `encryptionProperties`，其中包含以下值：
  * `vaultBaseUrl`：密钥保管库的 DNS 名称，可在门户中密钥保管库资源的 "概述" 边栏选项卡上找到。
  * `keyName`：之前生成的密钥的名称
  * `keyVersion`：密钥的当前版本。 若要找到此项，可单击密钥本身（在 key vault 资源的 "设置" 部分中的 "密钥" 下）
* 在容器组属性下，添加值为 `Standard`的 `sku` 属性。 API 版本2019-12-01 中需要 `sku` 属性。

以下模板代码段显示了用于加密部署数据的其他属性：

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

下面是一个完整的模板，与 [教程中的模板改编：使用资源管理器模板](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)部署多容器组。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "encryptionProperties": {
            "vaultBaseUrl": "https://example.vault.azure.net",
            "keyName": "acikey",
            "keyVersion": "xxxxxxxxxxxxxxxx"
        },
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>部署资源

如果在桌面上创建并编辑了模板文件，则可以通过将文件拖动到 Cloud Shell 目录上，将其上传到该文件。 

使用[az group create][az-group-create]命令创建资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az group deployment create][az-group-deployment-create] 命令部署模板。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

将在几秒钟内收到来自 Azure 的初始响应。 部署完成后，所有与该服务相关的数据都将使用您提供的密钥进行加密。

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
