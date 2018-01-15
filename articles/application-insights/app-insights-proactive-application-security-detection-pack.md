---
title: "智能检测 - Azure Application Insights 中的安全检测包 | Microsoft Docs"
description: "使用 Azure Application Insights 监视应用程序的潜在安全问题。"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 837525d70f28c0710b8a8b86d411a7854402207f
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="application-security-detection-pack-preview"></a>应用程序安全检测包（预览版）

Application Insights 会自动分析应用程序生成的遥测数据并检测潜在的安全问题。 此功能使得你能够识别潜在的安全问题，并通过修复应用程序或采取必要的安全措施来处理这些问题。

除了[配置应用来发送遥测数据](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview)之外，此功能不要求进行特殊设置。

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>我何时会收到此类型的智能检测通知？
检测到的安全问题分三种类型：
1. 不安全的 URL 访问：正在同时通过 HTTP 和 HTTPS 访问应用程序中的 URL。 通常情况下，接受 HTTPS 请求的 URL 不应当接受 HTTP 请求。 这可能表明应用程序中存在 bug 或安全问题。
2. 不安全的窗体：应用程序中的窗体（或其他“POST”请求）使用了 HTTP 而非 HTTPS。 使用 HTTP 可能会危害窗体发送的用户数据的安全。
3. 可疑用户活动：同一用户大约在同一时间在从多个国家/地区访问应用程序。 例如，同一用户在同一小时内从西班牙和美国访问了应用程序。 此检测表明可能有人试图恶意访问你的应用程序。

## <a name="does-my-app-definitely-have-a-security-issue"></a>我的应用是否一定有安全问题？
否，收到通知并不意味着你的应用一定有问题。 在许多情况下，检测到以上任一状况可能意味着存在安全问题。 但是，检测可能有正常的业务理由并且可以忽略。

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>如何修复“不安全的 URL 访问”检测？
1. **会审。** 该通知提供访问了不安全 URL 的用户数，以及受不安全访问影响最大的 URL。 这可以帮助你对问题分配优先级。
2. **划分范围。** 访问了不安全 URL 的用户所占百分比是多少？ 受影响的 URL 有多少？ 可以从通知中获取此信息。
3. **诊断。** 该检测提供不安全请求的列表，以及受影响的 URL 和用户的列表，以帮助你进一步诊断问题。

## <a name="how-do-i-fix-the-insecure-form-detection"></a>如何修复“不安全的窗体”检测？
1. **会审。** 该通知提供不安全窗体的数量，以及其数据可能已受危害的用户数。 这可以帮助你对问题分配优先级。
2. **划分范围。** 哪个窗体涉及的不安全传输数量最多？从时间方面来看，不安全传输是如何分布的？ 可以从通知中获取此信息。
3. **诊断。** 该检测提供不安全窗体的列表以及每个窗体的不安全传输数量，以帮助你进一步诊断问题。

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>如何修复“可疑用户活动”检测？
1. **会审。** 该通知提供表现出可疑行为的不同用户的数量。 这可以帮助你对问题分配优先级。
2. **划分范围。** 可疑请求是从哪些国家/地区发起的？ 哪个用户最为可疑？ 可以从通知中获取此信息。
3. **诊断。** 该检测提供可疑用户的列表以及每个用户的国家/地区列表，以帮助你进一步诊断问题。
