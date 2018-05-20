---
title: Azure AD 应用代理 - 开始安装连接器| Microsoft Docs
description: 在 Azure 门户中，打开应用程序代理并为反向代理安装连接器。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: d636542c788aff43acf9e35583b4c0615fe0c7db
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>开始使用应用程序代理并安装连接器
本文指导完成在 Azure AD 中为云目录启用 Microsoft Azure AD 应用程序代理的步骤。

如果你尚未意识到应用程序代理给你的组织带来的安全性和工作效率优势，请详细了解[如何提供对本地应用程序的安全远程访问](manage-apps/application-proxy.md)。

## <a name="application-proxy-prerequisites"></a>应用程序代理先决条件
在启用和使用应用程序代理服务之前，需要：

* [Microsoft Azure AD 基本或高级版订阅](active-directory-editions.md) 以及本人为全局管理员的 Azure AD 目录。
* 可以安装应用程序代理连接器、运行 Windows Server 2012 R2 或 2016 的服务器。 该服务器需要能够连接到云中的应用程序代理服务和你要发布的本地应用程序。
  * 若要使用 Kerberos 约束委派单一登录到已发布的应用程序，此计算机应域加入要发布的应用程序所在的同一 AD 域。 有关信息，请参阅[用于使用应用程序代理进行单一登录的 KCD](active-directory-application-proxy-sso-using-kcd.md)。

如果你的组织使用代理服务器连接到 Internet，在开始使用应用程序代理之前，请阅读[使用现有本地代理服务器](application-proxy-working-with-proxy-servers.md)，了解配置方法的详细信息。

## <a name="open-your-ports"></a>打开端口

若要为 Azure AD 应用程序代理准备你的环境，首先需要实现与 Azure 数据中心的通信。 如果路径中有防火墙，请确保防火墙已打开，以便连接器向应用程序代理发出 HTTPS (TCP) 请求。

1. 打开以下用于出站流量的端口：

   | 端口号 | 用途 |
   | --- | --- |
   | 80 | 下载证书吊销列表 (Crl) 的同时验证 SSL 证书 |
   | 443 | 与应用程序代理服务进行所有出站通信 |

   如果防火墙根据发起用户强制实施流量，请针对来自作为网络服务运行的 Windows 服务的流量打开这些端口。

   > [!IMPORTANT]
   > 表中会显示针对连接器版本 1.5.132.0 及更高版本的端口要求。 如果你仍然使用较低的连接器版本，则除了 80 和 443 之外，还需要启用下列端口：5671、8080、9090-9091、9350、9352、10100–10120。
   >
   >有关将连接器升级到最新版本的信息，请参阅[了解 Azure AD 应用程序代理连接器](application-proxy-understand-connectors.md#automatic-updates)。

2. 如果你的防火墙或代理允许执行 DNS 允许列表，可以将与 msappproxy.net 和 servicebus.windows.net 的连接加入允许列表。 如果不允许，则需要允许访问每周进行更新的 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。

3. Microsoft 使用四个地址来验证证书。 允许对以下 URL 的访问（如果还未对其他产品执行过此操作）：
   * mscrl.microsoft.com:80
   * crl.microsoft.com:80
   * ocsp.msocsp.com:80
   * www.microsoft.com:80

4. 连接器在注册过程中需要访问 login.windows.net 和 login.microsoftonline.com。

5. 使用 [Azure AD 应用程序代理连接器端口测试工具](https://aadap-portcheck.connectorporttest.msappproxy.net/)验证连接器是否能够访问应用程序代理服务。 请至少确保美国中部区域和离你最近的区域有全部绿色复选标记。 绿色复选标记越多表示复原能力越强。

## <a name="install-and-register-a-connector"></a>安装并注册连接器
1. 在 [Azure 门户](https://portal.azure.com/)中，以管理员身份登录。
2. 当前目录会显示在右上角的用户名下。 如果需要更改目录，则选择该图标。
3. 转到“Azure Active Directory” > “应用程序代理”。

   ![导航到应用程序代理](./media/active-directory-application-proxy-enable/app_proxy_navigate.png)

4. 选择“下载连接器”。

   ![下载连接器](./media/active-directory-application-proxy-enable/download_connector.png)

5. 在根据先决条件准备的服务器上运行 **AADApplicationProxyConnectorInstaller.exe** 。
6. 按照向导中的说明进行安装。 在安装期间，系统会提示将连接器注册到 Azure AD 租户的应用程序代理。

   * 提供 Azure AD 全局管理员凭据。 全局管理员租户可能不同于 Microsoft Azure 凭据。
   * 确保注册连接器的管理员在启用应用程序代理服务的同一目录中。 例如，如果租户域为 contoso.com，则管理员应该为 admin@contoso.com 或该域上的任何其他别名。
   * 如果在要安装连接器的服务器上将“IE 增强的安全配置”设为“打开”，则可能不会看到注册屏幕。 若要访问，请按照错误消息中的说明进行操作。 确保 Internet Explorer 增强的安全性已关闭。

为了获得高可用性，应该部署至少两个连接器。 每个连接器必须分开注册。

## <a name="test-that-the-connector-installed-correctly"></a>测试连接器是否已正确安装

可以通过在 Azure 门户中或服务器上检查新连接器来确认它是否已正确安装。 

在 Azure 门户中，登录到你的租户并导航到“Azure Active Directory” > “应用程序代理”。 你的所有连接器和连接器组都显示在此页面上。 选择一个连接器来查看其详细信息或将其移动到另一个连接器组中。 

在服务器上，检查连接器和连接器更新程序的活动服务的列表。 有两个服务应当会立即开始运行，但如果没有，请开启它们： 

   * **Microsoft AAD 应用程序代理连接器** 将启用连接

   * **Microsoft AAD 应用程序代理连接器更新程序**是一个自动的更新服务。 该更新程序会检查连接器的新版本并根据需要更新连接器。

   ![应用代理连接器服务 - 屏幕截图](./media/active-directory-application-proxy-enable/app_proxy_services.png)

有关连接器以及它们如何保持最新状态的信息，请参阅[了解 Azure AD 应用程序代理连接器](application-proxy-understand-connectors.md)。


## <a name="next-steps"></a>后续步骤
现在，可以随时 [使用应用程序代理发布应用程序](application-proxy-publish-azure-portal.md)。

如果应用程序位于单独网络或其他位置，可以使用连接器组将不同连接器组织到逻辑单元中。 了解有关 [使用应用程序代理连接器](active-directory-application-proxy-connectors-azure-portal.md)的详细信息。
