---
title: Azure HDInsight 中的托管标识
description: 提供 Azure HDInsight 中托管标识的实现概述。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 1081865a2e138af38ba171197719f08dedf6ffdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408936"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Azure HDInsight 中的托管标识

托管标识是在 Azure Active Directory (Azure AD) 中注册的标识，其凭据由 Azure 管理。 使用托管标识时，无需在 Azure AD 中注册服务主体。 或维护凭据（如证书）。

Azure HDInsight 中托管标识用于访问 Azure AD 域服务或在需要时访问 Azure 数据湖存储 Gen2 中的文件。

有两种类型的托管标识：用户分配的托管标识和系统分配的托管标识。 Azure HDInsight 仅支持用户分配的托管标识。 HDInsight 不支持系统分配的托管标识。 用户分配的托管标识创建为独立的 Azure 资源，然后您可以将其分配给一个或多个 Azure 服务实例。 相比之下，系统分配的托管标识是在 Azure AD 中创建的，系统会自动在特定的 Azure 服务实例上直接启用它。 然后，系统分配的该托管标识的生存期将绑定到启用该托管标识的服务实例的生存期。

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight 托管标识的实现

在 Azure HDInsight 中，托管标识是在群集的每个节点上预配的。 但是，这些标识组件只可由 HDInsight 服务使用。 目前没有支持使用 HDInsight 群集节点上安装的托管标识生成访问令牌的方法。 对于某些 Azure 服务，托管标识使用可用于获取访问令牌的终结点实现。 使用令牌自行与其他 Azure 服务进行交互。

## <a name="create-a-managed-identity"></a>创建托管标识

可以使用以下任何方法创建托管标识：

* [Azure 门户](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure 资源管理器](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

托管标识的剩余配置步骤取决于使用该托管标识的方案。

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Azure HDInsight 中的托管标识方案

Azure HDInsight 中的多种方案都会使用托管标识。 有关详细的设置和配置说明，请参阅相关文档：

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [企业安全包](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [客户管理的密钥磁盘加密](disk-encryption.md)

## <a name="faq"></a>常见问题解答

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>如果在群集创建后删除托管标识，会发生什么情况？

当需要托管标识时，群集将遇到问题。 创建群集后，当前无法更新或更改托管标识。 因此，我们的建议是确保托管标识在群集运行时不会被删除。 或者，您可以重新创建群集并分配新的托管标识。

## <a name="next-steps"></a>后续步骤

* [什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)
