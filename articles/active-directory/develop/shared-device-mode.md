---
title: 适用于 Android 设备的共享设备模式 |Microsoft
description: 了解共享设备模式，这允许 firstline 工作人员共享 Android 设备
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: twhitney
ms.reviwer: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 53fa82cf6eaaba09353ba21a12ae9677b9264b1a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701461"
---
# <a name="shared-device-mode-for-android-devices"></a>适用于 Android 设备的共享设备模式

> [!NOTE]
> 此功能目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Firstline 工作人员（例如零售协会、航班员工和现场服务工作人员）通常使用共享移动设备来完成工作。 当他们开始共享密码或 pin 号码以访问共享设备上的客户和业务数据时，这将会出现问题。

使用共享设备模式可以配置 Android 设备，以便多名员工可以轻松共享该设备。 员工可快速登录并访问客户信息。 当他们完成了其移动或任务后，他们可以从设备注销，并立即准备就绪，以便下一名员工使用。

共享设备模式还为设备提供了 Microsoft 身份支持的管理。

若要创建共享设备模式应用，开发人员和云设备管理员可以一起工作：

- 开发人员编写单帐户应用（共享设备模式下不支持多帐户应用），将 `"shared_device_mode_supported": true` 添加到应用的配置，并编写代码来处理共享设备注销等任务。
- 设备管理员通过安装验证器应用，并使用验证器应用将设备设置为共享模式，来准备要共享的设备。 只有处于[云设备管理员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#cloud-device-administrator)角色中的用户才能使用[验证器应用](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview)将设备置于共享模式。 你可以通过以下方式在 Azure 门户中配置组织角色的成员身份： **Azure Active Directory** > **角色和管理员** > **云设备管理员**。

 本文重点介绍开发人员应考虑的事项。

## <a name="single-vs-multiple-account-applications"></a>单个和多个帐户应用程序

使用 Microsoft 身份验证库 SDK （MSAL）编写的应用程序可以管理一个或多个帐户。 有关详细信息，请参阅[单一帐户模式或多帐户模式](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account)。 适用于你的应用程序的 Microsoft 标识平台功能因应用程序是在单帐户模式下运行还是在多帐户模式下运行而有所不同。

**共享设备模式应用仅在单帐户模式下工作**。

> [!IMPORTANT]
> 仅支持多帐户模式的应用程序无法在共享设备上运行。 如果员工加载不支持单帐户模式的应用，则它不会在共享设备上运行。
>
> 在 MSAL SDK 发布之前编写的应用程序在多帐户模式下运行，并且必须更新以支持单帐户模式，然后才能在共享模式设备上运行。

**同时支持单一帐户和多个帐户**

可以构建你的应用以支持在个人设备和共享设备上运行。 如果你的应用当前支持多个帐户，并且你想要支持共享设备模式，请添加对单帐户模式的支持。

您还可能希望您的应用程序更改其行为，具体取决于运行它的设备类型。 使用 `ISingleAccountPublicClientApplication.isSharedDevice()` 确定在单帐户模式下运行的时间。

可以通过两种不同的接口来表示应用程序所处的设备类型。 从 MSAL 的应用程序工厂请求应用程序实例时，会自动提供正确的应用程序对象。

以下对象模型说明了你可以接收的对象类型以及在共享设备上下文中的含义：

![公共客户端应用程序继承模型](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

获取 `PublicClientApplication` 对象时，需要执行类型检查并强制转换为相应的接口。 以下代码将检查多个帐户模式或单帐户模式，并适当地强制转换应用程序对象：

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

以下差异适用于你的应用程序是在共享设备还是个人设备上运行：

|  | 共享模式设备  | 个人设备 |
|---------|---------|---------|
| **帐户**     | 单个帐户 | 多个帐户 |
| **登录** | 全球 | 全球 |
| **注销** | 全球 | 每个应用程序都可以控制注销是应用程序的本地还是应用程序系列。 |
| **支持的帐户类型** | 仅工作帐户 | 支持个人和工作帐户  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>为什么你可能只想要支持单帐户模式

如果你正在编写仅用于使用共享设备的 firstline 工作人员的应用，我们建议你将应用程序编写为仅支持单帐户模式。 这包括以任务为中心的大多数应用程序，例如医疗记录应用、发票应用和大多数业务线应用。 仅支持单帐户模式可简化开发，因为无需实现属于多帐户应用的一部分的附加功能。

## <a name="what-happens-when-the-device-mode-changes"></a>设备模式更改时会发生的情况

如果你的应用程序在多帐户模式下运行，并且管理员将设备置于共享设备模式，则将从应用程序中清除设备上的所有帐户，并将应用程序转换为单帐户模式。

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>共享设备注销和总体应用生命周期

当用户注销时，你将需要采取措施来保护用户的隐私和数据。 例如，如果您要构建一个医学记录应用程序，则需要确保用户注销之前显示的患者记录。 必须为应用程序做好准备，并在每次输入前景时检查。

当你的应用程序使用 MSAL 在处于共享模式的设备上运行的应用程序中注销用户时，已登录的帐户和缓存的令牌将从应用和设备中删除。

下图显示了应用运行时可能出现的整体应用生命周期和常见事件。 该图包括活动启动的时间、登录和注销帐户，以及事件（例如暂停、继续和停止活动）的大小。

![共享设备应用生命周期](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>后续步骤

尝试[使用 android 应用程序中的 "使用共享设备模式](tutorial-v2-shared-device-mode.md)" 教程，该教程演示如何在共享模式 Android 设备上运行 firstline 工作线程应用。
