---
title: 什么是 Azure AD Connect 管理代理 - Azure AD Connect | Microsoft Docs
description: 介绍了用来通过 Azure AD 同步和监视本地环境的工具。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e33143626e136523b4af086e841b92e9ad30fa86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60294988"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>什么是 Azure AD Connect 管理员代理？ 
Azure AD Connect 管理代理是 Azure Active Directory Connect 的一个新组件，安装在 Azure Active Directory Connect 服务器上。 该组件用于从 Active Directory 环境收集特定的数据，以帮助 Microsoft 支持工程师在收到支持案例时排查问题。

安装后，Azure AD Connect 管理代理会等待 Azure Active Directory 发来的特定数据请求，从同步环境获取请求的数据，并将数据发送到 Azure Active Directory，后者将数据提供给 Microsoft 支持工程师。

Azure AD Connect 管理代理从环境中检索到的信息不会以任何形式进行存储 - 这些信息只向 Microsoft 支持工程师显示，以帮助他们调查你所提出的 Azure Active Directory Connect 相关支持案例，以及进行故障排除

## <a name="how-is-the-azure-ad-connect-admin-agent-installed-on-the-azure-ad-connect-server"></a>Azure AD Connect 管理代理如何安装在 Azure AD Connect 服务器上？ 
安装管理代理后，服务器控制面板上的“添加/删除程序”列表中会显示以下两个新程序： 

![管理代理](media/whatis-aadc-admin-agent/adminagent1.png)

请不要删除或卸载这些程序，因为它们是 Azure AD Connect 安装的关键组成部分。

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>向 Microsoft 服务工程师显示同步服务中的哪些数据？
当你提出支持案例时，Microsoft 支持工程师可以查看给定用户在 Active Directory 中的相关数据、Azure Active Directory Connect 服务器中的 Active Directory 连接器空间，以及 Azure Active Directory Connect 服务器中的 Metaverse。

Microsoft 支持工程师无法更改你的系统中的任何数据，且无法查看任何密码。

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>如果我不希望 Microsoft 支持工程师访问我的数据，该怎样做？ 
 
如果你不希望 Microsoft 服务工程师在处理支持请求时访问你的数据，可按如下所述通过修改服务配置文件来禁用此设置： 

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