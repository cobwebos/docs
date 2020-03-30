---
title: 跳过删除范围外用户 |微软文档
description: 了解如何覆盖在作用域外用户取消预配的默认行为。
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1668c022a0f067a381ba09b397c7d38c99ad074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522443"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>跳过删除超出范围的用户帐户

默认情况下，Azure AD 预配引擎软删除或禁用超出范围的用户。 但是，对于某些方案（如工作日到 AD 用户入站预配）此行为可能不是预期的，您可能需要重写此默认行为。  

本指南介绍如何使用 Microsoft 图形 API 和 Microsoft 图形 API 资源管理器设置标志***SkipOutScopeDeletions，*** 用于控制超出范围的帐户的处理。 
* 如果***SkipOutScope 删除***设置为 0（false），则超出范围的帐户将在目标中禁用
* 如果***SkipOutScope 删除***设置为 1（true），则超出范围的帐户将不会在目标中禁用此标志设置在*预配应用*级别，并且可以使用图形 API 进行配置。 

由于此配置与*工作日到活动目录用户预配*应用广泛应用，以下步骤包括工作日应用程序的屏幕截图。 但是，这也可以用于**所有其他应用**，如服务现在、销售队伍、Dropbox 等）。

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>第 1 步：检索预配应用服务主体 ID（对象 ID）

1. 启动[Azure 门户](https://portal.azure.com)，然后导航到预配应用程序的"属性"部分。 例如，如果要将*工作日导出到 AD 用户预配应用程序映射*，请导航到该应用的"属性"部分。 
1. 在预配应用的“属性”部分中，复制与“对象 ID”字段关联的 GUID 值**。 此值也称为应用的 ServicePrincipalId，它将用于 Graph 浏览器操作****。

   ![Workday 应用服务主体 ID](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>第 2 步：登录微软图形资源管理器

1. 启动 [Microsoft Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)
1. 单击“使用 Microsoft 登录”按钮，然后使用 Azure AD 全局管理员或应用管理员凭据登录。

    ![Graph 登录](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. 成功登录后，你将在左侧窗格中看到用户帐户详细信息。

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>第 3 步：获取现有应用凭据和连接详细信息

在 Microsoft Graph 浏览器中，运行以下 GET 查询，将 [servicePrincipalId] 替换为从[步骤 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id) 中提取的 ServicePrincipalId****。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![获取作业查询](./media/skip-out-of-scope-deletions/skip-03.png)

将响应复制到文本文件中。 它将类似于下面的 JSON 文本，以特定于部署的黄色突出显示值。 将以绿色突出显示的行添加到末尾，并更新以蓝色突出显示的工作日连接密码。 

   ![获取工作响应](./media/skip-out-of-scope-deletions/skip-04.png)

下面是要添加到映射的 JSON 块。 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>第 4 步：使用"跳过退出删除"标志更新机密终结点

在图形资源管理器中，运行下面的命令，使用***SkipOutScope 删除***标志更新机密终结点。 

在下面的 URL 中，将 [服务主体Id] 替换为从步骤[1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)中提取**的服务主体Id。** 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
将步骤 3 中更新的文本复制到"请求正文"中，并将标题"内容类型"设置为"请求标题"中的"应用程序/json"。 

   ![PUT 请求](./media/skip-out-of-scope-deletions/skip-05.png)

单击"运行查询"。 

您应该将输出作为"成功 + 状态代码 204"。 

   ![PUT 响应](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>第 5 步：验证范围外用户未被禁用

您可以通过更新范围规则来跳过特定用户来测试此标志导致预期行为。 在下面的示例中，我们添加了新的范围规则，从而排除了 ID 21173（在作用域中较早）的员工： 

   ![范围界定示例](./media/skip-out-of-scope-deletions/skip-07.png)

在下一个预配周期中，Azure AD 预配服务将标识用户 21173 已退出范围，如果启用了 SkipOutScope 删除属性，则该用户的同步规则将显示如下所示的消息： 

   ![范围界定示例](./media/skip-out-of-scope-deletions/skip-08.png)


