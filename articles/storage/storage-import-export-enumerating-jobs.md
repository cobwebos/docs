---
title: "列出所有 Azure 导入/导出作业 | Microsoft Docs"
description: "了解如何列出订阅中的所有 Azure 导入/导出服务作业。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f2e619be-1bbd-4a54-9472-9e2f70a83b64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 50fbd0d214c825137c3ac6873be27b9d2d53cee1
ms.openlocfilehash: 5bbe99110b86cb15d23eadc48c1ea45bbab13daf
ms.lasthandoff: 02/16/2017


---

# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>枚举 Azure 导入/导出服务中的作业
若要枚举订阅中的所有作业，请调用[列出作业](/rest/api/storageimportexport/jobs#Jobs_List)操作。 `List Jobs` 返回作业列表以及以下属性：

-   作业的类型（导入或导出）

-   当前作业状态

-   作业的关联存储帐户

## <a name="see-also"></a>另请参阅
 [使用导入/导出服务 REST API](storage-import-export-using-the-rest-api.md)

