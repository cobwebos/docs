---
title: 快速入门 - 创建和管理访问令牌
titleSuffix: An Azure Communication Services quickstart
description: 了解如何使用 Azure 通信服务管理客户端库管理标识和访问令牌。
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: a76000ecacdf78196ec1b80a60940484f6421641
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943488"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>快速入门：创建和管理访问令牌

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

通过使用通信服务管理客户端库预配和管理访问令牌，开启 Azure 通信服务使用旅程。 访问令牌使聊天和呼叫客户端库能直接对 Azure 通信服务进行身份验证。 这些令牌是在你实现的服务器端令牌预配服务上生成的。 然后，它们用于在客户端设备上初始化通信服务客户端库。

请注意，本教程中的图像中显示的任何价格仅用于示例用途。

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

应用的输出描述每个已完成的操作：
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Issued a access token with 'voip' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Issued a access token with 'chat' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Successfully deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050

Deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](./create-communication-resource.md#clean-up-resources)的详细信息。


## <a name="next-steps"></a>后续步骤

在此快速入门中，读者学习了如何：

> [!div class="checklist"]
> * 管理标识
> * 颁发访问令牌
> * 使用通信服务管理客户端库


> [!div class="nextstepaction"]
> [在应用中添加语音呼叫](./voice-video-calling/getting-started-with-calling.md)

你可能还想要：

 - [了解身份验证](../concepts/authentication.md)
 - [向应用中添加聊天](./chat/get-started.md)
 - [了解客户端和服务器体系结构](../concepts/client-and-server-architecture.md)
