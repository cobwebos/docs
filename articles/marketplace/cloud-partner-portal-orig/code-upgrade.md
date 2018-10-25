---
title: 将代码升级到最新平台 | Microsoft Docs
description: 本主题介绍了如何将 Microsoft Dynamics 365 for Operations 平台版本升级到最新平台版本
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 787d9ef509bcafeb6a21d4b0ec952ea1eb4fd52b
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805739"
---
# <a name="upgrading-code-to-the-latest-platform"></a>将代码升级到最新平台

本文介绍了如何将 Microsoft Dynamics 365 for Operations 平台版本升级到最新平台版本。

## <a name="overview"></a>概述

Microsoft Dynamics 365 for Operations 平台由以下组件构成：

Dynamics 365 for Operations 平台二进制文件，如应用程序对象服务器 (AOS)、数据管理框架、报表和商业智能 (BI) 框架、开发工具和分析服务。 以下应用程序对象树 (AOT) 包：

1. 应用程序平台
2. 应用程序基础
3. 测试软件包

**重要提示**：若要迁移到最新版 Dynamics 365 for Operations 平台，Dynamics 365 for Operations 实现中不得包含所有属于平台的 AOT 包的任何自定义（覆盖）。 为了能够顺畅地持续更新平台，平台更新 3 中引入了此限制。 如果运行的平台版本低于平台更新 3，请参阅本文结尾的“从旧版升级到平台更新 3”部分。

若要详细了解代码升级，请参阅[此处](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update)。