---
title: 共享设备模式概述
titleSuffix: Microsoft identity platform | Azure
description: 了解共享设备模式，以便为一线工作人员启用设备共享。
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 514782d62f117af5bfff4a5d2b3354c4e263eece
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550235"
---
# <a name="overview-of-shared-device-mode"></a>共享设备模式概述

共享设备模式是 Azure 活动目录的一项功能，允许您在部署到它们的设备上生成支持一线辅助功能的应用程序并启用共享设备模式。

> [!NOTE]
> 此功能目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-are-firstline-workers"></a>什么是一线员工？

第一线员工是零售员工、维护和现场代理、医务人员以及不坐在计算机前或使用公司电子邮件进行协作的其他用户。 以下各节介绍支持一线工作人员的各个方面和挑战，然后介绍 Microsoft 提供的功能，这些功能使您的应用程序可供组织的一线工作人员使用。

### <a name="challenges-of-supporting-firstline-workers"></a>支持一线员工的挑战

启用一线工作人员工作流包括典型信息工作者通常未提出的挑战。 这些挑战可能包括高更替率和对组织核心生产力工具的不熟悉程度。 为了增强他们的一线员工的能力，组织正在采取不同的战略。 一些国家正在采用自带设备 （BYOD） 策略，其员工在其个人手机上使用业务应用，而另一些员工则为其员工提供 iPad 或 Android 平板电脑等共享设备。

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>支持为一个用户设计的设备上的多个用户

由于运行 iOS 或 Android 的移动设备专为单个用户设计，因此大多数应用程序会优化其体验，供单个用户使用。 这种优化体验的一部分意味着跨应用程序启用单一登录，并在其设备上保持用户登录。 当用户从应用程序中删除其帐户时，应用程序通常不会将其视为与安全相关的事件。 许多应用甚至保留用户的凭据以快速登录。 当您从移动设备中删除应用程序然后重新安装应用程序，但发现您仍处于登录中时，您甚至可能自己体验过这种情况。

### <a name="global-sign-in-and-sign-out-sso"></a>全局登录和注销 （SSO）

为了允许组织员工在这些员工共享的设备池中使用其应用，开发人员需要启用相反的体验。 员工应该能够从池中挑选设备，并在轮班期间执行单个手势以"使其成为自己的设备"。 轮班结束时，他们应该能够执行另一个手势，在设备上全局注销，并删除其所有个人和公司信息，以便将其返回到设备池。 此外，如果员工忘记注销，设备应在轮班结束时和/或处于非活动状态后自动注销。

Azure 活动目录使用称为**共享设备模式**的功能启用这些方案。

## <a name="introducing-shared-device-mode"></a>引入共享设备模式

如前所述，共享设备模式是 Azure 活动目录的一项功能，使您能够：

* 生成支持一线工作人员的应用程序
* 将设备部署到一线工作人员并打开共享设备模式

### <a name="build-applications-that-support-firstline-workers"></a>生成支持一线工作人员的应用程序

您可以使用 Microsoft 身份验证库 （MSAL） 和[Microsoft 身份验证器应用](../user-help/user-help-auth-app-overview.md)来启用称为*共享设备模式*的设备状态，从而支持应用程序中的第一线工作人员。 当设备处于共享设备模式时，Microsoft 会向应用程序提供信息，以允许它根据设备上的用户状态修改其行为，从而保护用户数据。

支持的功能包括：

* 通过任何受支持的应用程序**在用户设备范围内登录**。
* 通过任何受支持的应用程序**在用户设备上注销**。
* **查询设备的状态，** 以确定应用程序是否位于处于共享设备模式的设备上。
* **查询设备上用户的设备状态**，以确定自上次使用应用程序以来是否发生了任何更改。

支持共享设备模式应被视为应用程序的安全增强和功能升级，并有助于在高度监管的环境中（如医疗保健和财务）中增加其采用率。

您的用户依赖您来确保其数据不会泄露给其他用户。 共享设备模式提供有用的信号，以向应用程序指示您应该管理的更改已发生。 每次使用应用时，应用程序负责检查设备上的用户状态，从而清除前一个用户的数据。 这包括如果在多任务处理中从后台重新加载它，则包括它。 在用户更改时，应确保清除前一个用户的数据，并删除应用程序中显示的任何缓存数据。 我们建议您在将共享设备模式功能添加到应用后，始终执行彻底的安全检查过程。

有关如何修改应用程序以支持共享设备模式的详细信息，请参阅本文末尾的["后续步骤](#next-steps)"部分。

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>将设备部署到一线工作人员并打开共享设备模式

一旦应用程序支持共享设备模式并包含所需的数据和安全更改，就可以宣传它们可供一线工作人员使用。

组织的设备管理员能够通过 Microsoft Intune 等移动设备管理 （MDM） 解决方案将其设备和应用程序部署到其商店和工作场所。 预配过程的一部分是将设备标记为*共享设备*。 管理员通过部署[Microsoft 身份验证器应用](../user-help/user-help-auth-app-overview.md)并通过配置参数设置共享设备模式来配置共享设备模式。 执行这些步骤后，支持共享设备模式的所有应用程序都将使用 Microsoft 身份验证器应用程序来管理其用户状态，并为设备和组织提供安全功能。

## <a name="next-steps"></a>后续步骤

我们支持 iOS 和 Android 平台，支持共享设备模式。 请查看以下文档，了解您的平台，以开始在应用程序中支持一线工作人员。

* [支持 iOS 的共享设备模式](msal-ios-shared-devices.md)
* [支持 Android 的共享设备模式](msal-android-shared-devices.md)
