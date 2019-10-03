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
ms.openlocfilehash: 0ae9e7fa76c8ecbb724cf0f494e648df989dff30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478685"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>枚举 Azure 导入/导出服务中的作业
若要枚举订阅中的所有作业，请调用[列出作业](/rest/api/storageimportexport/jobs)操作。 `List Jobs` 返回作业列表以及以下属性：

-   作业的类型（导入或导出）

-   当前作业状态

-   作业的关联存储帐户

## <a name="next-steps"></a>后续步骤

* [使用导入/导出服务 REST API](storage-import-export-using-the-rest-api.md)
