---
title: 管理 API 和服务 API 之间的差异 - Azure 批处理 |微软文档
description: API 在 Azure 批处理服务的不同层上工作。
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672761"
---
# <a name="service-level-and-management-level-apis"></a>服务级别和服务级别 API

Azure Batch 有两组 API，一组用于服务级别，另一组用于管理级别。 命名通常相似，但它们返回的结果不同。 如果需要活动日志，则需要使用管理 API。 服务级别 API 绕过 Azure 资源管理层，并且未记录。


例如，批处理管理和批处理服务都具有池 API。 
- 此要删除池的 API 直接针对批处理帐户：https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- 此要删除池https://docs.microsoft.com/rest/api/batchmanagement/pool/delete的 API 面向management.azure.com层。

