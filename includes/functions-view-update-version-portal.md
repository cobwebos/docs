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
ms.openlocfilehash: d06bda1826964b019edb156375885c7f389ca6ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61026509"
---
使用以下步骤查看并更新函数应用当前使用的运行时版本。

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到你的函数应用。

1. 在“配置的功能”下，选择“函数应用设置”。

    ![选择函数应用设置](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. 在“函数应用设置”选项卡中，定位“运行时版本”。 请注意特定运行时版本和请求的主版本。 在以下示例中，版本设置为 `~2`。

   ![选择函数应用设置](./media/functions-view-update-version-portal/function-app-view-version.png)

1. 若要将函数应用固定到 1.x 版运行时，请在“运行时版本”下选择“~1”。 当应用中存在函数时，此开关被禁用。

1. 如果更改了运行时版本，请返回到“概览”选项卡并选择“重启”以重启应用。  函数应用重新开始在 1.x 版运行时上运行，并且在创建函数时将使用 1.x 版模板。