---
title: 如何填写“技术信息”窗体 | Microsoft Docs
description: 介绍了如何在“技术信息”窗体上为新的 Dynamics 365 Business Central 应用输入值。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cb8e5074d0cd99f5c7cb2130e6f9181eb5382015
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805591"
---
<a name="how-to-fill-out-the-technical-info-form"></a>如何填写“技术信息”窗体
===========================================

1.  在“选择应用程序类型”部分中，上传你的扩展包文件 (.app) 以及你的扩展所依赖的任何扩展包文件。

    ![应用程序包信息](./media/d365-financials/image015.png)

-   **扩展包文件** -- 必需 - 扩展包文件 (.app)。

-   **依赖项包文件** -- 如果应用依赖于 AppSource 中发布的其他应用，则为必需的。 此 .app 文件是当前应用所依赖的、AppSource 中已发布的某个扩展。 

-   **库包文件** -- 如果应用依赖于未在 AppSource 中发布的其他应用，则为必需的。 此 .app 文件是一个现有应用，但尚未或者不会在 AppSource 中发布。

-   **应用测试自动化** -- 必需 - 为了对扩展进行自动化测试而必须创建的 VS Code 测试包。

2.  在“扩展的其他信息”部分中，上传你的扩展的其他信息。 在验证过程中将使用此信息。

    ![应用扩展的其他信息窗体](./media/d365-financials/image016.png)


-   **产品文档 URL** -- 必需 - 扩展的文档的 URL。

-   **主要使用方案** -- 必需 - 一个文档，其中列出了扩展的分步设置和使用详细信息。 可以在[用户方案文档](https://docs.microsoft.com/dynamics-nav/compliance/apptest-userscenario/)一文中找到示例。

-   **目标版本** -- 必需 - 选择要将应用部署到其中的版本。 选择“当前”将在当前市场版本中进行部署。 选择“下一个次版本”将使用要发布的下一个次版本进行部署。 选择“下一个主版本”将使用要发布的下一个主版本进行部署。

-   **需要高级 SKU** -- 可选 -- 如果应用需要高级 SKU，则选择“高级”按钮。 “服务管理”和“制造”仅在高级版中可用。 可以在[更改显示哪些功能](https://docs.microsoft.com/dynamics365/financials/ui-experiences)一文中找到有关基本版与高级版的详细信息。

-   **代码分析错误的解释** -- 可选 -- 一个文档，其中列出并解释了不满足要求的任何代码。

-   **受影响的核心功能的解释** -- 可选 -- 一个文档，其中列出并解释了受扩展限制的任何核心功能。

-   **测试帐户** -- 可选 -- 用于远程服务、网站等等的用户帐户，完成端到端使用测试将需要这些帐户。

-   **用户体验要求异常** -- 可选 -- 一个文档，其中列出并解释了扩展不满足的任何用户体验要求。

下一步是为你的产品/服务添加店面详细信息。
