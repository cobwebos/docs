---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: 0ad3b360611a12b95568e9a20f13a57c93b2e603
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2018
ms.locfileid: "51208298"
---
## <a name="roles-versus-hierarchical-entities"></a>角色与分层实体

应该使用分层实体还是使用包含带角色的简单实体的模式？ 

这取决于具体情况。 模式和示例话语类似，因为它们表示用户的话语，并且特定于某个意向。  

如果话语没有清晰的模式，请使用分层实体。 

|分层实体|含角色的简单实体|
|--|--|
|在意向的示例话语和模式中可用|仅在模式中可用|
|必须包含示例话语，并且意向中标记了子实体|不能在意向的示例话语中标记角色|
|可能需要意向中的**更多**示例话语才能提取实体|应需要意向中的**更少**示例话语|
