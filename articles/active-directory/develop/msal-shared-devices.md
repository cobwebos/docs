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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80550235"
---
# <a name="overview-of-shared-device-mode"></a>共享设备模式的概述

共享设备模式是 Azure Active Directory 的一项功能，使用该模式可以生成能够为一线工作人员提供支持的应用程序，并在部署给这些人员的设备上启用共享设备模式。

> [!NOTE]
> 此功能目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-are-firstline-workers"></a>什么是一线工作人员？

一线工作人员是指不操作计算机的，或者不使用企业电子邮件进行协作的零售员工、维护和现场代理人、医疗人员以及其他用户。 以下部分将介绍为一线工作人员提供支持所要注意的各个方面和挑战，然后介绍 Microsoft 所提供的功能，这些功能使你的应用程序可供组织中的一线工作人员使用。

### <a name="challenges-of-supporting-firstline-workers"></a>为一线工作人员提供支持所要面对的挑战

启用一线工作人员工作流涉及到普通信息工作者通常不会面临的挑战。 此类挑战可能包括较高的人事变动率，以及工作人员不太熟悉组织的核心生产力工具。 组织需要采用不同的策略来为其一线工作人员提供支持。 某些组织采用自带设备 (BYOD) 策略（在这种情况下，其员工可以在其个人手机上使用业务应用），而其他组织则为其员工提供 iPad 或 Android 平板电脑等共享设备。

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>在专供一个用户使用的设备上支持多个用户

由于运行 iOS 或 Android 的移动设备是为单一用户设计的，因此，大多数应用程序的体验是针对供单个用户使用而优化的。 这种经过优化的体验有一部分意味着，跨应用程序启用单一登录并使用户在其设备上保持登录状态。 当用户从某个应用程序中删除其帐户时，该应用通常不会将此操作视为安全相关的事件。 许多应用甚至会就近保留用户的凭据，以便快速登录。 你甚至亲身体验过这种情况：从移动设备中删除了某个应用程序，再重新安装该应用程序，然后你发现自己仍保持登录状态。

### <a name="global-sign-in-and-sign-out-sso"></a>全局登录和注销 (SSO)

为使组织的员工能够在他们共享的整个设备池中使用其应用，开发人员需要启用相反的体验。 员工应该能够从池中选取一个设备，并通过执行一个操作，使该设备在他们当班期间变成“专供其使用的设备”。 在其班次结束时，他们应该能够执行另一个操作以在设备上全局注销，并删除其所有个人信息和公司信息，以便可将该设备退回到设备池。 此外，如果某个员工忘记了注销，该设备应该在该员工的班次结束时和/或处于非活动状态一段时间后自动注销。

Azure Active Directory 使用称作“共享设备模式”的功能来实现这些方案  。

## <a name="introducing-shared-device-mode"></a>共享设备模式简介

如前所述，共享设备模式是 Azure Active Directory 的一项功能，可用于：

* 生成可为一线工作人员提供支持的应用程序
* 将设备部署给一线工作人员并启用共享设备模式

### <a name="build-applications-that-support-firstline-workers"></a>生成可为一线工作人员提供支持的应用程序

你可以通过使用 Microsoft 身份验证库（MSAL）来支持应用程序中的 Firstline 工作线程，并[Microsoft Authenticator 应用](../user-help/user-help-auth-app-overview.md)程序启用名为*共享设备模式*的设备状态。 当设备处于共享设备模式时，Microsoft 将为应用程序提供所需的信息，使应用程序能够根据设备上用户的状态修改其行为，从而保护用户数据。

支持的功能包括：

* 通过任何受支持的应用程序在整个设备上将用户登录  。
* 通过任何受支持的应用程序在整个设备上将用户注销  。
* 查询设备的状态，以确定应用程序是否位于处于共享设备模式的设备上  。
* 查询设备上用户的设备状态，以确定自上次使用应用程序以来是否发生了任何更改  。

支持共享设备模式应被视为应用程序的安全增强和功能升级，并且有助于提高应用程序在严格管制的环境（例如医疗保健和金融行业）中的采用率。

用户依赖于你来确保其数据不会泄露给其他用户。 共享设备模式提供了有用的信号，用于向应用程序指示已发生了需要处理的更改。 每次使用你的应用程序时，该应用都要负责检查设备上用户的状态，并清除以前的用户数据。 这还包括检查应用是不是从正在执行多个任务的后台重新加载的。 发生用户更改后，你应确保前一个用户的数据已清除，并且应用程序中显示的所有缓存数据已删除。 在向应用添加共享设备模式功能后，我们建议始终执行全面的安全评审过程。

有关如何修改应用程序以支持共享设备模式的详细信息，请参阅本文末尾的[后续步骤](#next-steps)部分。

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>将设备部署给一线工作人员并启用共享设备模式

一旦应用程序支持共享设备模式并包含所需的数据和安全更改，你就可以播发这些更改，使其可供一线工作人员使用。

组织的设备管理员可以通过 Microsoft Intune 等移动设备管理 (MDM) 解决方案，将其设备和你的应用程序部署到其存储和工作区中。 预配过程的一部分工作是将设备标记为共享设备  。 管理员通过部署[Microsoft Authenticator 应用](../user-help/user-help-auth-app-overview.md)并通过配置参数设置共享设备模式来配置共享设备模式。 执行这些步骤后，所有支持共享设备模式的应用程序都将使用 Microsoft Authenticator 应用程序来管理其用户状态，并为设备和组织提供安全功能。

## <a name="next-steps"></a>后续步骤

我们支持在 iOS 和 Android 平台中使用共享设备模式。 请查看以下适用于你的平台的文档，以便开始在应用程序中为一线工作人员提供支持。

* [支持适用于 iOS 的共享设备模式](msal-ios-shared-devices.md)
* [支持适用于 Android 的共享设备模式](msal-android-shared-devices.md)
