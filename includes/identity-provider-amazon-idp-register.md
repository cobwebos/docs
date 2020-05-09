---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 05/04/2020
ms.author: mimart
ms.openlocfilehash: de1b67265c5c3b3f7247b7f21506eed88abfd550
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900371"
---
1. 用 Amazon 帐户凭据登录[Amazon 开发人员控制台](https://developer.amazon.com/dashboard)。
1. 如果未曾登录过，请单击“注册”****，按照开发人员注册步骤，并接受策略。
1. 在仪表板中，选择 "**用 Amazon 登录**"。
1. 选择“创建新的安全配置文件”。****
1. 输入 "**安全配置文件名称**"、"**安全配置文件描述**" 和 "同意" `https://www.contoso.com/privacy` **隐私通知 url**，例如隐私声明 url 是您管理的、向用户提供隐私信息的页面。 然后单击“保存”  。
1. 在 "**用 Amazon 配置登录**" 部分中，选择你创建的**安全配置文件名称**，单击 "**管理**" 图标，然后选择 " **Web 设置**"。
1. 在“Web 设置”**** 部分中，复制“客户端 ID”**** 的值。 选择“显示机密”**** 来获取客户端机密，然后复制它。 将 Amazon 帐户配置为租户中的标识提供者时需要这两个值。 ****“客户端密钥”是一个很重要的安全凭据。
1. 在 " **Web 设置**" 部分中，选择 "**编辑**"。 在 "**允许的来源**" 和 "允许的**返回 url**" 中，输入相应的 url （如上所述）。 
1. 单击“保存”  。
