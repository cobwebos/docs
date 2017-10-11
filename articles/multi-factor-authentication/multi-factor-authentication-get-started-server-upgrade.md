---
title: "升级到 Azure MFA 服务器的 PhoneFactor |Microsoft 文档"
description: "要开始使用 Azure MFA 服务器，当你从较旧的 phonefactor 代理升级。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 42838ff7-bdf2-4d06-bacc-b3839a00cd76
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/06/2017
ms.author: kgremban
ms.openlocfilehash: 7ab7e693909f807781744ae53eed75d425096590
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>将 PhoneFactor 代理升级到 Azure 多因素身份验证服务器
若要升级 PhoneFactor 代理 v5.x 或更低版本到 Azure 多因素身份验证服务器，卸载 PhoneFactor 代理和首先关联组件。 然后可以安装多因素身份验证服务器和及其附属的组件。

## <a name="uninstall-the-phonefactor-agent"></a>卸载 PhoneFactor 代理

1. 首先，备份 PhoneFactor 数据文件。 默认安装位置为 C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata。

2. 如果已安装用户门户：
  1. 导航到安装文件夹并备份 web.config 文件。 默认安装位置为 C:\inetpub\wwwroot\PhoneFactor。

  2. 如果你已向门户添加自定义主题，则备份 C:\inetpub\wwwroot\PhoneFactor\App_Themes 目录自定义文件夹。

  3. 卸载用户门户通过 PhoneFactor 代理 （仅当安装在 PhoneFactor 代理所在的同一服务器上可用） 或通过 Windows 程序和功能。

3. 如果已安装移动应用 Web 服务：

  1. 转到安装文件夹并备份 web.config 文件。 默认安装位置为 C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService。

  2. 卸载移动应用 Web 服务通过 Windows 程序和功能。

4. 如果已安装 Web 服务 SDK，卸载它通过 PhoneFactor 代理或通过 Windows 程序和功能。

5. 卸载 PhoneFactor 代理通过 Windows 程序和功能。

## <a name="install-the-multi-factor-authentication-server"></a>安装多因素身份验证服务器

安装路径是注册表中选取从以前的 PhoneFactor 代理安装，因此它应安装在同一位置 (例如，C:\Program Files\PhoneFactor)。 新安装具有不同的默认安装路径 (例如，C:\Program Files\multi-factor Authentication Server)。 因此你的用户和设置应仍在那里安装新的 Multi-factor Authentication 服务器后，应在安装期间，升级以前的 PhoneFactor 代理留下的数据文件。

1. 如果系统提示，请激活 Multi-factor Authentication 服务器，并确保它将分配到正确的复制组。

2. 如果以前安装了 Web 服务 SDK，安装新的 Web 服务 SDK 通过多因素身份验证服务器用户界面。

  默认虚拟目录名称现在是**MultiFactorAuthWebServiceSdk**而不是**PhoneFactorWebServiceSdk**。 如果你想要使用以前的名称，必须在安装过程中更改虚拟目录的名称。 否则，如果你允许安装使用新的默认名称，你必须更改任何应用程序中引用 Web 服务 SDK （如用户门户和移动应用 Web 服务） 为指向正确的位置的 URL。

3. 如果用户门户以前已安装在 PhoneFactor 代理服务器，安装新的多因素身份验证用户门户通过多因素身份验证服务器用户界面。

  默认虚拟目录名称现在是**MultiFactorAuth**而不是**PhoneFactor**。 如果你想要使用以前的名称，必须在安装过程中更改虚拟目录的名称。 否则为如果你允许安装使用新的默认名称，你应单击多因素身份验证服务器中的用户门户图标，并更新设置选项卡上的用户门户 URL。

4. 如果用户门户和/或移动应用 Web 服务以前已安装在 PhoneFactor 代理的不同服务器上：

  1. 转到安装位置 (例如，C:\Program Files\PhoneFactor)，并将一个或多个安装程序复制到另一台服务器。 有 32 位和 64 位安装程序的用户门户和移动应用 Web 服务。 它们分别名为 MultiFactorAuthenticationUserPortalSetupXX.msi 和 MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi。

  2. 若要安装用户门户 web 服务器上，打开管理员命令提示符并运行 MultiFactorAuthenticationUserPortalSetupXX.msi。

    默认虚拟目录名称现在是**MultiFactorAuth**而不是**PhoneFactor**。 如果你想要使用以前的名称，必须在安装过程中更改虚拟目录的名称。 否则为如果你允许安装使用新的默认名称，你应单击多因素身份验证服务器中的用户门户图标，并更新设置选项卡上的用户门户 URL。 需要新的 URL 通知现有用户。

  3. 转到用户门户安装位置 (例如，C:\inetpub\wwwroot\MultiFactorAuth) 并编辑 web.config 文件。 值复制之前已备份在升级到新的 web.config 文件的原始 web.config 文件的 appSettings 和 applicationSettings 节中。 如果安装 Web 服务 SDK 时保留新的默认虚拟目录名称，请，更改 applicationSettings 节中的 URL，使其指向正确的位置。 如果在以前的 web.config 文件中更改了任何其他默认设置，将这些更改应用到新的 web.config 文件中。

  4. 若要在 web 服务器上安装移动应用 Web 服务，打开管理员命令提示符并运行 MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi。

    默认虚拟目录名称现在是**MultiFactorAuthMobileAppWebService**而不是**PhoneFactorPhoneAppWebService**。 如果你想要使用以前的名称，必须在安装过程中更改虚拟目录的名称。 你可能想要选择较短的名称，以便可以方便最终用户在其移动设备上键入。 否则为如果你允许安装使用新的默认名称，你应单击 Multi-factor Authentication 服务器中的移动应用程序图标并更新移动应用 Web 服务 URL。

  5. 转到移动应用 Web 服务安装位置 (例如，C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) 并编辑 web.config 文件。 值复制之前已备份在升级到新的 web.config 文件的原始 web.config 文件的 appSettings 和 applicationSettings 节中。 如果安装 Web 服务 SDK 时保留新的默认虚拟目录名称，请，更改 applicationSettings 节中的 URL，使其指向正确的位置。 如果在以前的 web.config 文件中更改了任何其他默认设置，将这些更改应用到新的 web.config 文件中。

## <a name="next-steps"></a>后续步骤

- [安装用户门户](multi-factor-authentication-get-started-portal.md)Azure 多因素身份验证服务器。

- [配置 Windows 身份验证](multi-factor-authentication-get-started-server-windows.md)为应用程序。 
