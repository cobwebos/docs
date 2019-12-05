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
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 3ddba414ef1801b812d157ad734847099a8a9f25
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806178"
---
# <a name="try-the-speech-service-for-free"></a>免费试用语音服务

语音服务的使用既简单又经济。 免费提供两个选项，以便您可以了解服务的作用，并决定它是否适合您的需要：

- 免费试用，无需提供任何信用卡信息（需要拥有现有的 Azure 帐户）
- 免费创建新的 Azure 帐户（需要信用卡信息）

在本文中，你将选择最适合你的需求的选项之一。

> [!NOTE]
> 语音服务有两个服务层：免费和订阅，它们具有不同的限制和优点。 当你注册一个免费的 Azure 帐户时，它附带了 $200 服务信用额度，你可以将其应用于付费语音服务订阅，有效期为30天。
>
> 如果使用免费的低音量语音服务层，即使在免费试用或服务信用额度到期后，也可以保留此免费订阅。
>
> 有关详细信息，请参阅[认知服务定价-语音服务](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

## <a name="try-the-speech-service-without-credit-card-info"></a>试用语音服务，无需信用卡信息

尽管我们建议使用下一部分中的说明来尝试语音服务，但如果以下情况适用，你可能更愿意使用此部分的说明：

- 你已有一个有效的 Azure 帐户。
- 需要在不创建新 Azure 帐户的情况下评估语音服务。
- 不需要使用信用卡，也不会在试用期后保存数据。

> [!NOTE]
> 在完成以下步骤后，你的试用期将立即开始。

1. 请[尝试 "试用认知服务](https://azure.microsoft.com/try/cognitive-services/)"。
1. 选择“语音 API”选项卡。
1. 选择 "**获取 API 密钥**"。

系统会提供计费选项。 选择 "免费" 选项，然后阅读并批准用户协议。 你将看到一些密钥，你可以使用这些密钥在有限的时间里试用语音服务。

## <a name="try-the-speech-service-using-a-new-azure-account"></a>使用新的 Azure 帐户试用语音服务

若要注册新的 Azure 帐户，需要 Microsoft 帐户。 如果没有 Microsoft 帐户，则可以在[Microsoft 帐户门户](https://account.microsoft.com/account)上免费注册一个帐户。 选择**Microsoft 登录**然后在系统要求登录时，选择 "**创建 Microsoft 帐户**"。 按步骤创建并验证新的 Microsoft 帐户。

拥有 Microsoft 帐户后，请在[Azure 注册页](https://azure.microsoft.com/free/ai/)上，选择 "**开始免费**"，然后使用 Microsoft 帐户创建新的 Azure 帐户。

### <a name="create-a-speech-resource-in-azure"></a>在 Azure 中创建语音资源

> [!NOTE]
> 可在一个或多个区域中创建数量不受限的标准层订阅。 但是，只能创建一个免费层订阅。 不使用的免费层上的模型部署将自动解除授权。

若要将语音服务资源（免费层或付费层）添加到 Azure 帐户，请执行以下步骤：

1. 使用 Microsoft 帐户登录 [Azure 门户](https://portal.azure.com/)。

1. 选择门户左上角的“创建资源”。 如果看不到 "**创建资源**"，可以始终通过选择左上方的折叠菜单来找到它：

   ![折叠导航按钮](media/index/collapsed-nav.png)

1. 在**新**窗口中，在搜索框中键入 "speech"，然后按 enter。

1. 在搜索结果中，选择“语音”。

   ![语音搜索结果](media/index/speech-search.png)

1. 选择 "**创建**"，然后：

   - 为新资源提供唯一的名称。 名称有助于区分同一服务的多个订阅。
   - 选择新资源关联的 Azure 订阅，以确定计费方式。
   - 选择将使用该资源的[区域](regions.md)。
   - 选择免费（F0）或付费（S0）定价层。 有关每个层的定价和使用配额的完整信息，请选择 "**查看完整的定价详细信息**"。
   - 为此“语音”订阅创建新的资源组或将订阅分配到现有资源组。 资源组有助于使多种 Azure 订阅保持有序状态。
   - 选择**创建**。 这会将你转到部署概述并显示部署进度消息。

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
