---
title: 正在收集所需参数
services: cognitive-services
author: IEvangelist
manager: nitinme
description: 所有认知服务的容器的参数
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 06f68d28d06dec7c9e738ad8cb50ca337fa840be
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102012"
---
## <a name="gathering-required-parameters"></a>正在收集所需参数

所有认知服务容器都需要三个主要参数。 最终用户许可协议（EULA）的值`accept`必须为。 此外，需要终结点 URL 和 API 密钥。

> [!NOTE]
> 这三个必需参数的唯一例外是将容器视为 "脱机" 容器。 脱机容器不报告使用情况，不按流量计费，并遵循其他计费方法。

### <a name="endpoint-uri-endpoint_uri"></a>终结点 URI`{ENDPOINT_URI}`

"**终结点**URI" 值在相应认知服务资源的 "Azure 门户*概述*" 页上可用。 导航到 "*概述*" 页，将鼠标悬停在终结点`Copy to clipboard`上，将显示一个<span class="docon docon-edit-copy x-hidden-focus"></span>图标。 复制并在需要时使用。

![收集终结点 uri 供以后使用](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>键`{API_KEY}`

此密钥用于启动容器，并在相应认知服务资源的 Azure 门户 "密钥" 页上可用。 导航到 "*密钥*" 页，然后单击`Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span>图标。

![获取两个密钥之一供以后使用](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> 这些订阅密钥用于访问认知服务 API。 不要共享你的密钥。 安全地存储它们，例如，使用 Azure Key Vault。 我们还建议定期重新生成这些密钥。 只需一个密钥就能进行 API 调用。 重新生成第一个密钥时，可以使用第二个密钥来继续访问该服务。