---
title: "Azure Active Directory 中 Cloud App Discovery 的改进 | Microsoft Docs"
description: "提供有关如何使用 Cloud App Discovery 来查找和管理应用程序、这样做的好处以及其工作原理的信息。"
services: active-directory
keywords: "cloud app discovery, 管理应用程序"
documentationcenter: 
author: curtand
manager: femila
tags: ignite
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: curtand
ms.reviewer: nigu
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: 59af2a5de5936d15456058aaeacfc334b9b34d4c
ms.contentlocale: zh-cn
ms.lasthandoff: 09/29/2017

---
# <a name="cloud-app-discovery-enhancements-in-azure-active-directory"></a>Azure Active Directory 中 Cloud App Discovery 的增强功能 
超过 80% 的员工承认出于工作目的而使用未经批准的 SaaS 应用。 我们现在已将 Azure AD 中的 Cloud App Discovery 与 Microsoft Cloud App Security 数据和分析功能集成在一起，以便可以更轻松地识别更多非托管的云应用。 这一新集成需要执行与以前不同的设置步骤。

## <a name="what-can-i-do-now-with-the-improvements-to-cloud-app-discovery-in-azure-ad"></a>现在通过 Azure AD 中 Cloud App Discovery 的改进可以做什么？

* **云应用使用情况** Azure AD 中的 Cloud App Discovery 现在从所有组织网络流量和任何设备发现**超过 15,000 个应用**。 
* **无需代理**这一新版 Cloud App Discovery 不要求在用户设备上安装代理。 而是根据从防火墙和代理导入的日志文件执行发现。 无论设备或操作系统如何，均可跨所有组织网络流量发现应用。
* **正在进行的分析和警报** Azure AD 中的 Cloud App Discovery 现在提供详细的进行中的风险分析，并在使用新的应用时提供警报。 现在，可以深入了解更多组织中云应用使用情况，例如有关入站流量、出站流量的信息以及已发现应用的前几名用户的信息。

如果没有 Active Directory Premium，请参阅 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)，了解详细信息。

使用此链接获得 [Azure AD 中的新 Cloud App Discovery 体验](https://portal.cloudappsecurity.com)。

## <a name="next-steps"></a>后续步骤
使用以下链接在 Azure AD 中设置 Cloud App Discovery。

* [开始使用 Cloud App Discovery](cloudappdiscovery-get-started.md)
* [创建快照报表](cloudappdiscovery-set-up-snapshots.md)
* [配置持续报告](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [使用自定义日志分析器](https://docs.microsoft.comcommit/cloud-app-security/custom-log-parser)
