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
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: dd99218765c873120c4117a3ec1712fe0a605e66
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164581"
---
**问题**：应该使用分层实体还是使用包含带角色的简单实体的模式？ 

**答案**：这取决于具体情况。 模式和示例话语类似，因为它们表示用户的话语，并且特定于某个意向。  

如果话语没有清晰的模式，请使用分层实体。 

|分层实体|含角色的简单实体|
|--|--|
|必须包含示例话语，并且意向中标记了子实体|必须包含示例话语，**意向中不能标记角色**|
|可以在模式中使用|**必须**在模式中使用|
|可能需要意向中的**更多**示例话语|可能需要意向中的**更少示例话语|