---
title: "IIS 身份验证和 Azure MFA 服务器 |Microsoft 文档"
description: "这是将帮助你部署 IIS 身份验证和 Azure 多因素身份验证服务器的 Azure 多因素身份验证页。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d1bf1c8a-2c10-4ae6-9f4b-75f0c3df43eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/16/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: H1Hack27Feb2017,it-pro
ms.openlocfilehash: ab6f9110dccd3cfc15092f535650e8d8cb1af13c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>配置 IIS web 应用的 Azure 多因素身份验证服务器

使用 Azure 多因素身份验证 (MFA) 服务器的 IIS 身份验证部分来启用并配置与 Microsoft IIS web 应用程序的集成 IIS 身份验证。 Azure MFA 服务器将安装一个插件，可以筛选向 IIS web 服务器发出添加 Azure 多因素身份验证的请求。 IIS 插件支持基于窗体的身份验证和集成 Windows HTTP 身份验证。 受信任 Ip 也内部 IP 地址免除双因素身份验证配置。

![IIS 身份验证](./media/multi-factor-authentication-get-started-server-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>通过 Azure 多因素身份验证服务器使用基于窗体的 IIS 身份验证
若要保护使用基于窗体的身份验证的 IIS web 应用，在 IIS web 服务器上安装 Azure 多因素身份验证服务器，并按以下过程配置该服务器：

1. 在 Azure 多因素身份验证服务器中，单击左侧菜单中的 IIS 身份验证图标。
2. 单击**基于窗体的**选项卡。
3. 单击 **“添加”**。
4. 若要自动检测用户名、 密码和域变量，输入登录 URL （如 https://localhost/contoso/auth/login.aspx) 在自动配置基于表单的网站对话框中，单击**确定**。
5. 检查**需要 Multi-factor Authentication 用户匹配**框中，如果所有用户已被或将导入到该服务器并接受多因素身份验证。 如果大量用户具有尚未被导入到该服务器并且/或者将免除多因素身份验证，请使该框未选中状态。
6. 如果无法自动检测到页变量，请单击**手动指定**的自动配置基于表单的网站对话框中。
7. 在添加基于窗体网站对话框中，输入到提交 URL 字段中的登录页 URL 并输入应用程序名称 （可选）。 应用程序名称显示在 Azure 多因素身份验证报表，可能会显示在短信或移动应用程序身份验证消息。
8. 选择正确的请求格式。 此值设置为**POST 或 GET**对于大多数 web 应用程序。
9. 输入用户名变量、 密码变量和域变量 （如果它显示在登录页）。 查找输入框的名称，请导航到在 web 浏览器中的登录页、 在页上，右键单击并选择**查看源**。
10. 检查**需要 Azure Multi-factor Authentication 用户匹配**框中，如果所有用户已被或将导入到该服务器并接受多因素身份验证。 如果大量用户具有尚未被导入到该服务器并且/或者将免除多因素身份验证，请使该框未选中状态。
11. 单击**高级**若要查看高级的设置，包括：

  - 选择自定义拒绝页文件
  - 到网站的成功身份验证缓存一段时间使用 cookie
  - 选择是否针对 Windows 域中，LDAP 目录对主要凭据进行身份验证。 或 RADIUS 服务器。

12. 单击**确定**以返回到添加基于窗体网站对话框中。
13. 单击 **“确定”**。
14. 一旦已检测到或输入 URL 和页变量，网站数据将基于窗体的面板中显示。

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>通过 Azure 多因素身份验证服务器使用集成的 Windows 身份验证
若要保护使用集成 Windows HTTP 身份验证的 IIS web 应用，在 IIS web 服务器上，安装 Azure MFA 服务器，然后使用以下步骤配置服务器：

1. 在 Azure 多因素身份验证服务器中，单击左侧菜单中的 IIS 身份验证图标。
2. 单击**HTTP**选项卡。
3. 单击 **“添加”**。
4. 在添加基 URL 对话框中，输入 （如 http://localhost/owa) 执行 HTTP 身份验证网站的 URL 并提供应用程序名称 （可选）。 应用程序名称显示在 Azure 多因素身份验证报表，可能会显示在短信或移动应用程序身份验证消息。
5. 调整空闲超时和最大值会话时间默认值是否不足够。
6. 检查**需要 Multi-factor Authentication 用户匹配**框中，如果所有用户已被或将导入到该服务器并接受多因素身份验证。 如果大量用户具有尚未被导入到该服务器并且/或者将免除多因素身份验证，请使该框未选中状态。
7. 检查**Cookie 缓存**框中，如果需要。
8. 单击 **“确定”**。

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>为 Azure 多因素身份验证服务器启用 IIS 插件
配置基于窗体的后或 HTTP 身份验证 Url 和设置，在 IIS 中选择应加载并启用 Azure 多因素身份验证 IIS 插件的位置。 使用以下过程：

1. 如果在 IIS 6 上运行，请单击**ISAPI**选项卡。 选择 web 应用程序正在运行 （例如默认网站） 以启用 Azure 多因素身份验证 ISAPI 筛选器插件该站点的网站。
2. 如果运行 IIS 7 或更高版本，请单击**本机模块**选项卡。 选择服务器、 网站或应用程序可以启用 IIS 插件的所需的级别。
3. 单击**启用 IIS 身份验证**屏幕顶部的框。 现在，azure 多因素身份验证将保护所选的 IIS 应用程序。 确保用户已导入该服务器。

## <a name="trusted-ips"></a>受信任的 Ip
受信任 Ip 允许用户对从特定 IP 地址或子网发起的网站请求跳过 Azure 多因素身份验证。 例如，你可能想对用户免除 Azure 多因素身份验证从办公室登录时。 为此，你将为受信任 Ip 条目指定办公室子网。 若要配置受信任的 Ip，请使用以下过程：

1. 在 IIS 身份验证部分中，单击**受信任的 Ip**选项卡。
2. 单击 **“添加”**。
3. 添加受信任 Ip 对话框中显示时，选择**单个 IP**， **IP 范围**，或**子网**单选按钮。
4. 输入的 IP 地址、 IP 地址范围或子网应加入允许列表。 如果输入子网，选择相应的网络掩码，然后单击**确定**。 现已添加白名单。
