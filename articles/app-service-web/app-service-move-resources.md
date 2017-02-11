---
title: "将 Web 应用资源移到另一个资源组"
description: "说明将 Web Apps 和 App Services 从一个资源组移到另一个资源组的方案。"
services: app-service
documentationcenter: 
author: ZainRizvi
manager: wpickett
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2016
ms.author: zarizvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 08b8953b91a87a131f70309e41be3a9ce0123299


---
# <a name="supported-move-configurations"></a>受支持的迁移配置
可以使用 [ARM 迁移资源 API](../resource-group-move-resources.md) 迁移 Azure Web 应用资源。

Azure Web Apps 当前支持以下迁移方案：

* 将一个资源组的整个内容（Web 应用、应用服务计划和证书）移到另一个资源组 
  * 请注意：在此方案中，目标资源组不能包含任何 Microsoft.Web 资源
* 将单独的 Web 应用移到不同的资源组，同时仍然在其当前应用服务计划中托管这些 Web 应用（该应用服务计划保留在旧资源组中）




<!--HONumber=Nov16_HO3-->


