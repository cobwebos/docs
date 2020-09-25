---
title: 规划 Azure 通信服务的电话服务和短信解决方案
titleSuffix: An Azure Communication Services concept document
description: 了解如何有效规划电话号码和电话服务的使用。
author: stkozak
manager: rampras
services: azure-communication-services
ms.author: stkozak
ms.date: 06/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 39f88ab8b735438f60d8e20513ea5cbda43d41ee
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943381"
---
# <a name="plan-your-telephony-and-sms-solution"></a>规划电话服务和短信解决方案

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

本文档介绍了 Azure 通信服务必须提供的各种电话套餐和号码类型。 我们将逐步讲解决策流，帮助你选择通过通信服务提供的语音服务提供商、电话号码类型、套餐和功能。

## <a name="about-phone-numbers-in-azure-communications-services"></a>关于 Azure 通信服务中的电话号码

Azure 通信服务允许你使用电话号码拨打和接听电话以及收发短信。 这些电话号码可用于为你的服务拨出的通话配置呼叫方 ID。
  
如果没有要导入到通信服务解决方案的现有电话号码，最简单的开始方法是在几分钟内从 Azure 通信服务获取新的电话号码。

如果想在解决方案中继续使用现有电话号码（例如 1-800–COMPANY），则可以将该电话号码从现有提供商移植到通信服务。

下图可帮助你浏览可用选项：

:::image type="content" source="../media/plan-solution/decision-tree-basic.png" alt-text="说明如何针对电话号码做出决定的关系图。":::

现在，让我们了解一下通过通信服务提供的电话号码类型和功能。 

## <a name="microsoft-direct-offer-of-phone-numbers-and-capabilities"></a>Microsoft 直接提供的电话号码和功能

Azure 通信服务为开发人员提供了极佳的灵活性。 对于大多数电话号码，我们允许配置“按菜单点菜”套餐。 某些开发人员只需要拨入通话套餐；而其他人可能会选择拨入通话和传出短信套餐。 你可以在通信服务中租赁和/或移植电话号码时选择这些套餐。

哪些套餐可用取决于你所使用的电话号码类型以及该号码所属的国家/地区。下图表示决策流：  哪些套餐可用取决于你所使用的电话号码类型以及该号码所属的国家/地区。

<!-- Tami/team have rejected this multiple times despite updates, says it needs to be higher res - need to work with her to get approval for this image. Commenting out to move our staging forward. :::image type="content" source="../../media/example-decision-flow.png" alt-text="Example for the decision flow"::: -->

在选择电话号码类型之前，请先查看国际电话编号计划。

### <a name="optional-reading-international-public-telecommunication-numbering-plan-e164"></a>可选阅读材料。 国际公众电信编号计划 (E.164)

> [!NOTE]
> 即使熟悉 E.164 电话编号计划，也建议你仔细阅读此信息，以便更好地了解 Azure 通信服务直接提供的号码类型和功能。

国际公众电信编号计划在国际电信联盟 (ITU) 建议书 E.164 中定义。 合格号码最多为 15 位数。

电话号码包括

-   前缀“+”
-   国际拨号前缀或国家/地区代码（一位数、两位数或三位数） 
-   （可选）国家目的地代码或编号计划，通常称为区号。 此代码的长度取决于国家/地区。 在美国，它是三位数。 在澳大利亚和新西兰，它是一位数。 德国、日本、墨西哥和其他一些国家/地区的区号长度可变。 例如，在德国，区号可以是两位数至五位数；而在日本，区号可以是一位数至五位数。
-   订户号

> [!NOTE]
> 以上分类并不完全符合 ITU E.164 标准，而仅用于提供简化描述。 例如，该标准对订户号进行了细分。 如果你想更深入地了解国际编号计划，[ITU E.164 标准](https://www.itu.int/rec/T-REC-E.164)是一个很好的起点。  

下面这些示例可帮助你更好地了解编号计划：

美国的区域电话号码：

:::image type="content" source="../media/plan-solution/regional-us.png" alt-text="美国的区域电话号码示例":::

加拿大的区域电话号码：

:::image type="content" source="../media/plan-solution/regional-canada.png" alt-text="加拿大的区域电话号码示例":::

北美地区的免费电话号码：

:::image type="content" source="../media/plan-solution/tollfree-us.png" alt-text="北美地区的免费电话号码示例":::

英国移动电话号码：

:::image type="content" source="../media/plan-solution/mobile-uk.png" alt-text="英国移动电话号码示例":::

接下来，让我们了解一下 Azure 通信服务提供的特定电话号码类型。

## <a name="phone-number-types-in-azure-communication-services"></a>Azure 通信服务中的电话号码类型

Microsoft 在适用的国家/地区提供了针对短码和移动号码的区域套餐和免费电话套餐。

下表总结了这些电话号码类型： 

| 电话号码类型 | 示例                              | 上市国家/地区    | 常见用例                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------------------------------------------------------------------------------------------------- |
| 区域          | +1（地理区号）XXX XX XX  | 美国、加拿大、波多黎各 | 将电话号码分配给应用程序中的用户或分配给互动语音响应 (IVR) 系统/机器人 |
| 免费电话         | +1（免费电话*区号*）XXX XX XX | 美国、加拿大、波多黎各 | 分配给互动语音响应 (IVR) 系统/机器人、短信应用程序                                        |

## <a name="plans"></a>Plans 

让我们看看你可以为电话号码启用的功能。 出于法规要求，这些功能因国家/地区而异。 Azure 通信服务提供以下功能：

- **单向传出短信**，适用于通知和双因素身份验证方案。
- **双向传入和传出短信**，这是一个预定义包，允许在一个套餐中发送和接收短信。
- **PSTN 呼叫**，你可以选择拨入通话，并使用呼叫方 ID 进行拨出通话。

## <a name="next-steps"></a>后续步骤

### <a name="quickstarts"></a>快速入门

- [获取电话号码](../../quickstarts/telephony-sms/get-phone-number.md)
- [拨打电话](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [发送短信](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>概念文档

- [语音和视频概念](../voice-video-calling/about-call-types.md)
- [呼叫流和短信流](../call-flows.md)
- [定价](../pricing.md)
