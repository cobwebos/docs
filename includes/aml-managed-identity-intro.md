---
title: include 文件
description: include 文件
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: d8ee7b61897a9718d7078fc16eddc177fefbff43
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147373"
---
 Azure 机器学习计算群集还支持 [托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ，以验证对 Azure 资源的访问权限，而无需在代码中包含凭据。 托管标识分为两种类型：

* **系统分配的托管标识**直接在 Azure 机器学习计算群集上启用。 系统分配的标识的生命周期直接绑定到计算群集。 如果删除了计算群集，Azure 会自动清理 Azure AD 中的凭据和标识。
* **用户分配的托管标识**是通过 Azure 托管标识服务提供的独立 Azure 资源。 可以向多个资源分配用户分配的托管标识，并根据需要将其保留。