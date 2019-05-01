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
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687230"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>什么是 Azure AD Connect 管理员代理？ 
Azure AD Connect 管理代理是一个新的 Azure Active Directory Connect 可以在 Azure Active Directory Connect 的服务器上安装的组件。 该组件用于从 Active Directory 环境收集特定的数据，以帮助 Microsoft 支持工程师在收到支持案例时排查问题。 

>[!NOTE]
>未安装和默认情况下启用的管理代理。  必须安装代理，以便收集数据，以帮助支持案例。

安装时，特定请求的 Azure AD Connect 管理代理等待 for Azure Active Directory 中的数据从同步环境中获取所请求的数据并将其发送到 Azure Active Directory，其中提供给 Microsoft 的支持工程师。 

Azure AD Connect 管理代理从你的环境中检索的信息不存储任何方式-它只会向 Microsoft 支持工程师，以帮助他们中调查和解决 Azure Active Directory Connect打开 Azure AD Connect 管理代理的相关的支持用例未安装在 Azure AD Connect 服务器上默认情况下。 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>在 Azure AD Connect 服务器上安装 Azure AD Connect 管理代理 
Azure AD Connect 管理代理二进制文件放置在 AAD Connect 服务器中。 若要安装代理，请执行以下操作： 



1. 在管理员模式下打开 powershell 
2. 导航到其中的应用程序是位于的 cd"C:\Program Files\Microsoft Azure Active Directory Connect\SetupFiles"的目录 
3. 运行 AADConnectAdminAgentSetup.exe 应用程序 
 
出现提示时，请输入你的 Azure AD 全局管理员凭据。 

>[!NOTE]
>是一个已知的问题，系统会提示输入凭据多次。 此问题将在下一版本中修复。

安装代理后，将看到你的服务器上的控制面板中的"添加/删除程序"列表中的以下两个新程序： 

![管理代理](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>向 Microsoft 服务工程师显示同步服务中的哪些数据？ 
打开支持案例时 Microsoft 支持工程师可以看到，给定的用户、 Active Directory 中的相关数据、 Active Directory 连接器空间在 Azure Active Directory Connect 服务器中，在 Azure 中的 Azure Active Directory 连接器空间Active Directory Connect 的服务器和 Azure Active Directory Connect 服务器中的 Metaverse。 

Microsoft 支持工程师无法更改你的系统中的任何数据，且无法查看任何密码。 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>如果我不希望 Microsoft 支持工程师访问我的数据，该怎样做？ 
安装代理后，如果不希望 Microsoft 服务工程师要访问电话支持的数据，您可以通过修改服务配置文件，如下所述禁用功能： 

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
