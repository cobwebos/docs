---
title: "德国 Microsoft 云中的 Azure AD Connect"
description: "Azure AD Connect 会将你的本地目录与 Azure Active Directory 集成。 这样，你便可以为集成到 Azure AD 的 Office 365、Azure 和 SaaS 应用程序提供一个通用标识。"
keywords: "Azure AD Connect 介绍, Azure AD Connect 概述, 什么是 Azure AD Connect, 安装 Active Directory, 德国, 黑森林"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/07/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 7db56a4c0efb208591bb15aa03a4c0dbf833d22e
ms.openlocfilehash: a6bb1c4b3a4972cdab9b99c548ef918a4d1070a0


---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>德国 Microsoft 云中的 Azure AD Connect - 公共预览版
## <a name="introduction"></a>介绍
Azure AD Connect 提供本地 Active Directory 和 Azure Active Directory 之间的同步。
当前， [德国 Microsoft 云](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) 中的许多方案必须由运营商完成。 使用“德国 Microsoft 云”时，必须注意以下几点：

* 必须在代理服务器上打开以下 URL，才可以成功执行同步：
  
  * *.microsoftonline.de
  * *.windows.net
  * * 证书吊销列表
* 登录你的 Azure AD 目录时，必须使用 onmicrosoft.de 域中的帐户。
* 以下功能不可用：
  * Azure AD Connect Health
  * 自动更新
  * 密码写回

## <a name="download"></a>下载
可从门户内的 Azure AD Connect 边栏选项卡下载 Azure AD Connect。  使用下面的说明找到 Azure AD Connect 边栏选项卡。

### <a name="the-azure-ad-connect-blade"></a>Azure AD Connect 边栏选项卡
登录 Azure 门户后，执行以下操作：

1. 转到“浏览”
2. 选择“Azure Active Directory”
3. 然后选择“Azure AD Connect”

你应该看到以下内容：

![Azure AD Connect 边栏选项卡](media/active-directory-aadconnect-germany/germany1.png)

下表描述了边栏选项卡中所示的功能。

| 标题 | 说明 |
| --- | --- |
| 同步状态 |指示同步处于启用状态，还是处于禁用状态。 |
| 上次同步 |上次成功执行同步的时间。 |
| 联合域 |显示当前配置的联合域数。 |

## <a name="installation"></a>安装
若要安装 Azure AD Connect，可使用 [此处](active-directory-aadconnect.md#install-azure-ad-connect)的文档。

## <a name="advanced-features-and-additional-information"></a>高级功能和其他信息
有关自定义设置或高级配置的其他信息和指南，请从 [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)入手。  此页提供了有关其他指南的信息以及链接。




<!--HONumber=Jan17_HO1-->


