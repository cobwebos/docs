---
title: 管理 API 和服务 API 之间的差异
description: API 在 Azure Batch 服务的不同层级上工作。
ms.topic: conceptual
ms.date: 02/26/2020
ms.custom: seodec18
ms.openlocfilehash: 33b8c5980aba1090155d6b136c6707e928666abf
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83867585"
---
# <a name="service-level-and-management-level-apis"></a>服务级别和管理级别 API

Azure Batch 具有两组 API，一个用于服务级别，另一个用于管理级别。 命名通常相似，但它们返回不同的结果。 如果需要活动日志，则需要使用管理 API。 服务级别 API 会绕过 Azure 资源管理层，并且不会进行记录。


例如，Batch 管理和 Batch 服务都具有适用于池的 API。 
- 这个用于删除池的 API 直接以批处理帐户为目标： https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- 这个用于删除池的 API https://docs.microsoft.com/rest/api/batchmanagement/pool/delete 以 management.azure.com 层为目标。

