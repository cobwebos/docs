---
title: 面向 Azure HDInsight 的客户管理密钥磁盘加密
description: 本文介绍如何使用 Azure 密钥保管库中自己的加密密钥来加密存储在 Azure HDInsight 群集中托管磁盘上的数据。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c0521f384a333c3054397fb0ec7c2ab907e54f67
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411752"
---
# <a name="customer-managed-key-disk-encryption"></a>客户管理的密钥磁盘加密

Azure HDInsight 支持客户管理的关键加密，用于托管磁盘和连接到 HDInsight 群集虚拟机的资源磁盘上的数据。 此功能允许您使用 Azure 密钥保管库来管理保护 HDInsight 群集上静态数据的加密密钥。

通过 Azure 存储服务加密 (SSE) 保护 HDInsight 中的所有托管磁盘。 默认情况下，这些磁盘上的数据使用 Microsoft 托管密钥进行加密。 如果为 HDInsight 启用客户管理的密钥，则可以为 HDInsight 提供加密密钥，以便使用 Azure 密钥保管库使用这些密钥并管理这些密钥。

本文档不解决存储在 Azure 存储帐户中的数据。 有关 Azure 存储加密的详细信息，请参阅[静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)。 群集可能具有一个或多个附加的 Azure 存储帐户，其中加密密钥也可以由 Microsoft 管理或客户管理，但加密服务不同。

## <a name="introduction"></a>简介

客户管理密钥加密是在群集创建期间处理的一步过程，无需额外费用。 需要做的就是将 HDInsight 注册为 Azure Key Vault 的托管标识，并在创建群集时添加加密密钥。

群集每个节点上的资源磁盘和托管磁盘都使用对称数据加密密钥 （DEK） 进行加密。 使用密钥保管库中的密钥加密密钥 (KEK) 保护 DEK。 加密和解密过程完全由 Azure HDInsight 处理。

如果在存储磁盘加密密钥的密钥保管库上启用密钥保管库防火墙，则必须将部署群集的区域的 HDInsight 区域资源提供程序 IP 地址添加到密钥保管库防火墙配置中。 这是必需的，因为 HDInsight 不是受信任的 Azure 密钥保管库服务。

可以使用 Azure 门户或 Azure CLI 安全地旋转密钥保管库中的密钥。 当密钥旋转时，HDInsight 群集将在几分钟内开始使用新密钥。 启用[软删除](../key-vault/key-vault-ovw-soft-delete.md)密钥保护功能，以防止勒索软件方案和意外删除。 不支持没有此保护功能的密钥保管库。

