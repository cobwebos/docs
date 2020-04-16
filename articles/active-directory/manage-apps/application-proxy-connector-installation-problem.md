---
title: 安装应用程序代理程序连接器时出现问题 | Microsoft Docs
description: 如何排除在安装应用程序代理程序连接器时可能遇到的问题
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d773e6302edf0b799e6dfccc702750a9cc74f60
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406689"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>安装应用程序代理程序连接器时出现问题

Microsoft AAD 应用程序代理连接器是一个内部域组件，该组件使用出站连接来建立从云可用终结点到内部域的连接性。

## <a name="general-problem-areas-with-connector-installation"></a>连接器安装的常规问题区域

连接器安装失败时，根本原因通常是以下方面之一：

1.  **连接性** – 若要成功完成安装，新连接器需要注册并建立未来的信任属性。 此操作可以通过连接到 AAD 应用程序代理云服务来完成。

2.  **信任建立** – 新连接器创建自签名证书并注册到云服务。

3.  **管理员的身份验证** – 在安装过程中，用户必须提供管理员凭据才能完成连接器安装。

> [!NOTE]
> 连接器安装日志可以在 %TEMP% 文件夹中找到，并可帮助提供有关导致安装失败的原因的其他信息。

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>验证与云应用程序代理服务和 Microsoft 登录页的连接性

**目的︰** 验证连接器计算机是否可以连接到 AAD 应用程序代理注册终结点以及 Microsoft 登录页。

1.  在连接器服务器上，使用[telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet)或其他端口测试工具运行端口测试，以验证端口 443 和 80 是否打开。

2.  如果其中任何端口未成功，请验证防火墙或后端代理是否有权访问所需的域和端口，请参阅[，准备本地环境](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)。

3.  打开浏览器（单独选项卡）并转到以下网页：`https://login.microsoftonline.com`，确保可登录到该页。

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>验证对应用程序代理信任证书的计算机和后端组件支持

**目标：** 验证连接器计算机、后端代理和防火墙是否可以支持连接器为将来信任创建的证书，以及该证书是否有效。

>[!NOTE]
>连接器尝试创建 TLS1.2 支持的 SHA512 证书。 如果计算机或后端防火墙和代理不支持 TLS1.2，则安装失败。
>
>

**查看所需的先决条件：**

1.  验证计算机是否支持 TLS1.2 – 2012 R2 后的所有 Windows 版本都应支持 TLS 1.2。 如果连接器计算机为 2012 R2 版本或更早的版本，请确保计算机上安装了以下知识库：<https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  联系网络管理员并要求验证后端代理和防火墙不会阻止 SHA512 传出流量。

**要验证客户端证书：**

验证当前客户端证书的指纹。 证书存储可以在 %程序数据%\微软_微软 AAD 应用程序代理连接器_Config_TrustSettings.xml 中找到

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

以下是可能的**IsInUserStore**值和含义：

- **false** - 客户端证书是在注册-AppProxyConnector 命令启动的安装或注册期间创建的。 它存储在本地计算机的证书存储中的个人容器中。 

按照步骤验证证书：

1. 运行**证书.msc**
2. 在管理控制台中展开个人容器，然后单击证书
3. 找到**由connectorregistrationca.msappproxy.net**颁发的证书

- **true** - 自动续订的证书存储在网络服务的用户证书存储中的个人容器中。 

按照步骤验证证书：

1. 下载[PsTools.zip](https://docs.microsoft.com/sysinternals/downloads/pstools)
2. 从包中提取[psExec，](https://docs.microsoft.com/sysinternals/downloads/psexec)并从提升的命令提示符中运行**psexec -i-u"nt 权限_网络服务"cmd.exe。**
3. 在新出现的命令提示中运行**certmgr.msc**
2. 在管理控制台中展开个人容器，然后单击证书
3. 找到 #connectorregistrationca.msappproxy.ne 颁发的证书

**要续订客户端证书：**

如果连接器有几个月未连接到服务，其证书可能会过时。 证书续订失败会导致证书过期。 这使得连接器服务停止工作。 事件 1000 记录在连接器的管理日志中：

连接器重新注册失败：连接器信任证书已过期。 在连接器运行的计算机上运行 PowerShell cmdlet 寄存器-AppProxyConnector，以重新注册连接器。

在这种情况下，卸载并重新安装连接器以触发注册，或者您可以运行以下 PowerShell 命令：

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

要了解有关注册-AppProxyConnector 命令的更多详细信息，请参阅[为 Azure AD 应用程序代理连接器创建无人参与安装脚本](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-register-connector-powershell)

## <a name="verify-admin-is-used-to-install-the-connector"></a>验证“admin”是否可用于安装连接器

**目的：** 验证尝试安装连接器的用户是否是具有正确凭据的管理员。 目前，用户必须至少是应用程序管理员才能成功安装。

**若要验证凭据是否正确：**

连接到 `https://login.microsoftonline.com` 并使用相同的凭据。 确保登录成功。 您可以通过访问**Azure 活动目录** -&gt;**用户和组** -&gt;**所有用户**来检查用户角色。 

选择您的用户帐户，然后在生成的菜单中选择"目录角色"。 验证所选角色是否为"应用程序管理员"。 如果按这些步骤操作无法访问任何页，则表示你不具有所需的角色。

## <a name="next-steps"></a>后续步骤
[了解 Azure AD 应用程序代理连接器](application-proxy-connectors.md)
