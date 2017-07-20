---
title: Custom Speech Service meters and quotas on Azure | Microsoft Docs
description: Information about meters and quotas of Custom Speech Service on Azure.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.technology: custom-speech-service
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 0905b744251c96fd526c9eac5027c3cdfdbc4e91
ms.contentlocale: zh-cn
ms.lasthandoff: 07/11/2017

---

# <a name="custom-speech-service-meters-and-quotas"></a>Custom Speech Service meters and quotas

Welcome to Microsoft's Custom Speech Service. Custom Speech Service is a cloud-based service that provides users with the ability to customize speech models for Speech-to-Text transcription.
To use the Custom Speech Service refer to the [Custom Speech Service Portal](https://cris.ai).

The current pricing meters are the following:

| Free Tier **F0** | Paying Tier **S2** |
| ----- | ----- |
|1 Model Deployment (no Scale Units) | Model Deployment $40/model/month |
| Free Adaptation (3 hours max) | Free Adaptation |
| 2 hours of free requests per month | 2 hours free then $1.40/hour |
| 2 hours of testing per month |2 hours free then $1.40/hour |
| 1 concurrent request | $200/scale unit*/month |
| | $30/model/month (no trace) |

## <a name="tiers-explained"></a>Tiers explained
We propose to use the free tier (F0) for testing and prototype only.

For production systems we propose to use the S2 tier. This tier enables you to scale your deployment to a number of SUs your scenario requires.

> [!NOTE]
> Remember that you **cannot** migrate between F0 tier and S2 tier!
>

## <a name="meters-explained"></a>Meters explained

### <a name="scale-out"></a>Scale Out
Scale out is a new feature released along the new pricing model. It gives customers the ability to control the number of concurrent requests their model can process.
Concurrent requests are set using the Scale Unit (SU) measure in the [_Create Model Deployment_ view](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md). Based on the how much traffic they envisage the model consuming, customers can decide on the appropriate number of Scale Units. **Each Scale Unit guarantees 5 concurrent requests**. Customers can buy 1 or more SUs as appropriate. SUs are increased in increments of 1 therefore guaranteed concurrent are increased in increments of 5.

> [!NOTE]
> Remember that **1 Scale Unit = 5 concurrent requests**
>

### <a name="log-management"></a>Log management
Customers can opt to switch off audio traces for a newly deployed model at an additional cost. Custom Speech Service will not log the audio requests or the transcripts from that particular model.

## <a name="next-steps"></a>Next steps
For more details about how to use the Custom Speech Service, refer to the [Custom Speech Service Portal] (https://cris.ai).

* [Get Started](cognitive-services-custom-speech-get-started.md)
* [FAQ](cognitive-services-custom-speech-faq.md)
* [Glossary](cognitive-services-custom-speech-glossary.md)

