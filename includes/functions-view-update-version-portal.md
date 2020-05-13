---
title: include 文件
description: include 文件
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: aa9a715fdafc143a116458691965087b016dec1f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83343348"
---
使用以下步骤查看并更新函数应用当前使用的运行时版本。

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到你的函数应用。

1. 在 "**设置**" 下，选择 "**配置**"。 在 "**函数运行时设置**" 选项卡中，找到 "**运行时版本**"。 请注意特定的运行时版本。 在以下示例中，版本设置为 `~3`。

    :::image type="content" source="./media/functions-view-update-version-portal/functions-view-runtime-version.png" alt-text="查看运行时版本。" border="true":::

1. 若要将函数应用固定到 1.x 版运行时，请在“运行时版本”下选择“~1”。******** 当应用中存在函数时，此开关被禁用。

1. 如果更改了运行时版本，请返回到“概览”**** 选项卡并选择“重启”**** 以重启应用。  函数应用重新开始在 1.x 版运行时上运行，并且在创建函数时将使用 1.x 版模板。

    :::image type="content" source="./media/functions-view-update-version-portal/functions-restart-function-app.png" alt-text="重新启动函数应用。" border="true":::
