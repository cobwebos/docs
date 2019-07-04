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
ms.openlocfilehash: 355fe134939b26c51d6e03368f782845628a6b96
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173258"
---
客户端应用程序需要知道某个话语是否对应用程序没有意义或不合适。 在创建过程中，“None”  意向将添加到每个应用程序，以确定客户端应用程序是否无法回答某个话语。

如果 LUIS 对于某个话语返回“None”  意向，客户端应用程序可以询问用户是否要结束聊天或提供更多指示以继续聊天。 

> [!CAUTION] 
> 不要将“None”  意向留空。 

1. 在左侧面板中选择“意向”。 

2. 选择“None”意向。  添加用户可能会输入但与人力资源应用无关的 3 个话语：

    | 示例陈述|
    |--|
    |Barking dogs are annoying|
    |Order a pizza for me|
    |Penguins in the ocean|
