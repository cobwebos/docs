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
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: 3803bc7f1e0da01fbaa8aa11bc6e8fc5c508b5ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61074276"
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
