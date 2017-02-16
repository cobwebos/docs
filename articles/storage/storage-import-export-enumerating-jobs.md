---
title: "枚举 Azure 导入/导出服务中的作业 | Microsoft 文档"
description: "了解如何枚举订阅中的所有 Azure 导入/导出服务作业。"
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
ms.date: 12/16/2016
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 65d8912c4eced92206ee1262f3e3307b3162cbd6


---

# <a name="enumerating-jobs"></a>枚举作业
若要枚举订阅中的所有作业，请调用[列出作业](/rest/api/storageimportexport/jobs#Jobs_List)操作。 `List Jobs` 返回作业列表以及以下属性：

-   作业的类型（导入或导出）

-   当前作业状态

-   作业的关联存储帐户

## <a name="see-also"></a>另请参阅
 [使用导入/导出服务 REST API](storage-import-export-using-the-rest-api.md)



<!--HONumber=Dec16_HO3-->


