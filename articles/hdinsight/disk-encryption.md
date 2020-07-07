---
title: Azure HDInsight 的客户管理的密钥磁盘加密
description: 本文介绍如何使用 Azure Key Vault 中你自己的加密密钥来加密 Azure HDInsight 群集中托管磁盘上存储的数据。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 732709dbcb5ebe54025a963379128f1a1e74183e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "81536295"
---
# <a name="customer-managed-key-disk-encryption"></a>客户管理的密钥磁盘加密

Azure HDInsight 支持使用客户管理的密钥，来加密附加到 HDInsight 群集虚拟机的托管磁盘和资源磁盘上的数据。 借助此功能，可以使用 Azure Key Vault 来管理用于保护 HDInsight 群集上的静态数据的加密密钥。

通过 Azure 存储服务加密 (SSE) 保护 HDInsight 中的所有托管磁盘。 默认情况下，这些磁盘上的数据使用 Microsoft 托管密钥进行加密。 如果为 HDInsight 启用客户管理的密钥，请提供加密密钥，使 HDInsight 能够通过 Azure Key Vault 来使用和管理这些密钥。

本文档不会探讨 Azure 存储帐户中存储的数据。 有关 Azure 存储加密的详细信息，请参阅[静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)。 群集中可能附加了一个或多个 Azure 存储帐户，其中的加密密钥可能是由 Microsoft 托管的或者客户自己管理的，但加密服务是不同的。

## <a name="introduction"></a>简介

客户管理的密钥加密是在群集创建期间处理的单步骤过程，不收取额外的费用。 需要做的就是将 HDInsight 注册为 Azure Key Vault 的托管标识，并在创建群集时添加加密密钥。

群集每个节点上的资源磁盘和托管磁盘均已使用对称数据加密密钥 (DEK) 进行加密。 使用密钥保管库中的密钥加密密钥 (KEK) 保护 DEK。 加密和解密过程完全由 Azure HDInsight 处理。

如果在存储磁盘加密密钥的密钥保管库上启用密钥保管库防火墙，则必须将用于部署群集的区域的 HDInsight 区域资源提供程序 IP 地址添加到密钥保管库防火墙配置。 这是必需的，因为 HDInsight 不是受信任的 Azure 密钥保管库服务。

可以使用 Azure 门户或 Azure CLI 安全地旋转密钥保管库中的密钥。 轮换密钥时，HDInsight 群集在几分钟内即可开始使用新密钥。 启用[软删除](../key-vault/general/overview-soft-delete.md)密钥保护功能可以防范勒索软件和意外删除。 未启用此项保护功能的 Key Vault 不受支持。

