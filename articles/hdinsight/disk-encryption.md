---
title: 适用于 Azure HDInsight 的客户托管的密钥磁盘加密
description: 本文介绍如何使用 Azure Key Vault 中的加密密钥来加密存储在 Azure HDInsight 群集中托管磁盘上的数据。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2019
ms.openlocfilehash: b452cb986e6f662aeb33c2a475f18695ebc75745
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846059"
---
# <a name="customer-managed-key-disk-encryption"></a>客户管理的密钥磁盘加密

Azure HDInsight 支持客户托管的密钥，也称为创建自己的密钥（BYOK）对托管磁盘上的数据和附加到 HDInsight 群集 Vm 的资源磁盘进行加密。 此功能允许你使用 Azure Key Vault 来管理保护 HDInsight 群集上静态数据的加密密钥。 群集可能有一个或多个附加的 Azure 存储帐户，其中的加密密钥也可以是 Microsoft 管理的或客户管理的，但加密服务有所不同。

本文档不会对存储在 Azure 存储帐户中的数据进行寻址。 有关 ASE 的详细信息，请参阅[静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)。

通过 Azure 存储服务加密 (SSE) 保护 HDInsight 中的所有托管磁盘。 默认情况下，这些磁盘上的数据使用 Microsoft 托管密钥进行加密。 如果为 HDInsight 启用客户管理的密钥，则需要提供 HDInsight 的加密密钥，以便使用 Azure Key Vault 来使用和管理这些密钥。

客户托管的密钥加密是在群集创建过程中处理的单步过程，无需额外付费。 需要做的就是将 HDInsight 注册为 Azure Key Vault 的托管标识，并在创建群集时添加加密密钥。

群集的每个节点上的资源磁盘和托管磁盘都使用对称数据加密密钥（DEK）进行加密。 使用密钥保管库中的密钥加密密钥 (KEK) 保护 DEK。 加密和解密过程完全由 Azure HDInsight 处理。

可以使用 Azure 门户或 Azure CLI 安全地旋转密钥保管库中的密钥。 当密钥旋转时，HDInsight 群集会在数分钟内开始使用新密钥。 启用 "软删除" 密钥保护功能来防止勒索软件方案和意外删除。 不支持不具有此保护功能的密钥保管库。

## <a name="get-started-with-customer-managed-keys"></a>客户托管密钥入门

若要创建已启用客户托管密钥的 HDInsight 群集，请完成以下步骤：

1. 创建 Azure 资源的托管标识
2. 设置 Azure Key Vault 和密钥
3. 创建启用了客户托管密钥的 HDInsight 群集
4. 旋转加密密钥

## <a name="create-managed-identities-for-azure-resources"></a>创建 Azure 资源的托管标识

若要对 Key Vault 进行身份验证，请使用[Azure 门户](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)、 [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)、 [Azure 资源管理器](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)或[Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)创建用户分配的托管标识。 有关 Azure HDInsight 中托管标识的工作方式的详细信息，请参阅[Azure hdinsight 中的托管标识](hdinsight-managed-identities.md)。 将托管标识资源 ID 添加到 Key Vault 访问策略时，请务必保存该 ID。

## <a name="set-up-the-key-vault-and-keys"></a>设置 Key Vault 和密钥

HDInsight 仅支持 Azure Key Vault。 如果拥有自己的密钥保管库，则可以将密钥导入 Azure Key Vault。 请记住，密钥必须具有 "软删除"。 "软删除" 功能通过 REST、.NET/C#、PowerShell 和 Azure CLI 接口提供。

1. 若要创建新的密钥保管库，请按照 [Azure Key Vault](../key-vault/key-vault-overview.md) 快速入门进行操作。 有关导入现有密钥的详细信息，请访问[关于密钥、机密和证书](../key-vault/about-keys-secrets-and-certificates.md)。

1. 使用[az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) CLI 命令对密钥保管库启用 "软删除"。

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. 创建密钥。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 若要创建新密钥，请从“设置”下的“密钥”菜单中选择“生成/导入”。

    ![在 Azure Key Vault 中生成新密钥](./media/disk-encryption/create-new-key.png "在 Azure Key Vault 中生成新密钥")

    b.保留“数据库类型”设置，即设置为“共享”。 将“选项”设置为“生成”并提供密钥名称。

    ![生成密钥名称](./media/disk-encryption/create-key.png "生成密钥名称")

    c. 选择从密钥列表中创建的密钥。

    ![key vault 密钥列表](./media/disk-encryption/key-vault-key-list.png)

    d.单击“下一步”。 使用自己的密钥进行 HDInsight 群集加密时，需要提供密钥 URI。 复制“密钥标识符”并将其保存在某处，直到你准备好创建群集。

    ![获取密钥标识符](./media/disk-encryption/get-key-identifier.png)

