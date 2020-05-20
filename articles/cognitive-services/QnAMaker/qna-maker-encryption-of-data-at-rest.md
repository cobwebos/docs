---
title: 静态数据加密 QnA Maker
titleSuffix: Azure Cognitive Services
description: 静态数据加密 QnA Maker。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 99c21ece202f8d9867045d506574dd7718bd455e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653660"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>静态数据加密 QnA Maker

QnA Maker 在将数据保存到云时，会自动对其进行加密，从而有助于满足组织的安全性和符合性目标。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，订阅使用 Microsoft 托管的加密密钥。 还可以选择使用自己的密钥来管理订阅。 客户托管的密钥（CMK）提供更大的灵活性来创建、轮换、禁用和撤消访问控制。 还可以审核用于保护数据的加密密钥。

QnA Maker 使用 Azure 搜索中的 CMK 支持。 需要[在 Azure 搜索中使用 Azure Key Vault 创建 CMK](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)。 此 Azure 实例应该与 QnA Maker 服务相关联，以使其 CMK 启用。

> [!IMPORTANT]
> Azure 搜索服务资源必须在2019年1月之后创建，且不能位于免费（共享）层中。 不支持在 Azure 门户中配置客户管理的密钥。

## <a name="enable-customer-managed-keys"></a>启用客户管理的密钥

QnA Maker 服务使用 Azure 搜索服务中的 CMK。 请按照以下步骤启用 Cmk：

1. 创建新的 Azure 搜索实例，并启用[适用于 Azure 认知搜索的客户托管密钥先决条件](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites)中所述的先决条件。

   ![查看加密设置](../media/cognitive-services-encryption/qna-encryption-1.png)

2. 创建 QnA Maker 资源时，它会自动与 Azure 搜索实例相关联。 这不能与 CMK 一起使用。 若要使用 CMK，需要将在步骤1中创建的新创建的 Azure 搜索实例关联起来。 具体而言，需要 `AzureSearchAdminKey` `AzureSearchName` 在 QnA Maker 资源中更新和。

   ![查看加密设置](../media/cognitive-services-encryption/qna-encryption-2.png)

3. 接下来，创建新的应用程序设置：
   * **名称**：将此设置为`CustomerManagedEncryptionKeyUrl`
   * **值**：这是在创建 Azure 搜索实例时在步骤1中获得的值。

   ![查看加密设置](../media/cognitive-services-encryption/qna-encryption-3.png)

4. 完成后，重新启动运行时。 现在，QnA Maker 服务已启用 CMK。

## <a name="regional-availability"></a>区域可用性

客户托管的密钥可用于所有 Azure 搜索区域。

## <a name="encryption-of-data-in-transit"></a>传输中的数据加密

QnA Maker 门户在用户的浏览器中运行。 每个操作都会触发直接调用各自的认知服务 API。 因此，QnA Maker 符合传输中的数据。
但是，因为 QnA Maker 门户服务托管在美国西部，所以对于非美国客户，这仍不是理想之选。 

## <a name="next-steps"></a>后续步骤

* [在 Azure Key Vault 中使用 Cmk 在 Azure 搜索中进行加密](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [静态数据加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [详细了解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
