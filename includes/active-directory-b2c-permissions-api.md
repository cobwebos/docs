---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: c1628894205754c1b8f1fc954796009afbdc8ed4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84680307"
---
#### <a name="app-registrations"></a>[应用注册](#tab/app-reg-ga/) 

1. 选择“应用注册”，然后选择应该有权访问 API 的 Web 应用程序。 例如，“webapp1”。
1. 在“管理”下选择“API 权限”。
1. 在“已配置权限”下，选择“添加权限”。
1. 选择“我的 API”选项卡。
1. 选择应授予 Web 应用程序对其的访问权限的 API。 例如，“webapi1”。
1. 在“权限”下展开“演示”，然后选择前面定义的范围。  例如，*demo.read* 和 *demo.write*。
1. 选择“添加权限”。
1. 选择“向(租户名称)授予管理员许可”。
1. 如果系统提示你选择一个帐户，请选择当前登录的管理员帐户，或者使用至少分配了“云应用程序管理员”角色的 Azure AD B2C 租户中的帐户登录。
1. 请选择“是”。
1. 选择“刷新”，然后确认两个范围的“状态”下是否均显示“已授予...”。 

#### <a name="applications-legacy"></a>[应用程序(旧版)](#tab/applications-legacy/)

1. 选择“应用程序(旧版)”，然后选择应该有权访问 API 的 Web 应用程序。 例如，“webapp1”。
1. 选择“API 访问”，然后选择“添加” 。
1. 在“选择 API”下拉列表中，选择应授予 Web 应用程序访问权限的 API。 例如，“webapi1”。
1. 在“选择范围”下拉列表中，选择先前定义的范围。 例如，*demo.read* 和 *demo.write*。
1. 选择“确定”。
