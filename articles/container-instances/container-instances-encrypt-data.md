---
title: 加密部署数据
description: 了解为容器实例资源保存的数据的加密，以及如何使用客户管理的密钥来加密数据
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 1c45999dbb354e8c2d550be82cdf37a6694d2dbb
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825683"
---
# <a name="encrypt-deployment-data"></a>加密部署数据

在云中运行 Azure 容器实例 (ACI) 资源时，ACI 服务将收集并保存与容器相关的数据。 将此数据保存在云中时，ACI 会自动对其进行加密。 这种加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。 ACI 还提供相应的选项让你使用自己的密钥来加密数据，使你可以更好地控制与 ACI 部署相关的数据。

## <a name="about-aci-data-encryption"></a>关于 ACI 数据加密 

ACI 中的数据是使用 256 位 AES 加密法加密和解密的。 此加密法已为所有 ACI 部署启用，无需修改部署或容器即可利用它。 这些数据包括有关部署的元数据、环境变量、要传入容器的密钥，以及在停止容器后保存的日志（以便仍可以查看这些日志）。 加密不影响容器组的性能，并且不产生额外的费用。

## <a name="encryption-key-management"></a>加密密钥管理

你可以依赖于 Microsoft 托管的密钥来加密容器数据，也可以通过自己的密钥来管理加密。 下表对这些选项做了比较： 

|    |    Microsoft 管理的密钥     |     客户管理的密钥     |
|----|----|----|
|    **加密/解密操作**    |    Azure    |    Azure    |
|    **密钥存储**    |    Microsoft 密钥存储    |    Azure Key Vault    |
|    **密钥轮换职责**    |    Microsoft    |    客户    |
|    **密钥访问权限**    |    仅限 Microsoft    |    Microsoft、客户    |

本文档的余下内容将介绍使用你自己的密钥（客户管理的密钥）加密 ACI 部署数据所要执行的步骤。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>使用客户管理的密钥来加密数据

### <a name="create-service-principal-for-aci"></a>为 ACI 创建服务主体

第一步是确保为 [Azure 租户](../active-directory/develop/quickstart-create-new-tenant.md)分配一个服务主体，用于向 Azure 容器实例服务授予权限。 

> [!IMPORTANT]
> 若要成功地运行以下命令并创建服务主体，请确认你有权在租户中创建服务主体。
>

以下 CLI 命令将在 Azure 环境中设置 ACI SP：

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

运行此命令后返回的输出应显示一个已设置了“displayName”：“Azure 容器实例服务”的服务主体。

如果无法成功创建服务主体，请执行以下操作：
* 确认你在租户中有权执行此操作
* 进行检查，看租户中是否存在服务主体，以便部署到 ACI。 为此，可以运行 `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` 并改用该服务主体

### <a name="create-a-key-vault-resource"></a>创建 Key Vault 资源

使用 [Azure 门户](../key-vault/secrets/quick-create-portal.md#create-a-vault)、[CLI](../key-vault/secrets/quick-create-cli.md) 或 [PowerShell](../key-vault/secrets/quick-create-powershell.md) 创建一个 Azure Key Vault。 

对于 Key Vault 的属性，请使用以下指导原则： 
* 姓名：必须提供唯一的名称。 
* 订阅：选择订阅。
* 在“资源组”下，选择现有的资源组，或创建新的资源组并输入资源组名称。
* 在“位置”下拉菜单中选择一个位置。
* 可将其他选项保留默认值，或者根据其他要求进行选择。

> [!IMPORTANT]
> 使用客户管理的密钥加密 ACI 部署模板时，建议对 Key Vault 设置以下两个属性：“软删除”和“不清除”。 默认不会启用这些属性，但可以使用 PowerShell 或 Azure CLI 对新的或现有的 Key Vault 启用。

### <a name="generate-a-new-key"></a>生成新密钥 

创建 Key Vault 后，在 Azure 门户中导航到该资源。 在资源边栏选项卡的左侧导航菜单中的“设置”下，单击“密钥”。  在“密钥”的视图中，单击“生成/导入”以生成新密钥。 对此密钥使用任何唯一名称，并根据要求设置任何其他首选项。 

![生成新密钥](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>设置访问策略

创建新的访问策略，以允许 ACI 服务访问你的密钥。

* 生成密钥后，返回到 Key Vault 资源边栏选项卡，在“设置”下单击“访问策略”。 
* 在 Key Vault 的“访问策略”页上，单击“添加访问策略”。 
* 设置“密钥权限”以包括“获取”和“解包密钥”![设置密钥权限](./media/container-instances-encrypt-data/set-key-permissions.png)   
* 对于“选择主体”，请选择“Azure 容器实例服务”  
* 在底部单击“添加”  

现在，该访问策略应会显示在 Key Vault 的访问策略中。

![新访问策略](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>修改 JSON 部署模板

> [!IMPORTANT]
> 当前正在推出的最新 API 版本 (2019-12-01) 中提供了使用客户管理的密钥加密部署数据的功能。请在部署模板中指定此 API 版本。 如果在执行此操作时遇到任何问题，请联系 Azure 支持部门。

设置 Key Vault 密钥和访问策略后，将以下属性添加到 ACI 部署模板。 若要详细了解如何使用模板来部署 ACI 资源，请参阅[教程：使用资源管理器模板部署多容器组](./container-instances-multi-container-group.md)。 
* 在 `resources` 下，将 `apiVersion` 设置为 `2019-12-01`。
* 在部署模板的容器组 properties 节下，添加包含以下值的 `encryptionProperties`：
  * `vaultBaseUrl`：Key Vault 的 DNS 名称，可在门户中 Key Vault 资源的概览边栏选项卡上找到。
  * `keyName`：前面生成的密钥的名称
  * `keyVersion`：密钥的当前版本。 单击密钥本身可找到此值（在 Key Vault 资源的“设置”部分的“密钥”下）
* 在容器组 properties 节下，添加值为 `Standard` 的 `sku` 属性。 在 API 版本 2019-12-01 中，`sku` 属性是必需的。

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

下面是一个完整的模板，改编自[教程：使用资源管理器模板部署多容器组](./container-instances-multi-container-group.md)。 

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

使用 [az group create][az-group-create] 命令创建资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az deployment group create][az-deployment-group-create] 命令部署模板。

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

将在几秒钟内收到来自 Azure 的初始响应。 部署完成后，所有与之相关且由 ACI 服务保存的数据将使用提供的密钥进行加密。

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group/#az-deployment-group-create
