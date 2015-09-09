<properties 
	pageTitle="将 PhoneFactor 代理升级到 Azure Multi-Factor Authentication 服务器" 
	description="本文档介绍如何开始使用 Azure MFA 服务器以及如何从较旧的 phonefactor 代理升级。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.date="06/02/2015" 
	wacn.date=""/>

# 将 PhoneFactor 代理升级到 Azure Multi-Factor Authentication 服务器

从 PhoneFactor 代理 v5.x 或更旧版本升级到 Azure Multi-Factor Authentication 服务器需要先卸载 PhoneFactor 代理及附属组件，然后才能安装 Multi-Factor Authentication 服务器及其附属组件。

## 将 PhoneFactor 代理升级到 Azure Multi-Factor Authentication 服务器
<ol>
<li>首先，备份 PhoneFactor 数据文件。默认安装位置为 C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata。


<li>如果已安装用户门户：</li>
<ol>
<li>导航到安装文件夹并备份 web.config 文件。默认安装位置为 C:\inetpub\wwwroot\PhoneFactor。</li>


<li>如果你已向门户添加了自定义主题，请将自定义文件夹备份到 C:\inetpub\wwwroot\PhoneFactor\App_Themes 目录下。</li>


<li>通过 PhoneFactor 代理（仅当与 PhoneFactor 代理安装在同一服务器上时适用）或“Windows 程序和功能”卸载用户门户。</li></ol>




<li>如果已安装移动应用 Web 服务：<ol><li>转到安装文件夹并备份 web.config 文件。默认安装位置为 C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService。</li> <li>通过“Windows 程序和功能”卸载移动应用 Web 服务。</li></ol>

<li>如果已安装 Web 服务 SDK，请通过 PhoneFactor 代理或“Windows 程序和功能”卸载它。

<li>通过“Windows 程序和功能”卸载 PhoneFactor 代理。

<li>安装 Multi-Factor Authentication 服务器。请注意，安装路径是从以前的 PhoneFactor 代理安装的注册表中选取的，因此它应安装在同一位置（例如 C:\Program Files\PhoneFactor）。新安装将具有不同的默认安装路径（例如 C:\Program Files\Multi-Factor Authentication Server）。安装期间会对以前的 PhoneFactor 代理留下的数据文件进行升级，因此，在安装新的 Multi-Factor Authentication 服务器后，你的用户和设置应仍在那里。

<li>如果出现提示，请激活 Multi-Factor Authentication 服务器，并确保已将它分配到正确的复制组。

<li>如果以前安装了 Web 服务 SDK，请通过 Multi-Factor Authentication 服务器用户界面安装新的 Web 服务 SDK。请注意，默认虚拟目录名称现在是“MultiFactorAuthWebServiceSdk”，而不是“PhoneFactorWebServiceSdk”。如果要使用以前的名称，则必须在安装过程中更改虚拟目录的名称。否则，如果你允许安装使用新的默认名称，则必须将任何应用程序中引用 Web 服务 SDK（如用户门户和移动应用 Web 服务）的 URL 改为指向正确的位置。

<li>如果用户门户以前已安装在 PhoneFactor 代理服务器上，请通过Multi-Factor Authentication 服务器用户界面安装新的Multi-Factor Authentication用户门户。请注意，默认虚拟目录名称现在是“MultiFactorAuth”，而不是“PhoneFactor”。如果要使用以前的名称，则必须在安装过程中更改虚拟目录的名称。否则，如果你允许安装使用新的默认名称，则应单击 Multi-Factor Authentication 服务器中的“用户门户”图标，并更新“设置”选项卡上的用户门户 URL。

<li>如果用户门户和/或移动应用 Web 服务以前已通过 PhoneFactor 代理安装在不同服务器上：
<ol>
<li>转到安装位置（例如 C:\Program Files\PhoneFactor），将相应的安装程序复制到其他服务器。用户门户和移动应用 Web 服务都有 32 位和 64 位安装程序。它们分别名为 MultiFactorAuthenticationUserPortalSetupXX.msi 和 MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi。</li>
<li>若要在 Web 服务器上安装用户门户，请以管理员身份打开命令提示符，并运行 MultiFactorAuthenticationUserPortalSetupXX.msi。请注意，默认虚拟目录名称现在是“MultiFactorAuth”，而不是“PhoneFactor”。如果要使用以前的名称，则必须在安装过程中更改虚拟目录的名称。否则，如果你允许安装使用新的默认名称，则应单击 Multi-Factor Authentication 服务器中的“用户门户”图标，并更新“设置”选项卡上的用户门户 URL。需要将新的 URL 通知现有用户。</li>
<li>转到用户门户安装位置（例如 C:\inetpub\wwwroot\MultiFactorAuth）并编辑 web.config 文件。将升级前备份的原始 web.config 文件的 appSettings 和 applicationSettings 节中的值复制到新的 web.config 文件。如果在安装 Web 服务 SDK 时保留了新的默认虚拟目录名称，请更改 applicationSettings 节中的 URL，让其指向正确的位置。如果在以前的 web.config 文件中更改了任何其他默认设置，请将这些更改应用到新的 web.config 文件。</li>
<li>若要在 Web 服务器上安装移动应用 Web 服务，请以管理员身份打开命令提示符，并运行 MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi。请注意，默认虚拟目录名称现在是“MultiFactorAuthMobileAppWebService”而不是“PhoneFactorPhoneAppWebService”。如果要使用以前的名称，则必须在安装过程中更改虚拟目录的名称。你可能想要选择较短的名称，方便最终用户在移动设备上键入。否则，如果你允许安装使用新的默认名称，则应单击 Multi-Factor Authentication 服务器中的“移动应用”图标，并更新移动应用 Web 服务 URL。</li>
<li>转到移动应用 Web 服务安装位置（例如 C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService）并编辑 web.config 文件。将升级前备份的原始 web.config 文件的 appSettings 和 applicationSettings 节中的值复制到新的 web.config 文件。如果在安装 Web 服务 SDK 时保留了新的默认虚拟目录名称，请更改 applicationSettings 节中的 URL，让其指向正确的位置。如果在以前的 web.config 文件中更改了任何其他默认设置，请将这些更改应用到新的 web.config 文件。</li></ol>


 


 

<!---HONumber=69-->