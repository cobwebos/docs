---
title: 适用于 Android 设备的共享设备模式 | Azure
description: 了解可让一线工作人员共享 Android 设备的共享设备模式
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: marsma
ms.reviwer: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 9928b64d286cc5072f28f7cc17e4af3e95662cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085620"
---
# <a name="shared-device-mode-for-android-devices"></a>适用于 Android 设备的共享设备模式

> [!NOTE]
> 此功能目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

一线工作人员（例如零售职员、机组人员和现场服务工人）通常使用共享移动设备来完成其工作。 当他们开始共享密码或 PIN 号来访问共享设备上的客户和业务数据时，这会造成问题。

使用共享设备模式可对 Android 设备进行配置，使其可由多名员工轻松共享。 员工可以登录并快速访问客户信息。 当他们完成其轮班或任务后，可以从设备注销，然后，该设备立即可供下一名员工使用。

共享设备模式还提供 Microsoft 标识支持的设备管理。

开发人员和云设备管理员可以共同创建共享设备模式应用：

- 开发人员编写单帐户应用（共享设备模式不支持多帐户应用），将 `"shared_device_mode_supported": true` 添加到应用的配置，并编写代码来处理共享设备注销等任务。
- 设备管理员通过安装 Authenticator 应用，并使用 Authenticator 应用将设备设置为共享模式，来准备要共享的设备。 只有充当[云设备管理员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#cloud-device-administrator)角色的用户才能使用 [Authenticator 应用](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview)将设备置于共享模式。 您可以通过 **：Azure 活动目录** > **角色和管理员** > **云设备管理员**在 Azure 门户中配置组织角色的成员身份。

 本文重点介绍开发人员应考虑的事项。

## <a name="single-vs-multiple-account-applications"></a>单帐户与多帐户应用程序

使用 Microsoft 身份验证库 SDK (MSAL) 编写的应用程序可以管理一个或多个帐户。 有关详细信息，请参阅[单帐户模式或多帐户模式](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account)。 适用于应用的 Microsoft 标识平台功能根据应用程序是在单帐户模式还是多帐户模式下运行而异。

**共享设备模式应用只能在单帐户模式下工作**。

> [!IMPORTANT]
> 仅支持多帐户模式的应用程序无法在共享设备上运行。 如果员工加载不支持单帐户模式的应用，该应用不会在共享设备上运行。
>
> 在 MSAL SDK 发布之前编写的应用在多帐户模式下运行，必须更新为支持单帐户模式，才能在共享模式的设备上运行。

**同时支持单帐户和多帐户**

可将应用构建为支持在个人设备和共享设备上运行。 如果应用目前支持多个帐户，而你想要支持共享设备模式，请添加对单帐户模式的支持。

你可能还希望应用根据其运行所在的设备类型更改其行为。 使用 `ISingleAccountPublicClientApplication.isSharedDevice()` 确定何时在单帐户模式下运行。

有两个不同的接口表示应用程序所在的设备类型。 从 MSAL 的应用程序工厂请求应用程序实例时，会自动提供正确的应用程序对象。

以下对象模型演示了可以接收的对象类型，以及该对象在共享设备上下文中的含义：

![公共客户端应用程序继承模型](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

获取 `PublicClientApplication` 对象时，需要执行类型检查并强制转换为相应的接口。 以下代码检查多帐户模式或单帐户模式，并相应地强制转换应用程序对象：

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

根据应用是在共享设备还是个人设备上运行而存在以下差异：

|  | 共享模式设备  | 个人设备 |
|---------|---------|---------|
| **帐户**     | 单帐户 | 多帐户 |
| **登录** | Global | Global |
| **注销** | Global | 每个应用程序可以控制注销是在应用的本地进行，还是针对应用程序的系列。 |
| **支持的帐户类型** | 仅限工作帐户 | 支持个人和工作帐户  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>为何只希望支持单帐户模式

如果你正在编写的应用仅用于使用共享设备的一线工作人员，则我们建议将应用程序编写为仅支持单帐户模式。 这包括以任务为中心的大多数应用程序，例如医疗记录应用、发票应用和大多数业务线应用。 仅支持单帐户模式可以简化开发，因为无需实现多帐户应用包含的附加功能。

## <a name="what-happens-when-the-device-mode-changes"></a>设备模式更改时会发生什么情况

如果应用程序在多帐户模式下运行，而管理员将设备置于共享设备模式，则会从应用程序中清除设备上的所有帐户，并将应用程序转换为单帐户模式。

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>共享设备注销和整体应用生命周期

当用户注销时，你需要采取措施来保护用户的隐私和数据。 例如，如果你正在构建一个医疗记录应用，则需要确保在用户注销时，清除以前显示的患者记录。 必须在应用程序中准备好此保护机制，并在应用程序每次进入前台时进行检查。

当应用使用 MSAL 来注销处于共享模式的设备上运行的应用中的用户时，将从该应用和设备中删除已登录的帐户和缓存的令牌。

下图显示了整体应用生命周期，以及应用运行时可能发生的常见事件。 该图从活动启动时开始，涵盖帐户的登录和注销，以及事件（例如暂停、恢复和停止活动）的适应方式。

![共享设备应用生命周期](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>后续步骤

尝试学习[在 Android 应用程序中使用共享设备模式](tutorial-v2-shared-device-mode.md)教程，其中介绍了如何在共享模式的 Android 设备上运行一线工作人员应用。
