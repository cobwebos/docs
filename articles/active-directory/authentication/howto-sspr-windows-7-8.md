---
title: Azure AD 自助服务密码重置 Windows 7 和 8.1-Azure Active Directory
description: 如何使用 Windows 7 或 8.1 登录屏幕上的“忘记了密码”启用自助密码重置
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57d3e955059724756eb7102c1b9fbbf55ed203ab
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370442"
---
# <a name="how-to-enable-password-reset-from-windows-7-8-and-81"></a>如何：在 Windows 7、8 和 8.1 中启用密码重置

尽管管理员已启用自助密码重置 (SSPR)，但用户仍在不断地呼叫服务台重置其密码，因为他们无法通过浏览器窗口访问 [SSPR 门户](https://aka.ms/sspr)。 在 Windows 10 计算机上，可以根据教程[通过登录屏幕重置 Azure AD 密码](tutorial-sspr-windows.md)中所述启用登录屏幕上的“重置密码”链接。以下指导将会帮助你让 Windows 7、8 和 8.1 用户使用 Windows 登录屏幕上的 SSPR 重置其密码。

与 Windows 10 计算机不同，Windows 7、8 和 8.1 计算机的密码重置并不要求加入 Azure AD 域或 Active Directory 域。

![显示了“忘记了密码”链接的 Windows 7 登录屏幕 示例](media/howto-sspr-windows-7-8/windows-7-logon-screen.png)

## <a name="prerequisites"></a>必备组件

* 必须启用 Azure AD 自助密码重置。
* 修补的 Windows 7 或 Windows 8.1 操作系统。
* 根据[传输层安全性 (TLS) 注册表设置](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)中的指导启用的 TLS 1.2。

> [!WARNING]
> 必须启用 TLS 1.2，而不能仅仅设置为自动协商。

## <a name="install"></a>安装

1. 下载要启用的 Windows 版本的相应安装程序。

   1. Microsoft 下载中心 ([https://aka.ms/sspraddin](https://aka.ms/sspraddin)) 提供了软件

1. 登录到要在其中进行安装的计算机，然后运行安装程序。
1. 安装完成后，强烈建议重新启动。
1. 重新启动后，在登录屏幕中选择一个用户，然后单击“忘记了密码?” 启动密码重置工作流。
1. 遵循屏幕上的步骤完成重置密码的工作流。

![在 Windows 7 中单击“忘记了密码?”后的 SSPR 流](media/howto-sspr-windows-7-8/windows-7-sspr.png)

### <a name="silent-installation"></a>无提示安装

* 若要进行无提示安装，请使用命令“msiexec /i SsprWindowsLogon.PROD.msi /qn”
* 若要进行无提示卸载，请使用命令“msiexec /x SsprWindowsLogon.PROD.msi /qn”

## <a name="caveats"></a>注意事项

必须注册 SSPR 才能使用“忘记了密码”链接。

![需要提供额外的安全信息才能重置密码](media/howto-sspr-windows-7-8/windows-7-sspr-need-security-info.png)

此初始版本不支持使用 Microsoft Authenticator 应用发送用于重置密码的通知和代码。 用户必须注册符合策略要求的备用方法。

## <a name="troubleshooting"></a>故障排除

事件将同时记录在计算机和 Azure AD 中。

Azure AD 事件包括有关发生密码重置的 IP 地址和 ClientType 的信息。

![示例 Windows 7 中密码重置 Azure AD 审核日志](media/howto-sspr-windows-7-8/windows-7-sspr-azure-ad-audit-log.png)

如果需要记录更多的日志，可以更改计算机上的注册表项，以启用详细日志记录。 请仅出于故障排除的目的启用详细日志记录。

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

* 若要启用详细日志记录，请创建 REG_DWORD“EnableLogging”并将其设置为 1。
* 若要禁用详细日志记录，请将 REG_DWORD“EnableLogging”更改为 0。

如果 Windows 7、8 和 8.1 计算机位于代理服务器或防火墙后面，应允许向 passwordreset.microsoftonline.com 传输 HTTPS 流量 (443)。

## <a name="next-steps"></a>后续步骤

* [使 Windows 10 用户能够在登录屏幕上重置其密码](tutorial-sspr-windows.md)
