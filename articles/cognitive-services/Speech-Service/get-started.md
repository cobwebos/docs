---
title: 免费试用语音服务
titleSuffix: Azure Cognitive Services
description: 以简单、经济的方式开始使用语音服务。 可以使用两个免费的选项来发现服务的功能，并确定它是否符合自己的需求。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219661"
---
# <a name="try-the-speech-service-for-free"></a>免费试用语音服务

在本文中，您可以选择一个选项，以便轻松免费测试语音服务，以便了解该服务可以做什么，并决定它是否适合您的需要。 根据您的情况和用例选择以下两个选项之一：

- [选项 1：](#no-card)立即获取**免费试用**API 密钥，而无需提供任何信用卡信息（您需要具有现有的 Azure 帐户）。 **免费试用**将持续 30 天，数据在末尾被删除。 此选项最适合快速试验服务。
- [选项 2：](#new-resource)使用**免费订阅**（需要信用卡信息）在 Azure 中免费创建新的语音资源。 **免费订阅**主要比付费订阅具有更严格的费率限制。 如果要测试服务，但计划将来升级到付费订阅，并且不想丢失数据，则此选项是最佳选择。

## <a name="try-the-speech-service-without-credit-card-info"></a><a id="no-card"></a>尝试没有信用卡信息的语音服务

完成以下步骤以激活 30 天的免费试用版并获得 API 密钥。 完成以下步骤后，试用期立即开始。

1. 转到[尝试认知服务](https://azure.microsoft.com/try/cognitive-services/)。
1. 选择“语音 API”选项卡。****
1. 选择**获取 API 密钥**。

您将看到帐单选项。 选择免费选项，然后读取和批准用户协议。 您将获得可用于免费试用语音服务的密钥 30 天。

## <a name="try-the-speech-service-by-creating-an-azure-resource"></a><a id="new-resource"></a>通过创建 Azure 资源尝试语音服务

对于以下步骤，您需要 Microsoft 帐户和 Azure 帐户。 如果您没有 Microsoft 帐户，则可以在[Microsoft 帐户门户](https://account.microsoft.com/account)免费注册一个帐户。 选择 **"使用 Microsoft 登录**"，然后，当被要求登录时，选择"**创建 Microsoft 帐户**"。 按步骤创建并验证新的 Microsoft 帐户。

拥有 Microsoft 帐户后，转到[Azure 注册页](https://azure.microsoft.com/free/ai/)，选择 **"免费开始"，** 并使用 Microsoft 帐户创建新的 Azure 帐户。

> [!NOTE]
> 语音服务有两个服务层级：免费和订阅，它们具有不同的限制和优势。 当您注册免费 Azure 帐户时，它附带 200 美元的服务积分，您可以申请付费语音服务订阅，有效期最长为 30 天。
>
> 如果使用免费的低流量语音服务层级，即使是在免费试用帐户或服务额度过期之后，也仍可以保留此免费订阅。
>
> 有关详细信息，请参阅[认知服务定价 - 语音服务](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

### <a name="create-the-resource"></a>创建资源

若要将语音服务资源（免费层或付费层）添加到 Azure 帐户，请执行以下步骤：

1. 使用 Microsoft 帐户登录 [Azure 门户](https://portal.azure.com/)。

1. 选择门户左上角的“创建资源”。**** 如果看不到“创建资源”，始终可以通过选择左上角的折叠菜单来找到它：****

   ![折叠的导航按钮](media/index/collapsed-nav.png)

1. 在“新建”窗口中的搜索框内键入“语音”，然后按 ENTER。****

1. 在搜索结果中，选择“语音”。****

   ![语音搜索结果](media/index/speech-search.png)

1. 选择“创建”，然后：****

   - 为新资源指定唯一的名称。 该名称可帮助您区分与同一服务关联的多个订阅。
   - 选择新资源关联的 Azure 订阅，以确定计费方式。
   - 选择将使用资源[的区域](regions.md)。
   - 选择免费 (F0) 或付费 (S0) 定价层。 若要查看每个层的定价和用量配额的完整信息，请选择“查看全部定价详细信息”****。
   - 为此“语音”订阅创建新的资源组或将订阅分配到现有资源组。 资源组有助于使多种 Azure 订阅保持有序状态。
   - 选择 **“创建”**。 系统随后会将你转到部署概述，并显示部署进度消息。

> [!NOTE]
> 可在一个或多个区域中创建数量不受限的标准层订阅。 但是，只能创建一个免费层订阅。 在免费层上进行的模型部署如果连续 7 天处于未使用状态，则会被系统自动停用。

部署新的语音资源需要花费片刻时间。 部署完成后，选择“转到资源”，然后在左侧导航窗格中选择“密钥”以显示语音服务订阅密钥。******** 每个订阅有两个密钥；可在应用程序中使用任意一个密钥。 若要将密钥快速复制/粘贴到代码编辑器或其他位置，请选择每个密钥旁边的复制按钮，切换窗口，然后将剪贴板中的内容粘贴到所需位置。

> [!IMPORTANT]
> 这些订阅密钥用于访问认知服务 API。 不要共享你的密钥。 安全存储密钥 - 例如，使用 Azure Key Vault。 此外，我们建议定期重新生成这些密钥。 发出 API 调用只需一个密钥。 重新生成第一个密钥时，可以使用第二个密钥来持续访问服务。

## <a name="switch-to-a-new-subscription"></a>切换到新订阅

若要从一个订阅切换到另一个订阅（例如，在免费试用过期或发布应用程序的情况下进行切换），请将代码中的区域和订阅密钥替换为新 Azure 资源的区域和订阅密钥。

## <a name="about-regions"></a>关于区域

- 如果应用程序使用[语音 SDK](speech-sdk.md)，请在创建语音配置时提供区域代码，例如 `westus`。
- 如果应用程序使用某个语音服务的 [REST API](rest-apis.md)，则区域是你在发出请求时使用的终结点 URI 的一部分。
- 为某个区域创建的密钥仅在该区域有效。 尝试在其他区域使用此类密钥会导致身份验证错误。

## <a name="next-steps"></a>后续步骤

完成我们的 10 分钟快速入门之一，或查看我们的 SDK 示例：

> [!div class="nextstepaction"]
> [快速入门：在 C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [语音 SDK 示例中识别语音](speech-sdk.md#get-the-samples)
