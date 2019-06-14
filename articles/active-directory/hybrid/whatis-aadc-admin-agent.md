---
title: 什么是 Azure AD Connect 管理代理 - Azure AD Connect | Microsoft Docs
description: 介绍了用来通过 Azure AD 同步和监视本地环境的工具。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ab3fff4294b4cda3d1554ef2761d3f4acaca35
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64687230"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>什么是 Azure AD Connect 管理员代理？ 
Azure AD Connect 管理代理是 Azure Active Directory Connect 的一个新组件，可安装在 Azure Active Directory Connect 服务器上。 该组件用于从 Active Directory 环境收集特定的数据，以帮助 Microsoft 支持工程师在收到支持案例时排查问题。 

>[!NOTE]
>默认情况下不会安装并启用管理代理。  必须安装该代理才能收集数据来为支持案例提供帮助。

安装后，Azure AD Connect 管理代理会等待 Azure Active Directory 发来的特定数据请求，从同步环境获取请求的数据，并将数据发送到 Azure Active Directory，后者将数据提供给 Microsoft 支持工程师。 

Azure AD Connect 管理代理从环境中检索到的信息不会以任何形式进行存储 - 这些信息只向 Microsoft 支持工程师显示，以帮助他们调查你所提出的 Azure Active Directory Connect 相关支持案例，以及进行故障排除。Azure AD Connect 管理代理默认不会安装在 Azure AD Connect 服务器上。 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>在 Azure AD Connect 服务器上安装 Azure AD Connect 管理代理 
Azure AD Connect 管理代理二进制文件将放到 AAD Connect 服务器中。 若要安装该代理，请执行以下操作： 



1. 在管理模式下打开 PowerShell 
2. 导航到其中的应用程序是位于的 cd"C:\Program Files\Microsoft Azure Active Directory Connect\SetupFiles"的目录 
3. 运行 AADConnectAdminAgentSetup.exe 应用程序 
 
出现提示时，请输入你的 Azure AD 全局管理员凭据。 

>[!NOTE]
>系统会多次提示你输入凭据，这是一个已知问题。 此问题将在下一版本中修复。

安装该代理后，服务器控制面板上的“添加/删除程序”列表中会显示以下两个新程序： 

![管理代理](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>向 Microsoft 服务工程师显示同步服务中的哪些数据？ 
当你提出支持案例时，Microsoft 支持工程师可以查看给定用户在 Active Directory 中的相关数据、Azure Active Directory Connect 服务器中的 Active Directory 连接器空间，以及 Azure Active Directory Connect 服务器中的 Metaverse。 

Microsoft 支持工程师无法更改你的系统中的任何数据，且无法查看任何密码。 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>如果我不希望 Microsoft 支持工程师访问我的数据，该怎样做？ 
安装该代理后，如果你不希望 Microsoft 服务工程师在处理支持请求时访问你的数据，可按如下所述通过修改服务配置文件来禁用该功能： 

1.  打开**C:\Program Files\Microsoft Azure AD Connect 管理 Agent\AzureADConnectAdministrationAgentService.exe.config**在记事本中。
2.  按如下所示禁用 **UserDataEnabled** 设置。 如果 **UserDataEnabled** 设置存在并已设置为 true，请将其设置为 false。 如果该设置不存在，请按如下所示添加该设置。    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  保存 config 文件。
4.  按如下所示重启 Azure AD Connect 管理代理服务

![管理代理](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。
