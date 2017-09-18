---
title: "将 PhoneFactor 升级到 Azure MFA 服务器 | Microsoft 文档"
description: "从旧版 PhoneFactor 代理升级以后，开始使用 Azure MFA 服务器。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: yossib
ms.assetid: 42838ff7-bdf2-4d06-bacc-b3839a00cd76
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/06/2017
ms.author: joflore
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: df7815ff16ac66531005f3a29550bdb419f3d052
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>将 PhoneFactor 代理升级到 Azure 多重身份验证服务器
若要将 PhoneFactor Agent v5.x 或更低版本升级到 Azure 多重身份验证服务器，请先卸载 PhoneFactor 代理及关联的组件。 然后即可安装多重身份验证服务器及其关联组件。

## <a name="uninstall-the-phonefactor-agent"></a>卸载 PhoneFactor 代理

1. 首先，备份 PhoneFactor 数据文件。 默认安装位置为 C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata。

2. 如果已安装用户门户：
  1. 导航到安装文件夹并备份 web.config 文件。 默认安装位置为 C:\inetpub\wwwroot\PhoneFactor。

  2. 如果已向门户添加了自定义主题，请将自定义文件夹备份到 C:\inetpub\wwwroot\PhoneFactor\App_Themes 目录下。

  3. 通过 PhoneFactor 代理（仅当与 PhoneFactor 代理安装在同一服务器上时适用）或“Windows 程序和功能”卸载用户门户。

3. 如果安装了移动应用 Web 服务：

  1. 转到安装文件夹并备份 web.config 文件。 默认安装位置为 C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService。

  2. 通过“Windows 程序和功能”卸载移动应用 Web 服务。

4. 如果已安装 Web 服务 SDK，请通过 PhoneFactor 代理或“Windows 程序和功能”卸载它。

5. 通过“Windows 程序和功能”卸载 PhoneFactor 代理。

## <a name="install-the-multi-factor-authentication-server"></a>安装多重身份验证服务器

安装路径是从以前的 PhoneFactor 代理安装的注册表中选取的，因此它应安装在同一位置（例如 C:\Program Files\PhoneFactor）。 新安装将具有不同的默认安装路径（例如 C:\Program Files\Multi-Factor Authentication Server）。 安装期间会对以前的 PhoneFactor 代理留下的数据文件进行升级，因此，在安装新的 Multi-Factor Authentication 服务器后，用户和设置应仍在那里。

1. 如果出现提示，请激活 Multi-Factor Authentication 服务器，并确保已将它分配到正确的复制组。

2. 如果以前安装了 Web 服务 SDK，请通过 Multi-Factor Authentication 服务器用户界面安装新的 Web 服务 SDK。

  默认虚拟目录名称现在是“MultiFactorAuthWebServiceSdk”，而不是“PhoneFactorWebServiceSdk”。 如果要使用以前的名称，则必须在安装过程中更改虚拟目录的名称。 否则，如果允许安装使用新的默认名称，则必须将任何应用程序中引用 Web 服务 SDK（如用户门户和移动应用 Web 服务）的 URL 改为指向正确的位置。

3. 如果用户门户以前已安装在 PhoneFactor 代理服务器上，请通过Multi-Factor Authentication 服务器用户界面安装新的Multi-Factor Authentication用户门户。

  默认虚拟目录名称现在是“MultiFactorAuth”，而不是“PhoneFactor”。 如果要使用以前的名称，则必须在安装过程中更改虚拟目录的名称。 否则，如果允许安装使用新的默认名称，则应单击 Multi-Factor Authentication 服务器中的“用户门户”图标，并更新“设置”选项卡上的用户门户 URL。

4. 如果用户门户和/或移动应用 Web 服务之前安装在了 PhoneFactor 代理中的不同服务器上：

  1. 转到安装位置（例如 C:\Program Files\PhoneFactor），并将一个或多个安装程序复制到另一台服务器。 用户门户和移动应用 Web 服务都有 32 位和 64 位安装程序。 它们名为 MultiFactorAuthenticationUserPortalSetupXX.msi 和 MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi。

  2. 若要在 Web 服务器上安装用户门户，请以管理员身份打开命令提示符，并运行 MultiFactorAuthenticationUserPortalSetupXX.msi。

    默认虚拟目录名称现在是“MultiFactorAuth”，而不是“PhoneFactor”。 如果要使用以前的名称，则必须在安装过程中更改虚拟目录的名称。 否则，如果允许安装使用新的默认名称，则应单击 Multi-Factor Authentication 服务器中的“用户门户”图标，并更新“设置”选项卡上的用户门户 URL。现有用户需知悉新的 URL。

  3. 转到用户门户安装位置（例如 C:\inetpub\wwwroot\MultiFactorAuth）并编辑 web.config 文件。 将升级前备份的原始 web.config 文件的 appSettings 和 applicationSettings 节中的值复制到新的 web.config 文件。 如果在安装 Web 服务 SDK 时保留了新的默认虚拟目录名称，请更改 applicationSettings 节中的 URL，让其指向正确的位置。 如果在以前的 web.config 文件中更改了任何其他默认设置，请将这些更改应用到新的 web.config 文件。

  4. 若要在 Web 服务器上安装移动应用 Web 服务，请以管理员身份打开命令提示符，并运行 MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi。

    默认虚拟目录名称现在是“MultiFactorAuthMobileAppWebService”，而不是“PhoneFactorPhoneAppWebService”。 如果要使用以前的名称，则必须在安装过程中更改虚拟目录的名称。 可能想要选择较短的名称，方便最终用户在移动设备上键入。 否则，如果允许安装使用新的默认名称，应单击多重身份验证服务器中的“移动应用”图标，并更新移动应用 Web 服务 URL。

  5. 转到移动应用 Web 服务安装位置（例如 C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService）并编辑 web.config 文件。 将升级前备份的原始 web.config 文件的 appSettings 和 applicationSettings 节中的值复制到新的 web.config 文件。 如果在安装 Web 服务 SDK 时保留了新的默认虚拟目录名称，请更改 applicationSettings 节中的 URL，让其指向正确的位置。 如果在以前的 web.config 文件中更改了任何其他默认设置，请将这些更改应用到新的 web.config 文件。

## <a name="next-steps"></a>后续步骤

- 为 Azure 多重身份验证服务器[安装用户门户](multi-factor-authentication-get-started-portal.md)。

- 为应用程序[配置 Windows 身份验证](multi-factor-authentication-get-started-server-windows.md)。 

