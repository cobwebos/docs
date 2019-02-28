---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: ef8dae8219eaf1a85a5c112705517b992e25a50f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741471"
---
通过终结点密钥提供对预测终结点的访问权限。 出于本快速入门的目的，请使用与 LUIS 帐户关联的免费初学者密钥。 
 
1. 使用 LUIS 帐户登录。 

    [![语言理解 (LUIS) 应用列表的屏幕截图](media/cognitive-services-luis/app-list.png "语言理解 (LUIS) 应用列表的屏幕截图")](media/cognitive-services-luis/app-list.png)

2. 在右上方菜单中选择你的名称，然后选择“设置”。

    ![LUIS 用户设置菜单访问](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. 复制**创作密钥**的值。 稍后你将在此快速入门中使用它。 

    [![语言理解 (LUIS) 用户设置的屏幕截图](media/cognitive-services-luis/get-user-authoring-key.png "语言理解 (LUIS) 用户设置的屏幕截图")](media/cognitive-services-luis/get-user-authoring-key.png)

    创作密钥允许对创作 API 发出无限量的请求而无需付费，并允许每月为你的所有 LUIS 应用向预测终结点 API 发出最多 1000 个查询。 <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->
