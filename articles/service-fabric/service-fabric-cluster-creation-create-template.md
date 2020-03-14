---
title: 创建 Azure Service Fabric 群集模板
description: 了解如何为 Service Fabric 群集创建资源管理器模板。 为客户端身份验证配置安全性、Azure Key Vault 和 Azure Active Directory (Azure AD)。
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: a00f2bc505acd89d9fb9488565b6235bf7d146ba
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79258871"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>创建 Service Fabric 群集 Resource Manager 模板

[Azure Service Fabric 群集](service-fabric-deploy-anywhere.md)是一组联网的虚拟机，可在其中部署和管理微服务。 在 Azure 中运行的 Service Fabric 群集是一种 Azure 资源，它是使用资源管理器进行部署、管理和监视的。  本文介绍了如何为在 Azure 中运行的 Service Fabric 群集创建资源管理器模板。  在模板完成后，可以[在 Azure 上部署群集](service-fabric-cluster-creation-via-arm.md)。

群集安全性是在首次设置群集时配置的，以后无法更改。 在设置群集之前，请阅读[Service Fabric 群集安全方案][service-fabric-cluster-security]。 在 Azure 中，Service Fabric 使用 x509 证书来保护群集及其终结点，对客户端进行身份验证以及对数据进行加密。 另外，还建议使用 Azure Active Directory 来保护对管理终结点的访问。 在创建群集之前，必须先创建 Azure AD 租户和用户。  有关详细信息，请阅读[设置 Azure AD 来对客户端进行身份验证](service-fabric-cluster-creation-setup-aad.md)。

在部署生产群集来运行生产工作负荷之前，请务必首先阅读[生产就绪情况核对清单](service-fabric-production-readiness-checklist.md)。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>创建 资源管理器模板
[GitHub 上的 Azure 示例](https://github.com/Azure-Samples/service-fabric-cluster-templates)中提供了示例资源管理器模板。 这些模板可用作群集模板的起点。

本文使用[5 节点安全群集][service-fabric-secure-cluster-5-node-1-nodetype]示例模板和模板参数。 将 *azuredeploy.json* 和 *azuredeploy.parameters.json* 下载到计算机并在你喜欢使用的文本编辑器中打开这两个文件。

> [!NOTE]
> 对于国家/地区云（Azure 政府、Azure 中国、Azure 德国），还应将以下 `fabricSettings` 添加到模板：`AADLoginEndpoint`、`AADTokenEndpointFormat` 和 `AADCertEndpointFormat`。

## <a name="add-certificates"></a>添加证书
通过引用包含证书密钥的密钥保管库将证书添加到群集 Resource Manager 模板。 在资源管理器模板参数文件 (*azuredeploy.parameters.json*) 中添加这些密钥保管库参数和值。

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>将所有证书都添加到虚拟机规模集 osProfile
必须在规模集资源 (Microsoft.Compute/virtualMachineScaleSets) 的 **osProfile** 节中配置在群集中安装的每个证书。 该操作会指示资源提供程序在 VM 上安装证书。 此安装包括群集证书和打算用于应用程序的任何应用程序安全证书：

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

### <a name="configure-the-service-fabric-cluster-certificate"></a>配置 Service Fabric 群集证书

必须在 Service Fabric 群集资源 (Microsoft.ServiceFabric/clusters) 和 Service Fabric 扩展为虚拟机规模集资源中的虚拟机规模集配置群集身份验证证书。 通过此安排，Service Fabric 资源提供程序便可以将该证书配置为用于群集身份验证及管理终结点的服务器身份验证。

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>将证书信息添加到虚拟机规模集资源

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>将证书信息添加到 Service Fabric 群集资源

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
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>添加 Azure AD 配置以使用 Azure AD 访问客户端

通过引用包含证书密钥的密钥保管库，将 Azure AD 配置添加到群集资源管理器模板。 在资源管理器模板参数文件 (*azuredeploy.parameters.json*) 中添加这些 Azure AD 参数和值。 

> [!NOTE]
> 在 Linux 上，必须先创建 Azure AD 租户和用户，然后才能创建群集。  有关详细信息，请阅读[设置 Azure AD 来对客户端进行身份验证](service-fabric-cluster-creation-setup-aad.md)。

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

## <a name="populate-the-parameter-file-with-the-values"></a>在参数文件中填充值

最后，使用 Key Vault 和 Azure AD PowerShell 命令的输出值填充参数文件。

如果打算使用 Azure Service Fabric 资源管理器 PowerShell 模块，则不需要填充群集证书信息。 如果希望让系统生成自签名证书来确保群集安全性，则只需将它们保留为空。 

> [!NOTE]
> 要让资源管理器模块拾取并填充这些空参数值，参数名称必须与以下名称匹配

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

如果使用的是应用程序证书或已上传到密钥保管库的现有群集，则需要获取并填充此信息。

资源管理器模块没有能力为你生成 Azure AD 配置，因此，如果计划将 Azure AD 用于客户端访问，你需要填充该配置。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

## <a name="test-your-template"></a>测试模板
运行以下 PowerShell 命令，使用参数文件测试资源管理器模板：

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

如果遇到问题并收到含义模糊的消息，请使用“-Debug”作为选项。

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

下图演示密钥保管库和 Azure AD 配置在 Resource Manager 模板中的作用。

![Resource Manager 依赖关系图][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>后续步骤
现在，你已有了用于群集的模板，请学习如何[将群集部署到 Azure](service-fabric-cluster-creation-via-arm.md)。  在部署生产群集之前，如果尚未阅读[生产就绪情况核对清单](service-fabric-production-readiness-checklist.md)，请阅读该内容。

若要了解本文中部署的资源的 JSON 语法和属性，请参阅：

* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