1. 将托管标识添加到密钥保管库访问策略。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 创建新的 Azure Key Vault 访问策略。

    ![创建新的 Azure Key Vault 访问策略](./media/disk-encryption/add-key-vault-access-policy.png)

    b.保留“数据库类型”设置，即设置为“共享”。 在“选择主体”下，选择你创建的用户分配的托管标识。

    ![为 Azure Key Vault 访问策略设置“选择主体”](./media/disk-encryption/azure-portal-add-access-policy.png)

    c. 将“密钥权限”设置为“获取”、“解包密钥”和“包装密钥”。

    ![设置 Azure Key Vault 访问 policy1 的密钥权限](./media/disk-encryption/add-key-vault-access-policy-keys.png "设置 Azure Key Vault 访问 policy1 的密钥权限")

    d.单击“下一步”。 将“机密权限”设置为“获取”、“设置”和“删除”。

    ![设置 Azure Key Vault 访问 policy2 的密钥权限](./media/disk-encryption/add-key-vault-access-policy-secrets.png "设置 Azure Key Vault 访问 policy2 的密钥权限")

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 选择“保存”。

    ![保存 Azure Key Vault 访问策略](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>通过客户托管的密钥磁盘加密创建群集

现在已准备好新建 HDInsight 群集。 客户托管的密钥只能在群集创建过程中应用于新群集。 无法从客户管理的密钥群集中删除加密，且不能将客户托管的密钥添加到现有群集。

### <a name="using-the-azure-portal"></a>使用 Azure 门户

在群集创建期间，提供完整的密钥 URL，包括密钥版本。 例如，`https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4` 。 还需要将托管标识分配给集群并提供密钥 URI。

![创建新群集](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>使用 Azure CLI

下面的示例演示如何使用 Azure CLI 来创建启用了磁盘加密的新 Apache Spark 群集。 有关详细信息，请参阅[Azure CLI az hdinsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)文档。

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

在某些情况下，你可能想要更改 HDInsight 群集在创建 HDInsight 群集时使用的加密密钥。 这可以通过门户轻松实现。 对于此操作，群集必须具有对当前密钥和预期新密钥的访问权限，否则旋转键操作将失败。

### <a name="using-the-azure-portal"></a>使用 Azure 门户

若要轮换密钥，必须具有新密钥的完整 url （请参阅[设置 Key Vault 和密钥](#set-up-the-key-vault-and-keys)的步骤3）。 完成此操作后，转到门户中的 "HDInsight 群集属性" 部分，并单击 "**磁盘加密密钥 URL**" 下的 "**更改密钥**"。 输入新的密钥 url，并提交以旋转密钥。

![旋转磁盘加密密钥](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>使用 Azure CLI

下面的示例演示如何轮换现有 HDInsight 群集的磁盘加密密钥。 有关更多详细信息，请参阅[Azure CLI az hdinsight 轮换磁盘加密密钥](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key)。

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>客户托管的密钥加密的常见问题

**HDInsight 群集如何访问我的密钥保管库？**

HDInsight 使用与 HDInsight 群集关联的托管标识来访问你的 Azure Key Vault 实例。 可以在群集创建之前或创建期间创建此托管标识。 还需要对存储密钥的密钥保管库的托管标识授予访问权限。

**此功能是否可用于 HDInsight 上的所有群集？**

客户托管的密钥加密适用于除 Spark 2.1 和2.2 之外的所有群集类型。

**是否可以使用多个密钥来加密不同的磁盘或文件夹？**

不可以，所有托管磁盘和资源磁盘都由相同的密钥加密。

**如果群集失去对密钥保管库或密钥的访问权限，会发生什么情况？**

如果群集失去对密钥的访问权限，则会在 Apache Ambari 门户中显示警告。 在此状态下，**更改密钥**操作将失败。 恢复密钥访问权限后，Ambari 警告将消失，可以成功执行密钥轮换等操作。

![密钥访问 Ambari 警报](./media/disk-encryption/ambari-alert.png)

**如果删除密钥，如何恢复群集？**

由于仅支持 "软删除" 启用的密钥，因此，如果密钥在 key vault 中恢复，则群集应重新获得对密钥的访问权限。 若要恢复 Azure Key Vault 密钥，请参阅[AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval)或[az-keyvault-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover)。

**加密哪些磁盘类型？OS 磁盘/资源磁盘是否也进行了加密？**

资源磁盘和数据/托管磁盘已加密。 OS 磁盘未加密。

**如果对群集进行扩展，则新节点是否可以无缝地支持客户管理的密钥？**

可以。 在纵向扩展期间，群集需要访问密钥保管库中的密钥。 使用相同的密钥来加密群集中的托管磁盘和资源磁盘。

**客户管理的密钥在我的位置中是否可用？**

HDInsight 客户管理的密钥在所有公有云和全国云中都可用。

## <a name="next-steps"></a>后续步骤

* [Azure HDInsight 中的企业安全性概述](./domain-joined/hdinsight-security-overview.md)
