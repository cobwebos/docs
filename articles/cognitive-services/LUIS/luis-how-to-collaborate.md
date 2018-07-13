---
title: 与 Azure 中的 LUIS 应用上的其他参与者进行协作 | Microsoft Docs
description: 了解如何与语言理解 (LUIS) 应用程序上的其他参与者协作。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/05/2018
ms.author: v-geberr
ms.openlocfilehash: c0451f7621a3c18dbf365f3a03934924c030092f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366436"
---
# <a name="collaborate-with-others-on-language-understanding-luis-apps"></a>与语言理解 (LUIS) 应用程序上的其他人进行协作  

你可以在 LUIS 应用上与其他人协作。 

## <a name="owner-and-collaborators"></a>所有者和协作者
一个应用只有一个所有者，但可以有许多协作者。 

## <a name="add-collaborator"></a>添加协作者

要允许协作者编辑 LUIS 应用，请在 LUIS 应用的“设置”页面上输入协作者的电子邮件，然后单击“添加协作者”。

![添加协作者](./media/luis-how-to-collaborate/add-collaborator.png)

* 协作者可以在你使用 LUIS 应用的同时登录和编辑你的 LUIS 应用。 <!--If a collaborator edits the LUIS app, you see a notification at the top of the browser.-->
* 协作者无法添加其他协作者。

## <a name="set-application-as-public"></a>将应用程序设置为公共
有关详细信息，请参阅[公共应用终结点访问](luis-concept-security.md#public-app-endpoint-access)。

### <a name="transfer-of-ownership"></a>转让所有权
虽然 LUIS 目前不支持转让所有权，但你可以导出应用，而其他 LUIS 用户可以导入该应用。 两个应用程序之间的 LUIS 分数可能存在细微差别。 
