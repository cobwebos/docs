---
title: Azure HDInsight 中的托管标识
description: 提供 Azure HDInsight 中托管标识的实现概述。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: e7be8fbf5f6c2c59e93d48729785dd34bae5955e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327375"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Azure HDInsight 中的托管标识

托管标识是在 Azure Active Directory (Azure AD) 中注册的标识，其凭据由 Azure 管理。 利用托管标识，无需在 Azure AD 中注册服务主体，也不需要维护证书等凭据。

可以在 Azure HDInsight 中使用托管标识，使群集能够访问 Azure AD 域服务、访问 Azure Key Vault，或访问 Azure Data Lake Storage Gen2 中的文件。

有两种类型的托管标识：用户分配的托管标识和系统分配的托管标识。 Azure HDInsight 使用用户分配的托管标识。 用户分配的托管标识是作为独立的 Azure 资源创建的，你可以将其分配给一个或多个 Azure 服务实例。 相比之下，系统分配的托管标识是在 Azure AD 中创建的，系统会自动在特定的 Azure 服务实例上直接启用它。 然后，系统分配的该托管标识的生存期将绑定到启用该托管标识的服务实例的生存期。

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight 托管标识的实现

在 Azure HDInsight 中，托管标识是在群集的每个节点上预配的。 但是，这些标识组件只可由 HDInsight 服务使用。 目前不支持使用 HDInsight 群集节点上安装的托管标识生成访问令牌。 对于某些 Azure 服务，托管标识是使用某个终结点实现的。使用该终结点，可以自行获取用来与其他 Azure 服务交互的访问令牌。

## <a name="create-a-managed-identity"></a>创建托管标识

可以通过以下任何方法创建托管标识：

* [Azure 门户](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure 资源管理器](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

托管标识的剩余配置步骤取决于使用该托管标识的方案。

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Azure HDInsight 中的托管标识方案

Azure HDInsight 中的多种方案都会使用托管标识。 有关详细的设置和配置说明，请参阅相关文档：

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [企业安全性套餐](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka 创建自己的密钥（BYOK）](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>后续步骤

* [什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)
