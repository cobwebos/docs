---
title: include 文件
description: include 文件
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b1bbc11d7772e4f56d7dc6ead580b0a0cbd3cd8d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669534"
---
可以通过 Functions 轻松地将 Application Insights 集成从 [Azure 门户]添加到某个函数应用。

1. 在[门户][Azure 门户]中选择“所有服务”>“Function App”，选择你的函数应用，然后选择窗口顶部的“Application Insights”横幅  

    ![从门户启用 Application Insights](media/functions-connect-new-app-insights/enable-application-insights.png)

1. 使用图片下方的表中指定的设置创建 Application Insights 资源。

   ![创建 Application Insights 资源](media/functions-connect-new-app-insights/ai-general.png)

    | 设置      | 建议的值  | 说明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名称** | 唯一的应用名称 | 使用与函数应用相同的名称是最方便的，该名称在订阅中必须独一无二。 | 
    | **位置** | 西欧 | 尽可能使用函数应用所在的同一[区域](https://azure.microsoft.com/regions/)，或与该区域接近的区域。 |

1. 选择“确定”  。 Application Insights 资源在与函数应用相同的资源组和订阅中创建。 创建资源后，关闭 Application Insights 窗口。

1. 回到函数应用中，选择“应用程序设置”，然后向下滚动到“应用程序设置”。   如果看到名为 `APPINSIGHTS_INSTRUMENTATIONKEY` 的设置，则表明已为在 Azure 中运行的函数应用启用 Application Insights 集成。

[Azure 门户]: https://portal.azure.com
