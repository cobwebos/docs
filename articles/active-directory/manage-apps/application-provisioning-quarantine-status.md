---
title: 隔离的应用程序预配状态 |Microsoft Docs
description: 为应用程序配置了自动用户预配后，请了解 "隔离" 的预配状态以及如何清除它。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 933217b2ad86535d45f6674165ee162f263a8cd7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712174"
---
# <a name="application-provisioning-in-quarantine-status"></a>隔离状态的应用程序设置

Azure AD 预配服务监视配置的运行状况，并将不正常的应用置于 "隔离" 状态。 如果对目标系统进行的大部分或全部调用由于错误（例如无效的管理员凭据）而失败，则设置作业会标记为隔离。

在隔离过程中，增量循环的频率会逐渐减少到每天一次。 修复所有错误并启动下一个同步周期后，将从隔离区中删除该设置作业。 如果预配作业处于隔离状态的时间超过四周，则会禁用设置作业（停止运行）。

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>如何实现知道我的应用程序是否处于隔离区？

有三种方法可以检查应用程序是否处于隔离区：
  
- 在 Azure 门户中，导航到**Azure Active Directory** > **企业应用程序** > &lt;&gt;**预配**的*应用程序名称*，并滚动到底部的进度栏。  

  ![显示隔离状态的设置状态栏](media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- 使用 Microsoft Graph 请求[Get synchronizationJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http)以编程方式获取设置作业的状态：

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- 检查电子邮件。 当应用程序位于隔离区时，将发送一次性通知电子邮件。 如果隔离原因发生更改，则会发送更新的电子邮件，显示隔离的新原因。 如果看不到电子邮件：

  - 请确保已在应用程序的设置配置中指定了有效的**通知电子邮件**。
  - 请确保在通知电子邮件收件箱中没有垃圾邮件筛选。
  - 请确保未从电子邮件取消订阅。

## <a name="why-is-my-application-in-quarantine"></a>为什么应用程序在隔离中？

获取设置作业状态的 Microsoft Graph 请求将显示以下隔离原因：

- `EncounteredQuarantineException` 指示提供的凭据无效。 预配服务无法在源系统和目标系统之间建立连接。

- `EncounteredEscrowProportionThreshold` 指示预配超出了保管阈值。 如果预配事件超过60%，则会出现此情况。

- `QuarantineOnDemand` 意味着我们检测到应用程序存在问题，并已将其手动设置为隔离。

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>如何实现将应用程序隔离到隔离区？

首先，请解决导致应用程序处于隔离中的问题。

- 请检查应用程序的设置设置，以确保[输入了有效的管理凭据](configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning)。 Azure AD 必须能够建立与目标应用程序的信任关系。 请确保输入的凭据有效，并且帐户具有所需的权限。

- 查看[预配日志](../reports-monitoring/concept-provisioning-logs.md)，进一步调查导致隔离和解决错误的错误。 转到 "**活动**" 部分中的**Azure Active Directory** &gt;**企业应用**&gt;**预配日志（预览版）** 来访问 Azure 门户中的设置日志。

解决问题后，请重新启动设置作业。 对应用程序的预配设置的某些更改（如属性映射或范围筛选器）将自动重新启动设置。 应用程序的 "**设置**" 页上的进度条指示预配上次启动的时间。 如果需要手动重新启动设置作业，请使用以下方法之一：  

- 使用 Azure 门户重启预配作业。 在应用程序的 "设置 **" 页的**"**设置**" 下，选择 "**清除状态并重新启动同步**"，并将设置**状态**设置为**On**。 此操作完全重启预配服务，这可能需要一些时间。 将再次运行完整的初始周期，这会清除 escrows，从隔离区中删除应用程序，并清除所有水印。

- 使用 Microsoft Graph[重启预配作业](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)。 你可以完全控制重新启动的内容。 你可以选择清除 escrows （以重新启动向隔离状态累算的保管计数器）、清除隔离（从隔离区删除应用程序）或清除水印。 다음 요청을 사용합니다.
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`