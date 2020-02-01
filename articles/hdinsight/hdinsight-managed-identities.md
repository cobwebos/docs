---
title: Azure HDInsight 中的托管标识
description: 概述如何实现 Azure HDInsight 中的托管标识。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: 236f79c9060a0d6fdcb0f558373d02f32eba7abb
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905601"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Azure HDInsight 中的托管标识

托管标识是在 Azure Active Directory （Azure AD）中注册的标识，其凭据由 Azure 管理。 利用托管标识，无需在 Azure AD 中注册服务主体，也不需要维护证书等凭据。

托管标识在 Azure HDInsight 中用于访问 Azure AD 域服务，或在需要时访问 Azure Data Lake Storage Gen2 中的文件。

有两种类型的托管标识：用户分配的和系统分配的标识。 Azure HDInsight 使用用户分配的托管标识。 用户分配的托管标识是作为独立的 Azure 资源创建的，你可以将其分配给一个或多个 Azure 服务实例。 与此相反，系统分配的托管标识在 Azure AD 中创建，然后在特定的 Azure 服务实例上自动启用。 系统分配的托管标识的生命周期将与启用它的服务实例的生存期相关联。

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight 托管标识实现

在 Azure HDInsight 中，托管标识在群集的每个节点上进行设置。 不过，这些标识组件只可供 HDInsight 服务使用。 目前不支持使用 HDInsight 群集节点上安装的托管标识生成访问令牌。 对于某些 Azure 服务，托管标识是通过终结点实现的，你可以使用该终结点获取访问令牌，以便自行与其他 Azure 服务交互。

## <a name="create-a-managed-identity"></a>创建托管标识

可以通过以下任何方法创建托管标识：

* [Azure 门户](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure 资源管理器](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

配置托管标识的其余步骤取决于将使用该标识的方案。

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Azure HDInsight 中的托管标识方案

托管标识在多个方案中用于 Azure HDInsight 中。 有关详细的设置和配置说明，请参阅相关文档：

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [企业安全性套餐](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka 创建自己的密钥（BYOK）](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="faq"></a>常见问题
### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>如果在创建群集后删除托管标识，会发生什么情况？
需要托管标识时，群集将会遇到问题。 创建群集后，当前没有办法更新或更改 "管理标识"。 建议确保在群集运行时不删除托管标识。 或者，你可以重新创建群集并分配一个新的托管标识。

## <a name="next-steps"></a>后续步骤

* [什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)
