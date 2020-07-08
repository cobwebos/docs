---
title: include 文件
description: include 文件
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/10/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 87af50c5b5e5b69fd175ac4a570c4b6f659b97e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84730968"
---
Azure Functions 可以轻松地从[Azure 门户]将 Application Insights 集成添加到 function app。

1. 在[Azure 门户][Azure 门户]中，搜索并选择 " **function app**"，然后选择函数应用。 

1. 选择窗口顶部的 "**未配置 Application Insights**标题"。 如果看不到此横幅，则你的应用可能已启用 Application Insights。

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="从门户启用 Application Insights":::

1. 展开 "**更改资源**"，并使用下表中指定的设置创建一个 Application Insights 资源。  

    | 设置      | 建议的值  | 说明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **新资源名称** | 唯一的应用名称 | 使用与函数应用相同的名称是最方便的，该名称在订阅中必须独一无二。 | 
    | **位置** | 西欧 | 尽可能使用函数应用所在的同一[区域](https://azure.microsoft.com/regions/)，或与该区域接近的区域。 |

    :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="创建 Application Insights 资源":::

1. 选择“应用”。 

   Application Insights 资源在与函数应用相同的资源组和订阅中创建。 创建资源后，关闭 Application Insights 窗口。

1. 在函数应用中，选择 "**设置**" 下的 "**配置**"，然后选择 "**应用程序设置**"。 如果看到名为 `APPINSIGHTS_INSTRUMENTATIONKEY` 的设置，则表明已为在 Azure 中运行的函数应用启用 Application Insights 集成。

[Azure 门户]: https://portal.azure.com
