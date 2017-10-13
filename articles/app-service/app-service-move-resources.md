---
title: "将 Web 应用资源移到另一个资源组"
description: "说明将 Web 应用和应用服务从一个资源组移到另一个资源组的方案。"
services: app-service
documentationcenter: 
author: ZainRizvi
manager: erikre
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: zarizvi
ms.openlocfilehash: 1b5059dc052005b6079f70ecf6771a3771df8d87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="supported-move-configurations"></a>受支持的迁移配置
可以使用 [Resource Manager 迁移资源 API](../azure-resource-manager/resource-group-move-resources.md) 迁移 Azure Web 应用资源。

Azure Web 应用当前支持以下迁移方案：

* 将一个资源组的整个内容（Web 应用、应用服务计划和证书）移到另一个资源组。 
   > [!Note]
   > 在此场景中，目标资源组不能包含任何 Microsoft.Web 资源。

* 将单独的 Web 应用移到不同的资源组，同时仍然在其当前应用服务计划中托管这些 Web 应用（该应用服务计划保留在旧资源组中）。


