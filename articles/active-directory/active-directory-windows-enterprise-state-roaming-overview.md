---
title: "企业状态漫游概述 | Microsoft Docs"
description: "提供有关 Windows 设备中的企业状态漫游设置的信息。 企业状态漫游可跨 Windows 设备为用户提供统一体验，并减少配置新设备所需的时间。"
services: active-directory
keywords: "什么是企业状态漫游, 企业同步, Windows 云"
documentationcenter: 
author: ningtan
manager: femila
editor: curtand
ms.assetid: 83b3b58f-94c1-4ab0-be05-20e01f5ae3f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: ce78b996a5f45b0b074563bc50af5aaab85f2667
ms.openlocfilehash: 56eacc48102302961132bba6d14838327a899c59
ms.lasthandoff: 01/05/2017


---
# <a name="enterprise-state-roaming-overview"></a>企业状态漫游概述
通过 Windows 10，[Azure Active Directory (Azure AD)](active-directory-whatis.md) 用户能够安全地将其用户设置和应用程序设置数据同步到云。 企业状态漫游可跨 Windows 设备为用户提供统一体验，并减少配置新设备所需的时间。 企业状态漫游的操作类似于首次在 Windows 8 中引入的标准[用户设置同步](http://windows.microsoft.com/en-US/windows-8/sync-settings-pcs)。 此外，企业状态漫游还提供以下优势：

* **分离企业数据和用户数据** – 组织可掌控其数据，且用户云帐户中不会混有企业数据，企业云帐户中不会混有用户数据。
* **增强安全性** - 数据离开用户的 Windows 10 设备前，会使用 Azure Rights Management (Azure RMS) 自动对其加密，云中的静态数据也会保持加密状态。 云中的所有静态内容保持加密状态，设置名称和 Windows 应用名称等命名空间除外。  
* **改善管理和监视** – 通过 Azure AD 门户集成可控制和查看组织中同步设置的人员以及进行同步的设备。 

企业状态漫游可用于多个 Azure 区域。 可在 Azure Active Directory 下的“[Azure 服务（按区域）](https://azure.microsoft.com/regions/#services)”页找到更新的可用区域列表。

| 文章 | 说明 |
| --- | --- |
| [在 Azure Active Directory 中启用企业状态漫游](active-directory-windows-enterprise-state-roaming-enable.md) |企业状态漫游可供任何拥有高级 Azure Active Directory (Azure AD) 订阅的组织使用。 有关如何获取 Azure AD 订阅的更多详细信息，请参阅“[Azure AD 产品](https://azure.microsoft.com/services/active-directory)”页。 |
| [设置和数据漫游的常见问题](active-directory-windows-enterprise-state-roaming-faqs.md) |本主题将解答 IT 管理员可能会遇到的一些设置和应用数据同步问题。 |
| [设置同步的组策略和 MDM 设置](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) |Windows 10 提供了组策略和移动设备管理 (MDM) 策略设置以限制设置同步。 |
| [Windows 10 漫游设置参考](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) |以下为将在 Windows 10 中漫游和/或备份的所有设置的完整列表。 |
| [故障排除](active-directory-windows-enterprise-state-roaming-troubleshooting.md) |本主题介绍故障排除的一些基本步骤，并包含已知问题列表。 |


