---
title: 德国 Microsoft 云中的 Azure AD Connect
description: Azure AD Connect 会将本地目录与 Azure Active Directory 集成。 这样，便可以为集成到 Azure AD 的 Office 365、Azure 和 SaaS 应用程序提供一个通用标识。
keywords: Azure AD Connect 介绍, Azure AD Connect 概述, 什么是 Azure AD Connect, 安装 Active Directory, 德国, 黑森林
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62f0d82a543c0ae4e629eda3bca18b0a06322f2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381225"
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>德国 Microsoft 云中的 Azure AD Connect - 公共预览版
## <a name="introduction"></a>简介
Azure AD Connect 提供本地 Active Directory 和 Azure Active Directory 之间的同步。
当前， [德国 Microsoft 云](https://azure.microsoft.com/global-infrastructure/germany/
) 中的许多方案必须由运营商完成。 使用“德国 Microsoft 云”时，必须注意以下几点：

* 必须在代理服务器上打开以下 URL，才可以成功执行同步：
  
  * *.microsoftonline.de
  * *.windows.net
  * * 证书吊销列表
* 登录 Azure AD 目录时，必须使用 onmicrosoft.de 域中的帐户。

 
## <a name="download"></a>下载
可从门户内的 Azure AD Connect 边栏选项卡下载 Azure AD Connect。  使用下面的说明找到 Azure AD Connect 边栏选项卡。

### <a name="the-azure-ad-connect-blade"></a>Azure AD Connect 边栏选项卡
登录到 Azure 门户以后，请执行以下操作：

1. 转到“浏览”
2. 选择“Azure Active Directory”
3. 然后选择“Azure AD Connect”

会看到以下详细信息：

![Azure AD Connect 边栏选项卡](./media/reference-connect-germany/germany1.png)

下表描述了边栏选项卡中所示的功能。

| 标题 | 描述 |
| --- | --- |
| 同步状态 |指示同步处于启用状态，还是处于禁用状态。 |
| 上次同步 |上次成功执行同步的时间。 |
| 联合域 |显示当前配置的联合域数。 |

## <a name="installation"></a>安装
若要安装 Azure AD Connect，可使用 [此处](how-to-connect-install-roadmap.md)的文档。

## <a name="advanced-features-and-additional-information"></a>高级功能和其他信息
有关自定义设置或高级配置的其他信息，请访问[将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)。 此页提供了有关其他指南的信息以及链接。

