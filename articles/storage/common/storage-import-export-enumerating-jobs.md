---
title: 列出所有 Azure 导入/导出作业 | Microsoft Docs
description: 了解如何列出订阅中的所有 Azure 导入/导出服务作业。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 017208c6fca7c4e55a45070f5aa21652e83e7e8d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462908"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>枚举 Azure 导入/导出服务中的作业
若要枚举订阅中的所有作业，请调用[列出作业](/rest/api/storageimportexport/jobs#Jobs_List)操作。 `List Jobs` 返回作业列表以及以下属性：

-   作业的类型（导入或导出）

-   当前作业状态

-   作业的关联存储帐户

## <a name="next-steps"></a>后续步骤

* [使用导入/导出服务 REST API](storage-import-export-using-the-rest-api.md)
