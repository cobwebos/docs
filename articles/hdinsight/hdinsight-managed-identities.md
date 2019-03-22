---
title: 在 Azure HDInsight 中管理的标识
description: 概述在 Azure HDInsight 中管理的标识的实现。
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: hrasheed
ms.openlocfilehash: 9386bcb8e455bff5ceed1fccdf55874caf7b6507
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58175772"
---
# <a name="managed-identities-in-azure-hdinsight"></a>在 Azure HDInsight 中管理的标识

托管的标识是 Azure Active Directory，(Azure AD) 的凭据由 Azure 管理中注册的标识。 管理的标识，不需要在 Azure AD 中注册服务主体或维护等证书凭据。

可以在 Azure HDInsight 中使用托管的标识，以允许群集访问 Azure AD 域服务、 访问 Azure 密钥保管库，或访问 Azure 数据湖存储第 2 代中的文件。

有两种类型的管理的标识： 用户分配和系统分配。 Azure HDInsight 使用用户分配托管的标识。 用户分配的托管的标识将创建为独立的 Azure 资源，你可以然后将其分配给一个或多个 Azure 服务实例。 与此相反，在 Azure AD 中创建并随后会自动启用直接对特定 Azure 服务实例的系统分配的托管的标识。 然后，该系统分配的托管标识的生命周期都绑定到启用的服务实例的生命周期。

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight 托管标识实现

在 Azure HDInsight 群集的每个节点上预配管理的标识。 这些标识组件，但是，仅可供 HDInsight 服务。 目前尚不受支持的方法，以便生成访问令牌使用管理的 HDInsight 群集节点上安装的标识。 对于某些 Azure 服务管理的标识是可用于获取访问令牌与你自己上的其他 Azure 服务进行交互的终结点实现的。

## <a name="create-a-managed-identity"></a>创建托管的标识

可以使用任何以下方法创建管理的标识：

* [Azure 门户](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure 资源管理器](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

配置托管身份信息的其余步骤取决于将使用的方案。

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>在 Azure HDInsight 中的托管的标识方案

多个方案中，在 Azure HDInsight 中使用管理的标识。 请参阅相关的文档的详细的设置和配置说明：

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-managed-identity)
* [企业安全性套餐](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka 自带密钥 (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>后续步骤

* [什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)