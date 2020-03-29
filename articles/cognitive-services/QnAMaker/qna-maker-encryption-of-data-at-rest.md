---
title: QnA 制造商静态数据加密
titleSuffix: Azure Cognitive Services
description: QnA 制造商对静态数据进行加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372091"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA 制造商静态数据加密

QnA Maker 会在数据保存到云中时自动加密数据，从而有助于实现组织安全性和合规性目标。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，您的订阅使用 Microsoft 管理的加密密钥。 还有一个选项可以用自己的密钥管理订阅。 客户管理的密钥 （CMK） 提供了创建、旋转、禁用和撤销访问控制的更大灵活性。 还可以审核用于保护数据的加密密钥。

QnA 制造商使用 Azure 搜索的 CMK 支持。 您需要[使用 Azure 密钥保管库在 Azure 搜索中创建 CMK。](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys) 此 Azure 实例应与 QnA Maker 服务关联，使其启用 CMK。

> [!IMPORTANT]
> Azure 搜索服务资源必须在 2019 年 1 月之后创建，并且不能位于免费（共享）层中。 不支持在 Azure 门户中配置客户管理的密钥。

## <a name="enable-customer-managed-keys"></a>启用客户管理的密钥

QnA Maker 服务使用 Azure 搜索服务的 CMK。 按照以下步骤启用 CMK：

1. 创建新的 Azure 搜索实例，并启用[客户管理 Azure 认知搜索的关键先决条件中提到的先决条件](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites)。

   ![查看加密设置](../media/cognitive-services-encryption/qna-encryption-1.png)

2. 创建 QnA Maker 资源时，它会自动与 Azure 搜索实例关联。 这不能与 CMK 一起使用。 要使用 CMK，您需要关联步骤 1 中创建的新创建的 Azure 搜索实例。 具体来说，您需要更新 QnA `AzureSearchAdminKey` Maker`AzureSearchName`资源中的 和。

   ![查看加密设置](../media/cognitive-services-encryption/qna-encryption-2.png)

3. 接下来，创建新的应用程序设置：
   * **名称**： 将此设置为`CustomerManagedEncryptionKeyUrl`
   * **值**：这是创建 Azure 搜索实例时在步骤 1 中获得的值。

   ![查看加密设置](../media/cognitive-services-encryption/qna-encryption-3.png)

4. 完成后，重新启动运行时。 现在，您的 QnA 制造商服务已启用 CMK。

## <a name="regional-availability"></a>区域可用性

客户管理的密钥在所有 Azure 搜索区域都可用。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 密钥保管库中的 CMK 在 Azure 搜索中进行加密](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [静态数据加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [了解有关 Azure 密钥保管库的更多](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
