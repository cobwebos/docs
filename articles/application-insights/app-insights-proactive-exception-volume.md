---
title: "智能检测 - Azure Application Insights 中异常卷的异常增加 | Microsoft Docs"
description: "使用 Azure Application Insights 监视应用程序异常，了解异常卷的异常模式。"
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
ms.date: 12/08/2017
ms.author: mbullwin
ms.openlocfilehash: 8030f3331a03170bb265c417a57725544bdc7d3f
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2017
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>异常卷的异常增加（预览）

Application Insights 自动分析应用程序中引发的异常，并对异常遥测中的异常模式发出警告。

此功能需要为应用[配置异常报告](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting)，除此之外，不需要其他特殊步骤。 在应用生成足够多的异常遥测数据后，此功能会激活。

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>何时会收到此类型的智能检测通知？
对比前面七天计算的基线，如果应用显示某个特殊类型的异常数在一天内异常增加，则可能会收到此类型的通知。
机器学习算法广泛用于检测异常数的增加，同时考虑应用程序使用情况的自然增长。

## <a name="does-my-app-definitely-have-a-problem"></a>收到通知是否意味着我的应用肯定有问题？
不是，通知并不意味着应用肯定有问题。 虽然异常数大量增加通常指示应用程序出现问题，但这些异常也可能是良性的并得到应用程序的正确处理。

## <a name="how-do-i-fix-it"></a>如何解决问题？
通知包括诊断信息，以在诊断进程中提供支持：
1. **会审。** 通知会显示有多少用户或多少请求受到影响。 这可以帮助你对问题分配优先级。
2. **范围。** 该问题是影响所有流量，还是只影响某些操作？ 可以从通知中获取此信息。
3. **诊断。** 检测包括从中引发异常的方法以及异常类型的相关信息。 还可以使用链接到支持信息的相关项和报告，帮助进一步诊断问题。