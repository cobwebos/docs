---
title: Azure IoT Edge 模块套餐发布概述 | Microsoft Docs
description: 在 Azure 市场中发布 IoT Edge 模块套餐的过程概述。
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 68c58c16d7083182d412adb6ed0d243a3291cc42
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49432372"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>IoT Edge 模块套餐发布概述

本主题介绍 IoT Edge 模块套餐的发布过程和关键组成部分。 在将套餐发布到 [Azure 市场](https://azuremarketplace.microsoft.com)时，请从本文着手。

## <a name="publishing-process"></a>发布过程

概括而言，发布 IoT Edge 模块套餐的步骤包括：

1. 创建套餐<br> 提供有关套餐的详细信息。 此信息包括：套餐说明、营销材料、支持信息和资产规格。

2. 创建业务资产和技术资产<br> 为关联的解决方案（托管在 Azure 容器注册表中的 IoT Edge 模块映像）创建业务资产（法律文档和营销材料）和技术资产。

3. 创建 SKU<br> 创建与套餐关联的 SKU。 必须为要发布的每个映像指定唯一的 SKU。

4. 认证并发布套餐 <br>完成产品/服务和技术资产以后，即可发布该产品/服务。 提交后会启动发布过程。 在此过程中，将会测试、验证并认证该解决方案，然后在 Azure 市场中将其“上线”。

## <a name="parts-of-an-offer"></a>套餐的组成部分

以下文章介绍了 IoT Edge 模块套餐的关键组成部分。

- [先决条件](./cpp-prerequisites.md) <br>此文列出了在创建或发布 IoT Edge 模块套餐之前所要满足的技术和业务要求。
- [准备 IoT Edge 模块技术资产](./cpp-create-technical-assets.md) <br>此文介绍了如何准备 IoT Edge 模块的技术资产。 在 Azure 市场中发布 IoT Edge 模块之前，这些资产必须满足所有所需技术条件。
- [创建 IoT Edge 模块产品/服务](./cpp-create-offer.md) <br>此文列出了使用[云合作伙伴门户](https://cloudpartner.azure.com)创建新 IoT Edge 模块套餐项所要执行的步骤。
- [发布 IoT Edge 模块产品/服务](./cpp-publish-offer.md)<br> 此文介绍了如何提交套餐，以便在 Azure 市场中发布。

## <a name="next-steps"></a>后续步骤

查看将 IoT Edge 模块发布到 Microsoft Azure 市场所要满足的[技术和业务要求](./cpp-prerequisites.md)。
