---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 08328b3c872ce9e8a25db6075b00e0cee2fdf4fd
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55480570"
---
客户端应用程序需要知道某个话语是否对应用程序没有意义或不合适。 在创建过程中，“None”意向将添加到每个应用程序，以确定客户端应用程序是否无法回答某个话语。

如果 LUIS 对于某个话语返回“None”意向，客户端应用程序可以询问用户是否要结束聊天或提供更多指示以继续聊天。 

> [!CAUTION] 
> 不要将“None”意向留空。 

1. 在左侧面板中选择“意向”。

2. 选择“None”意向。 添加用户可能会输入但与人力资源应用无关的 3 个话语：

    | 示例陈述|
    |--|
    |Barking dogs are annoying|
    |Order a pizza for me|
    |Penguins in the ocean|
