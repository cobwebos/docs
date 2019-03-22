---
title: 为 Azure HDInsight 上的 Apache Kafka 创建自己的密钥（预览版）
description: 本文介绍如何使用你在 Azure Key Vault 中的密钥加密 Azure HDInsight 上的 Apache Kafka 中存储的数据。
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 26e4b921b4050efa5217e3b599b9dc942a003090
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58173917"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight-preview"></a>为 Azure HDInsight 上的 Apache Kafka 创建自己的密钥（预览版）

Azure HDInsight 包括为 Apache Kafka 提供创建自己的密钥 (BYOK) 支持。 借助此功能，你可以拥有和管理用于加密静态数据的密钥。 

通过 Azure 存储服务加密 (SSE) 保护 HDInsight 中的所有托管磁盘。 默认情况下，这些磁盘上的数据使用 Microsoft 托管密钥进行加密。 如果启用 BYOK，则会为 HDInsight 提供加密密钥，以便通过 Azure Key Vault 使用该密钥并对其进行管理。 

BYOK 加密是在群集创建期间处理的一步过程，无需额外费用。 需要做的就是将 HDInsight 注册为 Azure Key Vault 的托管标识，并在创建群集时添加加密密钥。

所有发送到 Kafka 群集的消息（包括由 Kafka 维护的副本）都使用对称数据加密密钥 (DEK) 进行加密。 使用密钥保管库中的密钥加密密钥 (KEK) 保护 DEK。 加密和解密过程完全由 Azure HDInsight 处理。 

可以使用 Azure 门户或 Azure CLI 安全地旋转密钥保管库中的密钥。 当密钥旋转时，HDInsight Kafka 群集会在几分钟内开始使用新密钥。 启用“不清除”和“软删除”密钥保护功能，以防止出现勒索软件情况和意外删除。 不支持没有这些保护功能的密钥。

## <a name="get-started-with-byok"></a>BYOK 入门

1. 创建 Azure 资源的托管标识。

   若要向密钥保管库进行身份验证，使用以下命令创建用户分配托管的标识[Azure 门户](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)， [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)， [Azure 资源管理器](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)，或[Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)。 有关如何将托管在 Azure HDInsight 中的标识工作的详细信息，请参阅[托管在 Azure HDInsight 中的标识](../hdinsight-managed-identities.md)。 虽然托管标识和适用于 Kafka 的 BYOK 需要 Azure Active Directory，但不要求使用企业安全性套餐 (ESP)。 将托管标识资源 ID 添加到 Key Vault 访问策略时，请务必保存该 ID。

   ![在 Azure 门户中创建用户分配的托管标识](./media/apache-kafka-byok/user-managed-identity-portal.png)

2. 导入现有密钥保管库或新建一个。

   HDInsight 仅支持 Azure Key Vault。 如果拥有自己的密钥保管库，则可以将密钥导入 Azure Key Vault。 请记住，密钥必须启用“软删除”和“不清除”。 “软删除”和“不清除”功能通过 REST、.NET/C#、PowerShell 和 Azure CLI 接口提供。

   若要创建新的密钥保管库，请按照 [Azure Key Vault](../../key-vault/key-vault-overview.md) 快速入门进行操作。 有关导入现有密钥的详细信息，请访问[关于密钥、机密和证书](../../key-vault/about-keys-secrets-and-certificates.md)。

   若要创建新密钥，请从“设置”下的“密钥”菜单中选择“生成/导入”。

   ![在 Azure Key Vault 中生成新密钥](./media/apache-kafka-byok/kafka-create-new-key.png)

   将“选项”设置为“生成”并提供密钥名称。

   ![在 Azure Key Vault 中生成新密钥](./media/apache-kafka-byok/kafka-create-a-key.png)

   选择从密钥列表中创建的密钥。

   ![Azure Key Vault 密钥列表](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

   当你为 Kafka 群集加密使用自己的密钥时，需要提供密钥 URI。 复制“密钥标识符”并将其保存在某处，直到你准备好创建群集。

   ![复制密钥标识符](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
3. 将托管标识添加到密钥保管库访问策略。

   创建新的 Azure Key Vault 访问策略。

   ![创建新的 Azure Key Vault 访问策略](./media/apache-kafka-byok/add-key-vault-access-policy.png)

   在“选择主体”下，选择你创建的用户分配的托管标识。

   ![为 Azure Key Vault 访问策略设置“选择主体”](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

   将“密钥权限”设置为“获取”、“解包密钥”和“包装密钥”。

   ![设置 Azure Key Vault 访问策略的密钥权限](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

   将“机密权限”设置为“获取”、“设置”和“删除”。

   ![设置 Azure Key Vault 访问策略的密钥权限](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

4. 创建 HDInsight 群集

   现在已准备好新建 HDInsight 群集。 BYOK 只能在群集创建期间应用于新群集。 无法从 BYOK 群集中删除加密，并且无法将 BYOK 添加到现有群集。

   ![Azure 门户中的 Kafka 磁盘加密](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   在群集创建期间，提供完整的密钥 URL，包括密钥版本。 例如，`https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`。 还需要将托管标识分配给集群并提供密钥 URI。

## <a name="faq-for-byok-to-apache-kafka"></a>Apache Kafka BYOK 的常见问题解答

**Kafka 群集如何访问我的密钥保管库？**

   在群集创建期间，将托管标识与 HDInsight Kafka 群集相关联。 可以在群集创建之前或创建期间创建此托管标识。 还需要对存储密钥的密钥保管库的托管标识授予访问权限。

**此功能是否适用于 HDInsight 上的所有 Kafka 群集？**

   BYOK 加密仅适用于 Kafka 1.1 及更高版本的群集。

**是否可以为不同的主题/分区使用不同的密钥？**

   不可以，群集中的所有托管磁盘均使用相同的密钥加密。

**如果删除密钥，如何恢复群集？**

   由于仅支持“软删除”启用密钥，因此，如果密钥保管库中的密钥已恢复，则群集应重新获得对密钥的访问权限。 若要还原 Azure Key Vault 密钥，请参阅 [Restore-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)。

**是否可以让生成方/使用者应用程序同时使用 BYOK 集群和非 BYOK 集群？**

   是的。 使用 BYOK 对生成方/使用者应用程序没有任何影响。 加密发生在操作系统层。 无需对现有的生成方/使用者 Kafka 应用程序进行任何更改。

**是否还会加密操作系统磁盘/资源磁盘？**

   不是。 不会加密操作系统磁盘和资源磁盘。

**如果纵向扩展群集，新代理是否会无缝支持 BYOK？**

   是的。 在纵向扩展期间，群集需要访问密钥保管库中的密钥。 相同的密钥用于加密群集中的所有托管磁盘。

**我的位置是否可以使用 BYOK？**

   Kafka BYOK 适用于所有公共云。

## <a name="next-steps"></a>后续步骤

* 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](../../key-vault/key-vault-whatis.md)
* 若要开始使用 Azure Key Vault，请参阅 [Azure Key Vault 入门](../../key-vault/key-vault-overview.md)。
