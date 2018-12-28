---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 17c0213d63879687e9c6d5f8dca06b9113c44af8
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742409"
---
如果仅希望在应用程序上启用注册，请使用**注册**用户流。 此用户流描述了客户在注册过程中将经历的体验以及应用程序在成功注册之后会接收到的令牌内容。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

在“管理”下，选择“用户流”。

单击边栏选项卡顶部的“+ 新建用户流”。

在“选择用户流类型”下，选择“全部”，然后选择要使用的**注册**版本。

“名称”确定应用程序使用的注册用户流名称。 例如，输入 **SiUp**。

在“标识提供者”下，选择“电子邮件注册”。 或者，也可以选择社交标识提供者（如果已配置）。

在“用户特性和声明”下，单击“显示更多”。

在“收集特性”列中，选择要在注册期间从客户收集的特性。 例如，选择“国家/地区”、“显示名称”和“邮政编码”。

在“返回声明”列中，可以选择希望在成功完成注册体验后在发回到应用程序的令牌中返回的声明。 例如，选择“显示名称”、“标识提供者”、“邮政编码”、“用户是新用户”和“用户的对象 ID”。

单击“确定”。

单击“创建”。 创建的用户流将显示为 **B2C_1_SiUp**（**B2C\_1\_** 片段是自动添加的）。

单击“运行用户流”。

在“应用程序”下拉列表中选择“Contoso B2C 应用”，并在“回复 URL”下拉列表中选择 `https://localhost:44321/`。

单击“运行用户流”。 将打开一个新的浏览器选项卡，可以运行注册应用程序的使用者体验。

> [!NOTE]
> 用户流创建和更新最多需要一分钟才能生效。
>