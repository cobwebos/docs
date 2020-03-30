---
title: 隔离的应用程序预配状态 |微软文档
description: 为自动用户预配配置应用程序后，请了解隔离预配状态的含义以及如何清除它。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563c049bf3d1606e87db54e3b003dac987594610
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154621"
---
# <a name="application-provisioning-in-quarantine-status"></a>隔离状态中的应用程序预配

Azure AD 预配服务监视配置的运行状况，并将不正常的应用置于"隔离"状态。 如果针对目标系统所做的大多数或所有调用始终由于错误（例如无效的管理员凭据）而失败，则预配作业将标记为隔离区。

在隔离期间，增量循环的频率逐渐降低为每天一次。 修复所有错误并启动下一个同步周期后，将从隔离区中删除预配作业。 如果预配作业在隔离区中停留超过四周，则预配作业将禁用（停止运行）。

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>如何知道我的申请是否处于隔离状态？

有三种方法可以检查应用程序是否处于隔离状态：
  
- 在 Azure 门户中，导航到**Azure 活动目录** > **企业** > &lt;*应用程序名称*&gt; > **预配**，然后滚动到底部的进度栏。  

  ![显示隔离状态的预配状态栏](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- 在 Azure 门户中，导航到**Azure 活动目录** > **审核日志**>"**活动：隔离**"和"检查隔离历史记录"上的筛选器。 虽然上述进度栏中的视图显示预配当前是否处于隔离状态，但审核日志允许您查看应用程序的隔离历史记录。 

- 使用 Microsoft 图形请求[获取同步作业](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http)以编程方式获取预配作业的状态：

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- 查看电子邮件。 将应用程序置于隔离区时，将发送一次性通知电子邮件。 如果隔离原因发生更改，将发送更新的电子邮件，显示隔离的新原因。 如果您没有看到电子邮件：

  - 请确保在为应用程序的预配配置中指定了有效的**通知电子邮件**。
  - 确保通知电子邮件收件箱中没有垃圾邮件筛选。
  - 请确保您没有取消订阅电子邮件。

## <a name="why-is-my-application-in-quarantine"></a>为什么我的申请是在隔离中？

Microsoft Graph 请求获取预配作业的状态，显示隔离的以下原因：

- `EncounteredQuarantineException`表示提供了无效凭据。 预配服务无法在源系统和目标系统之间建立连接。

- `EncounteredEscrowProportionThreshold`表示预配超过托管阈值。 当超过 60% 的预配事件失败时，将发生此情况。

- `QuarantineOnDemand`表示我们检测到您的应用程序出现问题，并手动将其设置为隔离。

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>如何将申请从隔离区获取？

首先，解决导致应用程序被置于隔离区的问题。

- 检查应用程序的预配设置，以确保您[输入了有效的管理员凭据](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning)。 Azure AD 必须能够与目标应用程序建立信任。 确保您已输入有效凭据，并且您的帐户具有必要的权限。

- 查看[预配日志](../reports-monitoring/concept-provisioning-logs.md)以进一步调查导致隔离的错误并解决错误。 通过访问"**活动"** 部分中的**Azure 活动目录**&gt;**企业应用**&gt;**预配日志（预览）** 来访问 Azure 门户中的预配日志。

解决问题后，重新启动预配作业。 对应用程序预配设置的某些更改（如属性映射或范围筛选器）将自动重新启动您的预配。 应用程序**预配**页上的进度栏指示上次开始预配时。 如果需要手动重新启动预配作业，请使用以下方法之一：  

- 使用 Azure 门户重新启动预配作业。 在 **"设置"** 下的应用程序**预配**页上，选择 **"清除状态并重新启动同步**"并将**预配状态**设置为 **"打开**"。 此操作将完全重新启动预配服务，这可能需要一些时间。 完整的初始周期将再次运行，这将清除埃斯库，从隔离中删除应用程序，并清除任何水印。

- 使用 Microsoft 图形[重新启动预配作业](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)。 您将完全控制重新启动的内容。 您可以选择清除代管（重新启动累积到隔离状态的代管计数器）、清除隔离（从隔离区中删除应用程序）或清除水印。 使用以下请求：
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`
