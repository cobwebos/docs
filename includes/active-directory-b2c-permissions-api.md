---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 462c1fca0ecd706c1bf04ac5a0ef8561321e05bc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474851"
---
#### <a name="applicationstabapplications"></a>[应用程序](#tab/applications/)

1. 选择“应用程序”  ，然后选择应该有权访问 API 的 Web 应用程序。 例如，“webapp1”  。
1. 选择“API 访问”，然后选择“添加”   。
1. 在“选择 API”  下拉列表中，选择应授予 Web 应用程序访问权限的 API。 例如，“webapi1”  。
1. 在“选择范围”  下拉列表中，选择先前定义的范围。 例如，*demo.read* 和 *demo.write*。
1. 选择“确定”  。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[应用注册(预览版)](#tab/app-reg-preview/)

1. 选择“应用注册(预览版)”  ，然后选择应该有权访问 API 的 Web 应用程序。 例如，“webapp1”  。
1. 在“管理”下选择“API 权限”  。 
1. 在“已配置权限”  下，选择“添加权限”  。
1. 选择“我的 API”  选项卡。
1. 选择应授予 Web 应用程序对其的访问权限的 API。 例如，“webapi1”  。
1. 在“权限”  下，展开“演示”  ，然后选择先前定义的范围。 例如，*demo.read* 和 *demo.write*。
1. 选择“添加权限”  。 按照指示等待几分钟，然后继续下一步。
1. 选择“向(租户名称)授予管理员许可”  。
1. 选择当前登录的管理员帐户，或者使用至少分配了“云应用程序管理员”  角色的 Azure AD B2C 租户中的帐户登录。
1. 选择“接受”  。
1. 选择“刷新”  ，然后验证两个范围的“状态”  下是否均显示“授权给...”。 传播权限可能需要几分钟时间。