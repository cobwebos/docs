---
title: 隔离的应用程序预配状态 |Microsoft Docs
description: 为应用程序配置了自动用户预配后，请了解 "隔离" 的预配状态以及如何清除它。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: c1e0039133b7f9a7ae827e348640f6379b7f10ac
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593924"
---
# <a name="application-provisioning-in-quarantine-status"></a>隔离状态的应用程序设置

Azure AD 预配服务监视配置的运行状况，并将不正常的应用置于 "隔离" 状态。 如果对目标系统进行的大部分或全部调用由于错误（例如无效的管理员凭据）而失败，则设置作业会标记为隔离。

在隔离过程中，增量循环的频率会逐渐减少到每天一次。 修复所有错误并启动下一个同步周期后，将从隔离区中删除该设置作业。 如果预配作业处于隔离状态的时间超过四周，则会禁用设置作业（停止运行）。

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>如何实现知道我的应用程序是否处于隔离区？

有三种方法可以检查应用程序是否处于隔离区：
  
- 在 Azure 门户中，导航到**Azure Active Directory** > **企业应用** > &lt;*程序应用程序名称*&gt; > **预配**，并查看隔离消息的进度栏。   

  ![显示隔离状态的设置状态栏](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- 在 Azure 门户中，导航到 " **Azure Active Directory** > **审核日志**" > "对活动进行筛选" **： "隔离**" 并查看隔离历史记录。 上面所述的进度栏中的视图显示预配当前是否处于隔离中，审核日志允许你查看应用程序的隔离历史记录。 

- 使用 Microsoft Graph 请求[Get synchronizationJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http)以编程方式获取设置作业的状态：

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- 查看电子邮件。 当应用程序位于隔离区时，将发送一次性通知电子邮件。 如果隔离原因发生更改，则会发送更新的电子邮件，显示隔离的新原因。 如果看不到电子邮件：

  - 请确保已在应用程序的设置配置中指定了有效的**通知电子邮件**。
  - 请确保在通知电子邮件收件箱中没有垃圾邮件筛选。
  - 请确保未从电子邮件取消订阅。

## <a name="why-is-my-application-in-quarantine"></a>为什么应用程序在隔离中？

|说明|建议的操作|
|---|---|
|**SCIM 合规性问题：** 返回了 HTTP/404 找不到的响应，而不是预期的 HTTP/200 OK 响应。 在这种情况下，Azure AD 预配服务已向目标应用程序发出请求，并收到意外响应。|检查 "管理员凭据" 部分，以查看应用程序是否需要指定租户 URL 并确保 URL 正确。 如果看不到问题，请与应用程序开发人员联系，以确保其服务符合 SCIM。 https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**无效的凭据：** 尝试授予对目标应用程序的访问权限时，我们从目标应用程序收到指示提供的凭据无效的响应。|请导航到预配配置 UI 的 "管理员凭据" 部分，并使用有效的凭据再次授权访问权限。 如果应用程序在库中，请查看应用程序配置教程，了解所需的任何其他步骤。|
|**重复角色：** 从某些应用程序（如 Salesforce 和 Zendesk）导入的角色必须是唯一的。 |导航到 Azure 门户中的应用程序[清单](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)，并删除重复的角色。|

 获取设置作业状态的 Microsoft Graph 请求将显示以下隔离原因：

- `EncounteredQuarantineException`指示提供的凭据无效。 预配服务无法在源系统和目标系统之间建立连接。

- `EncounteredEscrowProportionThreshold`指示预配超出了保管阈值。 如果预配事件超过60%，则会出现此情况。

- `QuarantineOnDemand`表示我们检测到应用程序存在问题，并已将其手动设置为隔离。

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>如何实现将应用程序隔离到隔离区？

首先，请解决导致应用程序处于隔离中的问题。

- 请检查应用程序的设置设置，以确保[输入了有效的管理凭据](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning)。 Azure AD 必须能够建立与目标应用程序的信任关系。 请确保输入的凭据有效，并且帐户具有所需的权限。

- 查看[预配日志](../reports-monitoring/concept-provisioning-logs.md)，进一步调查导致隔离和解决错误的错误。 转到 "**活动**" 部分中的**Azure Active Directory** &gt; **Enterprise Apps** &gt; **预配日志（预览版）** ，访问 Azure 门户中的设置日志。

解决问题后，请重新启动设置作业。 对应用程序的预配设置的某些更改（如属性映射或范围筛选器）将自动重新启动设置。 应用程序的 "**设置**" 页上的进度条指示预配上次启动的时间。 如果需要手动重新启动设置作业，请使用以下方法之一：  

- 使用 Azure 门户重启预配作业。 在应用程序的 "设置 **" 页的**"**设置**" 下，选择 "**清除状态并重新启动同步**"，并将设置**状态**设置为**On**。 此操作完全重启预配服务，这可能需要一些时间。 将再次运行完整的初始周期，这会清除 escrows，从隔离区中删除应用程序，并清除所有水印。

- 使用 Microsoft Graph[重启预配作业](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)。 你可以完全控制重新启动的内容。 你可以选择清除 escrows （以重新启动向隔离状态累算的保管计数器）、清除隔离（从隔离区删除应用程序）或清除水印。 使用以下请求：
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`
