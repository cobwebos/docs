---
title: 什么是 Azure AD Connect 管理员代理的 Azure AD Connect |Microsoft Docs
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
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2019
ms.locfileid: "59577603"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>什么是 Azure AD Connect 管理员代理？ 
Azure AD Connect 管理代理是一个新的 Azure Active Directory Connect 在 Azure Active Directory Connect 的服务器上安装的组件。 它用于从 Active Directory 环境，可帮助 Microsoft 支持工程师来解决问题，当您打开一个支持案例时收集特定数据。

安装时，特定请求的 Azure AD Connect 管理代理等待 for Azure Active Directory 中的数据在同步环境中，获取所请求的数据并将其发送到 Azure Active Directory，其中提供给 Microsoft 的支持工程师。

Azure AD Connect 管理代理从你的环境中检索的信息不存储任何方式-它只会向 Microsoft 支持工程师，以帮助他们中调查和解决 Azure Active Directory Connect打开相关的支持案例

## <a name="how-is-the-azure-ad-connect-admin-agent-installed-on-the-azure-ad-connect-server"></a>如何在 Azure AD Connect 服务器上安装 Azure AD Connect 管理员代理？ 
安装管理代理后，您将看到你的服务器上的控制面板中的"添加/删除程序"列表中的以下两个新程序： 

![管理代理](media/whatis-aadc-admin-agent/adminagent1.png)

不删除或取消安装程序，因为它们是将 Azure AD Connect 安装的关键部分。

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>向 Microsoft 服务工程师显示我的同步服务中的哪些数据？
当你打开支持案例，Microsoft 支持工程师可以看到，给定的用户、 Active Directory 中的相关数据、 Active Directory 连接器空间在 Azure Active Directory Connect 服务器中，在 Azure 中的 Azure Active Directory 连接器空间Active Directory Connect 的服务器和 Azure Active Directory Connect 服务器中的 Metaverse。

Microsoft 支持工程师不能更改系统中的任何数据和不可以看到任何密码。

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>我不希望 Microsoft 支持工程师来访问我的数据怎么办？ 
 
如果不希望访问你的数据支持调用的 Microsoft 服务工程师可以禁用此通过修改服务配置文件，如下所述： 

1.  打开**C:\Program Files\Microsoft Azure AD Connect 管理 Agent\AzureADConnectAdministrationAgentService.exe.config**在记事本中。
2.  禁用**UserDataEnabled**设置，如下所示。 如果**UserDataEnabled**设置存在，并且设置为 true，然后将其设置为 false。 如果设置不存在，则添加设置，如下所示。    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  保存 config 文件。
4.  重新启动 Azure AD Connect 管理代理服务，如下所示

![管理代理](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。