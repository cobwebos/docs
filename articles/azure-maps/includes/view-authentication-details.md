---
title: 查看 Azure Maps 的身份验证详细信息
description: 使用 Azure 门户查看 Azure Maps 的身份验证详细信息。
author: philmea
ms.author: philmea
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9146844a6b83f78ad99ef7cd1aec4b028daf3ff6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988316"
---
可以在 Azure 门户中查看 Azure Maps 帐户身份验证详细信息。 在帐户的 "**设置**" 菜单上，选择 "**身份验证**"。

![身份验证详细信息](../media/how-to-manage-authentication/how-to-view-auth.png)

创建 Azure Maps 帐户后，Azure Maps `x-ms-client-id` 值就会出现在 Azure 门户身份验证详细信息页中。 此值表示将用于 REST API 请求的帐户。 此值应存储在应用程序配置中，并在对 Azure Maps 使用 Azure AD 身份验证时在发出 http 请求之前检索。
