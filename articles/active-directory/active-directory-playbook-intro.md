---
title: "Azure Active Directory PoC 演练手册简介 | Microsoft Docs"
description: "研究并快速实现标识和访问管理方案"
services: active-directory
keywords: "Azure Active Directory, 操作手册, 概念证明, PoC"
documentationcenter: 
author: dstefanMSFT
manager: asuthar
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/12/2017
ms.author: dstefan
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: bca54013e765315d2bbf2691503872f5e9ca859a
ms.lasthandoff: 04/12/2017


---
# <a name="azure-active-directory-proof-of-concept-playbook-introduction"></a>Azure Active Directory 概念证明演练手册：简介

本文提供用于探索概念证明 (PoC) 中不同 Azure AD 功能的指导准则。 本文档的目标读者是标识架构师、IT 专业人员和系统集成商

## <a name="how-to-use-this-playbook"></a>如何使用此演练手册

1. 阅读[主题](active-directory-playbook-ingredients.md#theme)部分并根据需要选择感兴趣的内容。  
2. 通过选择符合业务目标的方案限定 PoC 的范围。 越短越好。 建议执行此操作时尽量快速高效，以将价值传递给利益干系人，同时最大程度地降低操作复杂性。  
3. 阅读[实现](active-directory-playbook-implementation.md)部分，了解各种方案以及它们将对环境的意义。 在每个方案中，将介绍如何设置（所谓的[构建基块](active-directory-playbook-building-blocks.md)）和导航方案。 
4. 每个构建基块都会说明所需的先决条件以及完成方案所需的大致时间。 这在规划过程中会有所帮助。 
5. 根据上述 1-3，定义在其中执行操作的[环境](active-directory-playbook-ingredients.md#environment)。 建议努力定义一个生产环境，为用户提供良好体验。 
6. 出现冲突要求时，可使用此有用的权衡矩阵 
   * 以主题为中心显示价值  
   * 准备、设置和执行方案时的顺畅程度 
   * 执行目标方案的最短时间 
   * 在限定条件内，尽可能接近生产 

>[!NOTE]
> 为方便起见，整篇文章中会不时提及一些特定的第三方应用程序和产品，作为示例。 Azure AD 支持[应用程序库](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)中的数千个应用程序，可根据需要和环境进行使用。 



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
