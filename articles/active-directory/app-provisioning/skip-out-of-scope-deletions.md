---
title: 跳过删除超出范围用户的操作
description: 了解如何重写取消预配超出作用域用户的默认行为。
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: article
ms.workload: identity
ms.date: 12/10/2019
ms.author: chmutali
ms.reviewer: celested
ms.openlocfilehash: 5f17886736efb87cf44bc54c82ccca794482a093
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593261"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>跳过删除超出范围的用户帐户

默认情况下，Azure AD 预配引擎软删除或禁用超出范围的用户。 但是，在某些情况下，对于 Workday 到 AD 用户的入站预配，此行为可能不是预期的，你可能想要重写此默认行为。  

本指南介绍如何使用 Microsoft Graph API 和 Microsoft Graph API 资源管理器来设置标志***SkipOutOfScopeDeletions*** ，以控制超出作用域的帐户的处理。 
* 如果将***SkipOutOfScopeDeletions***设置为0（false），则将在目标中禁用超出范围的帐户
* 如果将***SkipOutOfScopeDeletions***设置为1（true），则不会在目标中禁用超出范围的帐户，此标志在*预配应用*级别设置，可以使用图形 API 进行配置。 

由于此配置广泛用于*workday Active Directory 用户预配*应用，下面的步骤包括 Workday 应用程序的屏幕截图。 但这也可用于**所有其他应用**，如 ServiceNow、Salesforce、Dropbox 等。

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>步骤1：检索预配应用服务主体 ID （对象 ID）

1. 启动[Azure 门户](https://portal.azure.com)，并导航到预配应用程序的 "属性" 部分。 例如，如果要将*Workday 导出到 AD 用户预配应用程序*映射，请导航到该应用的 "属性" 部分。 
1. 在预配应用的“属性”部分中，复制与“对象 ID”字段关联的 GUID 值**。 此值也称为应用的 ServicePrincipalId，它将用于 Graph 浏览器操作****。

   ![Workday 应用服务主体 ID](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>步骤2：登录 Microsoft Graph 资源管理器

1. 启动 [Microsoft Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)
1. 单击“使用 Microsoft 登录”按钮，然后使用 Azure AD 全局管理员或应用管理员凭据登录。

    ![Graph 登录](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. 成功登录后，你将在左侧窗格中看到用户帐户详细信息。

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>步骤3：获取现有应用凭据和连接详细信息

在 Microsoft Graph 浏览器中，运行以下 GET 查询，将 [servicePrincipalId] 替换为从[步骤 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id) 中提取的 ServicePrincipalId****。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![获取作业查询](./media/skip-out-of-scope-deletions/skip-03.png)

将响应复制到文本文件中。 它看起来将类似于下面所示的 JSON 文本，其值在特定于您的部署的黄色突出显示。 将绿色突出显示的行添加到末尾，并更新以蓝色突出显示的 Workday 连接密码。 

   ![获取作业响应](./media/skip-out-of-scope-deletions/skip-04.png)

下面是要添加到映射的 JSON 块。 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>步骤4：用 SkipOutOfScopeDeletions 标志更新机密终结点

在图形资源管理器中运行以下命令，以通过***SkipOutOfScopeDeletions***标志更新机密终结点。 

在下面的 URL 中，将 [servicePrincipalId] 替换为从[步骤 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)中提取的**servicePrincipalId** 。 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
将步骤3中的已更新文本复制到 "请求正文"，并在 "请求标头" 中将标头 "Content-type" 设置为 "application/json"。 

   ![PUT 请求](./media/skip-out-of-scope-deletions/skip-05.png)

单击 "运行查询"。 

应该会看到 "成功-状态代码 204" 的输出。 

   ![PUT 响应](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>步骤5：验证超出范围的用户是否未被禁用

你可以通过更新范围规则以跳过特定用户来测试此标志的行为。 在下面的示例中，通过添加新的范围规则，排除 ID 为21173的员工（作用域之前）： 

   ![作用域示例](./media/skip-out-of-scope-deletions/skip-07.png)

在下一个预配周期中，Azure AD 预配服务将确定用户21173已超出范围，如果启用了 SkipOutOfScopeDeletions 属性，则该用户的同步规则将显示一条消息，如下所示： 

   ![作用域示例](./media/skip-out-of-scope-deletions/skip-08.png)


