---
title: 免费试用语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务入门简单且经济实惠。 免费提供两个选项，以便您可以了解服务的作用，并决定它是否适合您的需求。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: f74e3ea3d20ad2666b434e009cf62add6f88d200
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219661"
---
# <a name="try-the-speech-service-for-free"></a>免费试用语音服务

在本文中，你将选择一个选项来轻松地免费测试语音服务，以便你能够发现服务的功能，并决定它是否适合你的需求。 选择以下两个选项之一，具体取决于你的情况和用例：

- [选项 1](#no-card)：立即获取**免费试用版**API 密钥，而无需提供任何信用卡信息（需要具有现有的 Azure 帐户）。 **免费试用**期限为30天，数据将在结束时删除。 此选项最适合用于对服务的快速试验。
- [选项 2](#new-resource)：在 Azure 中创建新的语音资源，**免费订阅**（需要信用卡信息）。 **免费订阅**主要具有比付费订阅更严格的速率限制。 如果你想要测试服务，并计划在将来升级到付费订阅，并且不希望丢失数据，则可以使用此选项。

## <a id="no-card"></a>试用语音服务，无需信用卡信息

完成以下步骤以激活30天免费试用版并获取 API 密钥。 在完成以下步骤后，你的试用期将立即开始。

1. 请[尝试 "试用认知服务](https://azure.microsoft.com/try/cognitive-services/)"。
1. 选择“语音 API”选项卡。
1. 选择 "**获取 API 密钥**"。

系统会提供计费选项。 选择 "免费" 选项，然后阅读并批准用户协议。 你将看到可用于在30天内免费试用语音服务的密钥。

## <a id="new-resource"></a>通过创建 Azure 资源来试用语音服务

对于以下步骤，你需要 Microsoft 帐户和 Azure 帐户。 如果没有 Microsoft 帐户，则可以在[Microsoft 帐户门户](https://account.microsoft.com/account)上免费注册一个帐户。 选择**Microsoft 登录**然后在系统要求登录时，选择 "**创建 Microsoft 帐户**"。 按步骤创建并验证新的 Microsoft 帐户。

拥有 Microsoft 帐户后，请在[Azure 注册页](https://azure.microsoft.com/free/ai/)上，选择 "**开始免费**"，然后使用 Microsoft 帐户创建新的 Azure 帐户。

> [!NOTE]
> 语音服务有两个服务层：免费和订阅，它们具有不同的限制和优点。 当你注册一个免费的 Azure 帐户时，它附带了 $200 服务信用额度，你可以将其应用于付费语音服务订阅，有效期为30天。
>
> 如果使用免费的低音量语音服务层，即使在免费试用或服务信用额度到期后，也可以保留此免费订阅。
>
> 有关详细信息，请参阅[认知服务定价-语音服务](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

### <a name="create-the-resource"></a>创建资源

若要将语音服务资源（免费层或付费层）添加到 Azure 帐户，请执行以下步骤：

1. 使用 Microsoft 帐户登录 [Azure 门户](https://portal.azure.com/)。

1. 选择门户左上角的“创建资源”。 如果看不到 "**创建资源**"，可以始终通过选择左上方的折叠菜单来找到它：

   ![折叠导航按钮](media/index/collapsed-nav.png)

1. 在**新**窗口中，在搜索框中键入 "speech"，然后按 enter。

1. 在搜索结果中，选择“语音”。

   ![语音搜索结果](media/index/speech-search.png)

1. 选择 "**创建**"，然后：

   - 为新资源提供唯一的名称。 该名称可帮助你区分绑定到同一个服务的多个订阅。
   - 选择新资源关联的 Azure 订阅，以确定计费方式。
   - 选择将使用该资源的[区域](regions.md)。
   - 选择免费（F0）或付费（S0）定价层。 有关每个层的定价和使用配额的完整信息，请选择 "**查看完整的定价详细信息**"。
   - 为此“语音”订阅创建新的资源组或将订阅分配到现有资源组。 资源组有助于使多种 Azure 订阅保持有序状态。
   - 选择“创建”。 这会将你转到部署概述并显示部署进度消息。

> [!NOTE]
> 可在一个或多个区域中创建数量不受限的标准层订阅。 但是，只能创建一个免费层订阅。 不使用的免费层上的模型部署将自动解除授权。

部署新的语音资源需要花费几分钟时间。 部署完成后，选择 "**转到资源**"，然后在左侧导航窗格中选择 "**密钥**"，以显示语音服务订阅密钥。 每个订阅有两个密钥；可在应用程序中使用任意一个密钥。 若要快速复制/粘贴代码编辑器或其他位置的密钥，请选择每个项旁边的 "复制" 按钮，切换 windows 将剪贴板内容粘贴到所需位置。

> [!IMPORTANT]
> 这些订阅密钥用于访问认知服务 API。 不要共享你的密钥。 安全地存储它们-例如，使用 Azure Key Vault。 我们还建议定期重新生成这些密钥。 只需一个密钥就能进行 API 调用。 重新生成第一个密钥时，可以使用第二个密钥来继续访问该服务。

## <a name="switch-to-a-new-subscription"></a>切换到新订阅

若要从一个订阅切换到另一个订阅（例如，在免费试用过期或发布应用程序的情况下进行切换），请将代码中的区域和订阅密钥替换为新 Azure 资源的区域和订阅密钥。

## <a name="about-regions"></a>关于区域

- 如果应用程序使用[语音 SDK](speech-sdk.md)，请在创建语音配置时提供区域代码，例如 `westus`。
- 如果应用程序使用某个语音服务的 [REST API](rest-apis.md)，则区域是你在发出请求时使用的终结点 URI 的一部分。
- 为某个区域创建的密钥仅在该区域有效。 尝试在其他区域使用此类密钥会导致身份验证错误。

## <a name="next-steps"></a>后续步骤

完成我们的 10 分钟快速入门之一，或查看我们的 SDK 示例：

> [!div class="nextstepaction"]
> [快速入门：在 C# 中识别语音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [语音 SDK 示例](speech-sdk.md#get-the-samples)
