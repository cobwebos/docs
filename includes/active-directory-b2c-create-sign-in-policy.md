---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 0ab34d6234db9c13ffe82ccd0e8580217085f631
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "58114916"
---
如果仅希望在应用程序上启用登录，请使用**登录**用户流。 此用户流描述了客户在登录过程中将经历的体验以及应用程序在成功登录时会接收到的令牌内容。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]
在“管理”下，选择“用户流”。

单击边栏选项卡顶部的“+ 新建用户流”。

在“选择用户流类型”下，选择“全部”，然后选择要使用的**登录**版本。

“名称”确定应用程序使用的登录用户流名称。 例如，输入 **SiIn**。

在“标识提供者”下，选择一个选项。 也可以选择社交标识提供者（如果已配置）。 单击“确定”。

在“应用程序声明”下，单击“显示更多”。

在“返回声明”列中，可以选择希望在成功登录体验后发回到应用程序的令牌中返回的声明。 例如，选择“显示名称”、“标识提供者”、“邮政编码”和“用户的对象 ID”。 单击“确定”。

单击“创建”。 请注意，刚刚创建的用户流显示为“B2C_1_SiIn”（自动添加 **B2C\_1\_** 片段）。

单击“运行用户流”。

在“应用程序”下拉列表中选择“Contoso B2C 应用”，并在“回复 URL”下拉列表中选择 `https://localhost:44321/`。

单击“运行用户流”。 将打开一个新的浏览器选项卡，可以运行登录到应用程序的使用者体验。

> [!NOTE]
> 用户流创建和更新最多需要一分钟才能生效。
>