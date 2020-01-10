---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3d9b0a7f08efcf9ea5892467b638e95d0404f35f
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752180"
---
## <a name="what-happens-to-my-app-during-deployment"></a>部署期间我的应用会发生什么情况？

所有正式支持的部署方法将更改应用的 `/home/site/wwwroot` 文件夹中的文件。 这些文件用于运行你的应用程序。 因此，由于锁定了文件，部署可能会失败。 在部署过程中，应用程序的行为可能会不可预知，因为并不是同时更新所有文件。 对于面向客户的应用，这是不必要的。 可通过多种不同方式避免这些问题：

- 部署期间停止应用或对应用启用脱机模式。 有关详细信息，请参阅[在部署过程中处理锁定文件](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)。
- 部署到[过渡槽](../articles/app-service/deploy-staging-slots.md)且启用了[自动交换](../articles/app-service/deploy-staging-slots.md#configure-auto-swap)。 
- 使用 "[从包运行](https://github.com/Azure/app-service-announcements/issues/84)" 而不是连续部署。
