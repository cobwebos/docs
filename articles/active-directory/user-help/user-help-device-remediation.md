---
title: "\"你不能那里从此处获取\"错误-Azure Active Directory 进行故障排除 |Microsoft Docs"
description: 排查收到“无法从此处访问”错误消息的潜在原因。
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lizross
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a317680a39d4594aacdf84ccdf963bb84bfbf07b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60473768"
---
# <a name="potential-reasons-for-the-you-cant-get-there-from-here-error-message"></a>“无法从此处访问”错误消息的潜在原因
访问组织的内部 Web 应用或服务时，可能会收到以下错误消息：无法从此处访问。 此消息表示组织已使阻止设备访问组织的资源的策略就位。 虽然你可能不必联系支持人员来解决此问题，但可以先尝试执行以下操作。

## <a name="make-sure-youre-using-a-supported-browser"></a>确保使用的是支持的浏览器
如果收到“无法从此处访问”消息，指示正在尝试从不受支持的浏览器访问组织的站点，请检查你所运行的浏览器。

![与浏览器支持相关的错误消息](media/user-help-device-remediation/browser-version.png)

若要解决此问题，必须根据你的操作系统安装并运行支持的浏览器。 如果使用的是 Windows 10，则支持的浏览器包括 Microsoft Edge、Internet Explorer 和 Google Chrome。 如果使用的是其他操作系统，则可以检查[支持的浏览器](../conditional-access/technical-reference.md#supported-browsers)的完整列表。

## <a name="make-sure-youre-using-a-supported-operating-system"></a>确保使用的是支持的操作系统
确保正在运行支持的操作系统版本，包括：

- Windows 客户端。 Windows 7 或更高版本。

- Windows Server。 Windows Server 2008 R2 或更高版本。

- macOS。 macOS X 或更高版本

- Android 和 iOS。 最新版本的 Android 和 iOS 移动操作系统

若要解决此问题，必须安装并运行支持的操作系统。

## <a name="make-sure-your-device-is-joined-to-your-network"></a>确保设备已加入网络
如果收到“无法从此处访问”消息，指示设备不符合组织的访问策略，请确保已将设备加入组织的网络。

![与是否已加入网络相关的错误消息](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>检查设备是否已加入网络
1. 请使用工作或学校帐户登录到 Windows。 例如，alain@contoso.com。

2. 通过虚拟专用网络 (VPN) 或 DirectAccess 连接到组织的网络。

3. 建立连接后，按 Windows 徽标键 + L 键来锁定设备。

4. 使用工作或学校帐户解锁设备，然后尝试再次访问有问题的应用或服务。

    如果再次看到“无法从此处访问”错误消息，请选择“更多详细信息”链接，然后联系支持人员以获取详细信息。

### <a name="to-join-your-device-to-your-network"></a>将设备加入网络
如果设备未加入组织的网络，则可以执行两个操作之一：

- 加入工作设备。 将工作专用的 Windows 10 设备加入组织的网络，以便能够访问可能受限的资源。 有关详细信息和分步说明，请参阅[将工作设备加入组织的网络](user-help-join-device-on-network.md)。

- **注册用于工作的个人设备。** 在组织的网络上注册个人设备（通常为电话或平板电脑）。 设备注册后，可以访问组织的受限资源。 有关详细信息和分步说明，请参阅[在组织的网络上注册个人设备](user-help-register-device-on-network.md)。

## <a name="next-steps"></a>后续步骤
- [什么是 MyApps 门户？](active-directory-saas-access-panel-introduction.md)

- [使用手机而非密码登录](user-help-auth-app-sign-in.md)
