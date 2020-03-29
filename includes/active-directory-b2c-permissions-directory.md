---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: bf8b9370abc58902d9c751505f633dee852210a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184330"
---
#### <a name="applications"></a>[应用程序](#tab/applications/)

1. 在“已注册的应用”概述页上，选择“设置”。********
1. 在**API 访问**下，选择 **"必需权限**"。
1. 选择“Microsoft Graph”。****
1. 在 **"应用程序权限**"下，选择授予管理应用程序的权限复选框。 例如：
    * **阅读所有审核日志数据**：选择此权限以读取目录的审核日志。
    * **读取和写入目录数据**：为用户迁移或用户管理方案选择此权限。
    * **读取和写入组织的信任框架策略**：选择此权限以进行持续集成/持续交付 （CI/CD） 方案。 例如，使用 Azure 管道进行自定义策略部署。
1. 选择“保存”。****
1. 选择“授予权限”，然后选择“是”********。 可能需要几分钟才能完全传播权限。

#### <a name="app-registrations-preview"></a>[应用注册（预览版）](#tab/app-reg-preview/)

1. 在“管理”下选择“API 权限”****。****
1. 在“已配置权限”**** 下，选择“添加权限”****。
1. 选择**Microsoft API**选项卡，然后选择 **"微软图形**"。
1. 选择“应用程序权限”。****
1. 展开相应的权限组，然后选择授予管理应用程序的权限复选框。 例如：
    * **审核日志** > **审核日志.阅读.所有**：用于阅读目录的审核日志。
    * **目录** > **.ReadWrite.所有**：用于用户迁移或用户管理方案。
    * **策略** > **策略.ReadWrite.TrustFramework**：用于持续集成/持续交付 （CI/CD） 方案。 例如，使用 Azure 管道进行自定义策略部署。
1. 选择“添加权限”****。 按照指示等待几分钟，然后继续下一步。
1. 选择“向(租户名称)授予管理员许可”****。
1. 选择当前登录的管理员帐户，或者使用至少分配了“云应用程序管理员”** 角色的 Azure AD B2C 租户中的帐户登录。
1. 选择“接受”****。
1. 选择 **"刷新**"，然后验证"已授予..."显示在**状态**下。 传播权限可能需要几分钟时间。
