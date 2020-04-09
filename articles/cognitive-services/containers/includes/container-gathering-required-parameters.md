---
title: 收集所需的参数
services: cognitive-services
author: aahill
manager: nitinme
description: 所有认知服务容器的参数
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 85c67384c38376dc6f5701cf26e879d6e1b76c7f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875023"
---
## <a name="gathering-required-parameters"></a>收集所需的参数

所有认知服务容器都需要三个主要参数。 最终用户许可协议 （EULA） 必须具有 的值。 `accept` 此外，还需要终结点 URL 和 API 密钥。

### <a name="endpoint-uri-endpoint_uri"></a>端点 URI`{ENDPOINT_URI}`

**端点**URI 值在相应认知服务资源的 Azure 门户*概述*页上可用。 导航到 *"概述"* 页，将鼠标悬停在终结点上`Copy to clipboard`<span class="docon docon-edit-copy x-hidden-focus"></span>，并显示一个图标。 根据需要复制和使用。

![收集终结点 uri 供以后使用](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>钥匙`{API_KEY}`

此密钥用于启动容器，并在相应认知服务资源的 Azure 门户的"密钥"页上可用。 导航到 *"密钥"* 页，然后单击该`Copy to clipboard`<span class="docon docon-edit-copy x-hidden-focus"></span>图标。

![获取两个密钥之一，供以后使用](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> 这些订阅密钥用于访问认知服务 API。 不要共享你的密钥。 使用 Azure 密钥保管库安全地存储它们。 此外，我们建议定期重新生成这些密钥。 发出 API 调用只需一个密钥。 重新生成第一个密钥时，可以使用第二个密钥来持续访问服务。