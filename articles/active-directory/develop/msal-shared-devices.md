---
title: 共享设备模式概述
titleSuffix: Microsoft identity platform | Azure
description: 了解共享设备模式，以便为 Firstline 工作人员启用设备共享。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550235"
---
# <a name="overview-of-shared-device-mode"></a>共享设备模式概述

共享设备模式是 Azure Active Directory 的一项功能，它允许你构建支持 Firstline 辅助角色的应用程序，以及在部署了的设备上启用共享设备模式。

> [!NOTE]
> 此功能目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-are-firstline-workers"></a>什么是 Firstline 辅助角色？

Firstline 工作人员是零售员工、维护和现场代理、医疗人员以及其他不在计算机前面的用户，或者使用公司电子邮件进行协作。 以下部分介绍了支持 Firstline 工作人员的各个方面和挑战，并介绍了 Microsoft 提供的功能，使应用程序可供组织的 Firstline 工作人员使用。

### <a name="challenges-of-supporting-firstline-workers"></a>支持 Firstline 工作人员的挑战

启用 Firstline 工作流包括典型信息工作者通常不会提供的挑战。 此类挑战可能包括高周转率，并不太熟悉组织的核心生产力工具。 为了使其 Firstline 工作人员的工作，组织采用不同的策略。 有些应用程序采用自己的设备（BYOD）策略，其员工在其个人电话上使用业务应用程序，而其他人则为其员工提供共享设备，例如 Ipad 或 Android 平板电脑。

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>支持针对一个用户设计的设备上的多个用户

由于运行 iOS 或 Android 的移动设备是为单一用户设计的，因此，大多数应用程序会优化其对单个用户的使用体验。 此优化体验的一部分意味着跨应用程序启用单一登录，并使用户能够在其设备上登录。 当用户从应用程序中删除其帐户时，应用通常不会将其视为与安全相关的事件。 许多应用程序甚至会保留用户的凭据，以便快速登录。 即使您从移动设备中删除了某个应用程序，然后重新安装该应用程序，您甚至可能会遇到这种情况。

### <a name="global-sign-in-and-sign-out-sso"></a>全局登录和注销（SSO）

若要允许组织的员工在这些员工共享的设备池中使用其应用，开发人员需要启用相反的体验。 员工应该能够从池中选取一个设备，并在其移动期间执行单个手势来 "使其成为其所有人"。 在移动结束时，他们应该能够执行其他手势，以便在设备上全局注销，并删除所有个人和公司信息，使他们可以将其返回到设备池。 此外，如果员工忘记注销，则应在移动结束时、/或在一段非活动期后自动注销设备。

Azure Active Directory 使用称为**共享设备模式**的功能实现这些方案。

## <a name="introducing-shared-device-mode"></a>共享设备模式简介

如前所述，共享设备模式是 Azure Active Directory 的一项功能，它使你能够：

* 生成支持 Firstline 辅助角色的应用程序
* 将设备部署到 Firstline 工作线程并打开共享设备模式

### <a name="build-applications-that-support-firstline-workers"></a>生成支持 Firstline 辅助角色的应用程序

你可以通过使用 Microsoft 身份验证库（MSAL）来支持应用程序中的 Firstline 工作线程，并[Microsoft Authenticator 应用](../user-help/user-help-auth-app-overview.md)程序启用名为*共享设备模式*的设备状态。 当设备处于共享设备模式时，Microsoft 会为应用程序提供信息，使其能够根据设备上用户的状态修改用户数据。

支持的功能包括：

* 通过任何受支持的应用程序，**在用户设备范围内登录**。
* 通过任何受支持的应用程序，**注销用户设备**。
* **查询设备的状态**，以确定应用程序是否位于处于共享设备模式的设备上。
* **查询设备上用户的设备状态**，以确定自上次使用应用程序后是否发生了任何更改。

支持共享设备模式应同时被视为适用于你的应用程序的安全增强和功能升级，并有助于提高其在医疗保健和财务等高度管控环境中的采用。

用户需要确保其数据不会泄露给其他用户。 共享设备模式提供了有用的信号，可向应用程序指示应管理的更改已发生。 应用程序负责在每次使用应用程序时检查设备上用户的状态，并清除以前的用户数据。 这包括在多任务中从后台重新加载时的情况。 用户更改时，应确保已清除上一用户的数据，并且删除应用程序中显示的所有缓存数据。 建议你始终在向应用程序添加共享设备模式功能后执行全面的安全审核过程。

有关如何修改应用程序以支持共享设备模式的详细信息，请参阅本文末尾的[后续步骤](#next-steps)部分。

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>将设备部署到 Firstline 工作线程并打开共享设备模式

一旦你的应用程序支持共享设备模式并包含所需的数据和安全更改，你就可以将其播发为可供 Firstline 工作人员使用。

组织的设备管理员可以通过移动设备管理（MDM）解决方案（如 Microsoft Intune）将其设备和应用程序部署到其存储和工作区。 设置过程的一部分是将设备标记为*共享设备*。 管理员通过部署[Microsoft Authenticator 应用](../user-help/user-help-auth-app-overview.md)并通过配置参数设置共享设备模式来配置共享设备模式。 执行这些步骤后，所有支持共享设备模式的应用程序都将使用 Microsoft Authenticator 应用程序来管理其用户状态，并为设备和组织提供安全功能。

## <a name="next-steps"></a>后续步骤

支持共享设备模式的 iOS 和 Android 平台。 请查看以下文档，了解如何在应用程序中开始支持 Firstline 辅助角色。

* [支持适用于 iOS 的共享设备模式](msal-ios-shared-devices.md)
* [支持 Android 共享设备模式](msal-android-shared-devices.md)