|群集类型 |OS 磁盘（托管磁盘） |数据磁盘（托管磁盘） |临时数据磁盘（本地 SSD） |
|---|---|---|---|
|卡夫卡，HBase，加速写入|[SSE 加密](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|SSE 加密 + 可选 CMK 加密|可选的 CMK 加密|
|所有其他群集（火花、交互式、Hadoop、HBase，无加速写入）|SSE 加密|空值|可选的 CMK 加密|

## <a name="get-started-with-customer-managed-keys"></a>客户托管密钥入门

要创建启用客户管理的启用密钥的 HDInsight 群集，我们将执行以下步骤：

1. 为 Azure 资源创建托管标识
1. 创建 Azure Key Vault
1. 创建密钥
1. 创建访问策略
1. 创建启用客户管理密钥的 HDInsight 群集
1. 旋转加密密钥

## <a name="create-managed-identities-for-azure-resources"></a>为 Azure 资源创建托管标识

创建用户分配的托管标识以对密钥保管库进行身份验证。

有关特定步骤[，请参阅创建用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 有关托管标识在 Azure HDInsight 中的工作方式的详细信息，请参阅[Azure HDInsight 中的托管标识](hdinsight-managed-identities.md)。 将托管标识资源 ID 添加到 Key Vault 访问策略时，请务必保存该 ID。

## <a name="create-azure-key-vault"></a>创建 Azure Key Vault

创建密钥保管库。 有关特定步骤，请参阅[创建 Azure 密钥保管库](../key-vault/quick-create-portal.md)。

HDInsight 仅支持 Azure Key Vault。 如果拥有自己的密钥保管库，则可以将密钥导入 Azure Key Vault。 请记住，密钥保管库必须启用**软删除**。 有关导入现有密钥的详细信息，请访问[关于密钥、机密和证书](../key-vault/about-keys-secrets-and-certificates.md)。

## <a name="create-key"></a>创建密钥

1. 从新的密钥保管库，导航到**设置** > **键** > **+ 生成/导入**。

    ![在 Azure Key Vault 中生成新密钥](./media/disk-encryption/create-new-key.png "在 Azure Key Vault 中生成新密钥")

1. 提供名称，然后选择 **"创建**"。 维护**RSA**的默认**密钥类型**。

    ![生成密钥名称](./media/disk-encryption/create-key.png "生成密钥名称")

1. 返回到 **"密钥"** 页时，请选择您创建的密钥。

    ![密钥保管库密钥列表](./media/disk-encryption/key-vault-key-list.png)

1. 选择要打开 **"密钥版本"页的版本**。 当您使用自己的密钥进行 HDInsight 群集加密时，您需要提供密钥 URI。 复制“密钥标识符”**** 并将其保存在某处，直到你准备好创建群集。

    ![获取密钥标识符](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>创建访问策略

1. 从新的密钥保管库，导航到**设置** > **访问策略** > **和添加访问策略**。

    ![创建新的 Azure Key Vault 访问策略](./media/disk-encryption/key-vault-access-policy.png)

1. 在 **"添加访问策略"** 页中，提供以下信息：

    |properties |说明|
    |---|---|
    |密钥权限|选择**获取**、**取消包装键**和**包装键**。|
    |秘密权限|选择**获取**、**设置**和**删除**。|
    |选择主体|选择您之前创建的用户分配的托管标识。|

    ![为 Azure Key Vault 访问策略设置“选择主体”](./media/disk-encryption/azure-portal-add-access-policy.png)

1. 选择 **添加** 。

1. 选择“保存”。 

    ![保存 Azure 密钥保管库访问策略](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>使用客户管理的关键磁盘加密创建群集

现在已准备好新建 HDInsight 群集。 客户管理的密钥只能在群集创建期间应用于新群集。 无法从客户管理的关键群集中删除加密，并且客户管理的密钥无法添加到现有群集。

### <a name="using-the-azure-portal"></a>使用 Azure 门户

在群集创建期间，提供完整的**密钥标识符**，包括密钥版本。 例如，`https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4` 。 还需要将托管标识分配给集群并提供密钥 URI。

![创建新群集](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>使用 Azure CLI

下面的示例演示如何使用 Azure CLI 创建启用磁盘加密的新 Apache Spark 群集。 有关详细信息，请参阅[Azure CLI az hdinsight 创建](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)。

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

## <a name="rotating-the-encryption-key"></a>旋转加密密钥

在某些情况下，您可能希望更改 HDInsight 群集在创建后使用的加密密钥。 这可以通过门户轻松。 对于此操作，群集必须同时访问当前密钥和预期的新密钥，否则旋转键操作将失败。

### <a name="using-the-azure-portal"></a>使用 Azure 门户

要旋转密钥，您需要基密钥保管库 URI。 完成此操作后，转到门户中的 HDInsight 群集属性部分，然后单击**磁盘加密密钥 URL**下的**更改密钥**。 输入新的密钥 URL 并提交以旋转密钥。

![旋转磁盘加密密钥](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>使用 Azure CLI

下面的示例演示如何旋转现有 HDInsight 群集的磁盘加密密钥。 有关详细信息，请参阅[Azure CLI az hdinsight 旋转磁盘加密密钥](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key)。

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板

要使用资源管理器模板使用客户托管密钥，请使用以下更改更新模板：

1. 在**azuredeploy.json**文件中，向资源"对象添加以下属性：

    ```json
       "diskEncryptionProperties":
         {
                 "vaultUri": "[parameters('diskEncryptionVaultUri')]",
                  "keyName": "[parameters('diskEncryptionKeyName')]",
                  "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
                   "msiResourceId": "[parameters('diskEncryptionMsiResourceId')]"
         }

1. In the **azuredeploy.parameters.json** file, add the following parameters. You can get the values of these parameters from the Key Vault URI and the managed Identity. For example, if you have the following URI and identity values,
    * Sample key vault URI: https://<KeyVault_Name>.vault.azure.net/keys/clusterkey/<Cluster_Key_Value>
    * Sample user-assigned managed identity: "/subscriptions/<subscriptionID>/resourcegroups/<ResourceGroup_Name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI_Name>

    The parameters in the **azuredeploy.parameters.json** file are:

    ```json
   "diskEncryptionVaultUri": {
            "value": "https://<KeyVault_Name>.vault.azure.net"
        },
        "diskEncryptionKeyName": {
            "value": "clusterkey"
        },
        "diskEncryptionKeyVersion": {
            "value": "<Cluster_Key_Value>"
        },
        "diskEncryptionMsiResourceId": {
            "value": "/subscriptions/<subscriptionID>/resourcegroups/<ResourceGroup_Name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI_Name>"
        }
    ```

## <a name="faq-for-customer-managed-key-encryption"></a>客户管理密钥加密常见问题解答

**HDInsight 群集如何访问我的密钥保管库？**

HDInsight 使用与 HDInsight 群集关联的托管标识访问 Azure 密钥保管库实例。 可以在群集创建之前或创建期间创建此托管标识。 还需要对存储密钥的密钥保管库的托管标识授予访问权限。

**此功能是否可用于 HDInsight 上的所有群集？**

除 Spark 2.1 和 2.2 外，所有群集类型都可用于客户管理密钥加密。

**我可以使用多个密钥来加密不同的磁盘或文件夹吗？**

否，所有托管磁盘和资源磁盘都使用相同的密钥加密。

**如果群集无法访问密钥保管库或密钥，会发生什么情况？**

如果群集无法访问密钥，警告将显示在 Apache Ambari 门户中。 在此状态下，"**更改密钥"** 操作将失败。 恢复密钥访问后，Ambari 警告将消失，并且可以成功执行密钥轮换等操作。

![密钥访问安巴里警报](./media/disk-encryption/ambari-alert.png)

**如果删除密钥，如何恢复群集？**

由于仅支持"软删除"启用的密钥，因此，如果在密钥保管库中恢复密钥，群集应重新获得对密钥的访问权限。 要恢复 Azure 密钥保管库密钥，请参阅[撤消-AzKeyVaultKey删除](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval)或[az 键保管库密钥恢复](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover)。

**哪些磁盘类型已加密？OS 磁盘/资源磁盘也加密吗？**

资源磁盘和数据/托管磁盘已加密。 操作系统磁盘未加密。

**如果扩展了群集，新节点是否无缝地支持客户管理的密钥？**

是的。 在纵向扩展期间，群集需要访问密钥保管库中的密钥。 同一密钥用于加密群集中的托管磁盘和资源磁盘。

**我的位置有客户管理的密钥吗？**

HDInsight 客户管理的关键在所有公共云和国家云中都有可用。

## <a name="next-steps"></a>后续步骤

* 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库](../key-vault/key-vault-overview.md)。
* [Azure HDInsight 中的企业安全性概述](./domain-joined/hdinsight-security-overview.md)。