|群集类型 |OS 磁盘（托管磁盘） |数据磁盘（托管磁盘） |临时数据磁盘（本地 SSD） |
|---|---|---|---|
|Kafka、有加速写入的 HBase|[SSE 加密](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|SSE 加密 + 可选 CMK 加密|可选 CMK 加密|
|所有其他群集（Spark、Interactive、Hadoop、无加速写入的 HBase）|SSE 加密|空值|可选 CMK 加密|

## <a name="get-started-with-customer-managed-keys"></a>客户托管密钥入门

若要创建已启用客户管理的密钥的 HDInsight 群集，请完成以下步骤：

1. 创建 Azure 资源的托管标识
1. 创建 Azure Key Vault
1. 创建密钥
1. 创建访问策略
1. 创建已启用客户管理的密钥的 HDInsight 群集
1. 转换加密密钥

## <a name="create-managed-identities-for-azure-resources"></a>创建 Azure 资源的托管标识

创建用户分配的托管标识，以便向 Key Vault 进行身份验证。

有关具体步骤，请参阅[创建用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 有关 Azure HDInsight 中托管标识的工作原理的详细信息，请参阅 [Azure HDInsight 中的托管标识](hdinsight-managed-identities.md)。 将托管标识资源 ID 添加到 Key Vault 访问策略时，请务必保存该 ID。

## <a name="create-azure-key-vault"></a>创建 Azure Key Vault

创建密钥保管库。 有关具体步骤，请参阅[创建 Azure Key Vault](../key-vault/secrets/quick-create-portal.md)。

HDInsight 仅支持 Azure Key Vault。 如果拥有自己的密钥保管库，则可以将密钥导入 Azure Key Vault。 请记住，密钥保管库必须启用“软删除”****。 有关导入现有密钥的详细信息，请访问[关于密钥、机密和证书](../key-vault/about-keys-secrets-and-certificates.md)。

## <a name="create-key"></a>创建密钥

1. 在新密钥保管库中，导航到“设置” > “密钥” > “生成/导入”。**** **** ****

    ![在 Azure Key Vault 中生成新密钥](./media/disk-encryption/create-new-key.png "在 Azure Key Vault 中生成新密钥")

1. 提供名称，然后选择“创建”。**** 保留默认**密钥类型** **RSA**。

    ![生成密钥名称](./media/disk-encryption/create-key.png "生成密钥名称")

1. 返回到“密钥”**** 页时，选择创建的密钥。

    ![Key Vault 密钥列表](./media/disk-encryption/key-vault-key-list.png)

1. 选择要打开“密钥版本”**** 页的版本。 使用自己的密钥加密 HDInsight 群集时，需要提供密钥 URI。 复制“密钥标识符”**** 并将其保存在某处，直到你准备好创建群集。

    ![获取密钥标识符](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>创建访问策略

1. 在新密钥保管库中，导航到“设置” > “访问策略” > “+ 添加访问策略”。**** **** ****

    ![创建新的 Azure Key Vault 访问策略](./media/disk-encryption/key-vault-access-policy.png)

1. 在“添加访问策略”页中提供以下信息：****

    |属性 |说明|
    |---|---|
    |密钥权限|选择****“获取”****、“解包密钥”**** 和“包装密钥”。|
    |机密权限|选择“获取”、“设置”和“删除”。**** **** ****|
    |选择主体|选择前面创建的用户分配的托管标识。|

    ![为 Azure Key Vault 访问策略设置“选择主体”](./media/disk-encryption/azure-portal-add-access-policy.png)

1. 选择“添加”  ****。

1. 选择“保存” ****。

    ![保存 Azure Key Vault 访问策略](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>创建支持客户管理的密钥磁盘加密的群集

现在已准备好新建 HDInsight 群集。 客户管理的密钥只能在群集创建期间应用于新群集。 无法从客户管理的密钥群集中删除加密，无法将客户管理的密钥添加到现有群集。

### <a name="using-the-azure-portal"></a>使用 Azure 门户

在群集创建期间，提供完整的“密钥标识符”****，包括密钥版本。 例如，`https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`。 还需要将托管标识分配给集群并提供密钥 URI。

![创建新群集](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>使用 Azure CLI

以下示例演示如何使用 Azure CLI 来创建已启用磁盘加密的新 Apache Spark 群集。 有关详细信息，请参阅 [Azure CLI az hdinsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)。

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板

以下示例演示如何使用 Azure 资源管理器模板来创建已启用磁盘加密的新 Apache Spark 群集。 有关详细信息，请参阅[什么是 ARM 模板？](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)。

此示例使用 PowerShell 调用模板。

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

资源管理模板 `azuredeploy.json` 的内容：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

## <a name="rotating-the-encryption-key"></a>转换加密密钥

在某些情况下，在创建 HDInsight 群集后，你可能想要更改它使用的加密密钥。 可以通过门户轻松实现此目的。 对于此操作，群集必须有权访问当前密钥和所需的新密钥，否则轮换密钥操作将会失败。

### <a name="using-the-azure-portal"></a>使用 Azure 门户

若要轮换密钥，需要基密钥保管库 URI。 完成此操作后，转到门户中的“HDInsight 群集属性”部分，单击“磁盘加密密钥 URL”下的“更改密钥”。**** **** 输入新密钥的 URL，并提交轮换密钥的操作。

![轮换磁盘加密密钥](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>使用 Azure CLI

以下示例演示如何轮换现有 HDInsight 群集的磁盘加密密钥。 有关详细信息，请参阅 [Azure CLI az hdinsight rotate-disk-encryption-key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key)。

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>客户管理的密钥加密的常见问题解答

**HDInsight 群集如何访问我的 Key Vault？**

HDInsight 使用与 HDInsight 群集关联的托管标识来访问 Azure Key Vault 实例。 可以在群集创建之前或创建期间创建此托管标识。 还需要对存储密钥的密钥保管库的托管标识授予访问权限。

**此功能是否适用于 HDInsight 上的所有群集？**

客户管理的密钥加密适用于除 Spark 2.1 和 2.2 以外的其他所有群集类型。

**是否可以使用多个密钥来加密不同的磁盘或文件夹？**

不可以，所有托管磁盘和资源磁盘由同一个密钥加密。

**如果群集失去了对 Key Vault 或密钥的访问权限，会发生什么情况？**

如果群集失去了对密钥的访问权限，Apache Ambari 门户中会显示警告。 在此状态下，“更改密钥”操作将会失败。**** 恢复密钥访问权限后，Ambari 警告将会消失，密钥轮换等操作可以成功执行。

![密钥访问 Ambari 警报](./media/disk-encryption/ambari-alert.png)

**如果删除密钥，如何恢复群集？**

由于仅支持已启用“软删除”的密钥，因此，如果 Key Vault 中的密钥已恢复，则群集应重新获得对密钥的访问权限。 若要恢复 Azure Key Vault 密钥，请参阅 [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) 或 [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover)。

**会加密哪些磁盘类型？是否还会加密 OS 磁盘/资源磁盘？**

会加密资源磁盘和数据磁盘/托管磁盘。 OS 磁盘不加密。

**如果群集已纵向扩展，新节点是否无缝支持客户管理的密钥？**

是的。 在纵向扩展期间，群集需要访问密钥保管库中的密钥。 将使用同一个密钥来加密群集中的托管磁盘和资源磁盘。

**是否可以在我的位置使用客户管理的密钥？**

可以在所有公有云和国家云中使用 HDInsight 客户管理的密钥。

## <a name="next-steps"></a>后续步骤

* 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault](../key-vault/general/overview.md)。
* [Azure HDInsight 中的企业安全性概述](./domain-joined/hdinsight-security-overview.md)。
