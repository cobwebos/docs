---
title: Azure Stack 验证的最佳做法。 | Microsoft Docs
description: 这篇文章具有作为服务的验证的最佳做法。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: b61c4a22d0c5343d41e35f2bbd66829b2b9e8d48
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336966"
---
# <a name="best-practices-for-validation-as-a-service"></a>作为一项服务验证的最佳做法

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

本文介绍如何管理即服务 (VaaS) 验证中的资源的最佳实践。 VaaS 资源的概述，请参阅[作为服务的关键概念验证](azure-stack-vaas-key-concepts.md)。

## <a name="solution-management"></a>解决方案管理

### <a name="naming-convention-for-vaas-solutions"></a>VaaS 解决方案的命名约定

使用一致的命名约定在 VaaS 中注册的所有解决方案。 例如，解决方案名称可以构造从下面的硬件属性，如下所示：

|产品名称 | 唯一的硬件元素 1 | 唯一的硬件元素 2 | 解决方案名称
|---|---|---|---|
我的解决方案 XYZ |  所有闪存 | 我切换 X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>何时创建新的 VaaS 解决方案

对在相同的硬件 SKU 运行工作流时，请使用相同的 VaaS 解决方案。 仅当为硬件 SKU 更改时，应创建一个新的 VaaS 解决方案。

## <a name="workflow-management"></a>工作流管理

### <a name="naming-convention-for-vaas-workflows"></a>VaaS 工作流的命名约定

用于所有 VaaS 工作流运行一致的命名约定。 例如，构造工作流的名称从下面的生成属性，如下所示：

|内部版本号 （主要） | 日期 | 解决方案大小 | 工作流名称
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>后续步骤

- 了解有关[作为服务的关键概念验证](azure-stack-vaas-key-concepts.md)
