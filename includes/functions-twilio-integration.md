---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132769"
---
此示例涉及使用 [Twilio](https://www.twilio.com/) 服务向移动电话发送短信。 Azure Functions 已通过 [Twilio 绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio)提供对 Twilio 的支持，此示例使用了这一功能。

首先，需要一个 Twilio 帐户。 可以通过 https://www.twilio.com/try-twilio 免费创建一个帐户。 创建帐户以后，向函数应用添加以下三个**应用设置**。

| 应用设置名称 | 值说明 |
| - | - |
| TwilioAccountSid   | Twilio 帐户的 SID |
| TwilioAuthToken    | Twilio 帐户的身份验证令牌 |
| TwilioPhoneNumber  | 与 Twilio 帐户关联的电话号码。 此电话号码用于发送短信。 |