---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/27/2019
ms.author: marsma
ms.openlocfilehash: 3b6f8ef5f2ec9f77dcba9c006fdd28b5ec668033
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315021"
---
与 Azure AD B2C 通信是通过在 B2C 租户中注册的应用程序进行的。 本部分列出了可用于创建测试应用程序的可选步骤（如果尚未创建）。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择 " **Azure AD B2C**"。 或者选择 "**所有服务**"，搜索并选择 " **Azure AD B2C**"。
1. 选择“应用程序”，然后选择“添加”。
1. 输入应用程序的名称。 例如， *testapp1-template.json*。
1. 对于**Web 应用/WEB API**，请选择 **"是"** 。
1. 对于 "**答复 URL**"，输入`https://jwt.ms`
1. 选择“创建”。
