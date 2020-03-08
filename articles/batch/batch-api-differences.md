---
title: 管理 Api 和服务 Api 之间的差异-Azure Batch |Microsoft Docs
description: Api 在 Azure Batch 服务的不同层上工作。
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672761"
---
# <a name="service-level-and-management-level-apis"></a>服务级别和管理级别 Api

Azure Batch 具有两组 Api，一个用于服务级别，另一个用于管理级别。 命名通常相似，但它们返回不同的结果。 如果需要活动日志，则需要使用管理 Api。 服务级别 Api 会绕过 Azure 资源管理层，并且不会进行记录。


例如，batch 管理和 Batch 服务都具有适用于池的 Api。 
- 此用于删除池的 API 直接针对批处理帐户： https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- 此 API 用于删除池 https://docs.microsoft.com/rest/api/batchmanagement/pool/delete 以 management.azure.com 层为目标。

