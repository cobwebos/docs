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
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945160"
---
## <a name="what-happens-to-my-app-during-deployment"></a>部署期间我的应用会发生什么情况？

所有正式支持的部署方法将更改应用的 `/home/site/wwwroot` 文件夹中的文件。 这些文件用于运行你的应用程序。 因此，由于锁定了文件，部署可能会失败。 在部署过程中，应用程序的行为可能会不可预知，因为并不是同时更新所有文件。 对于面向客户的应用，这是不必要的。 可通过多种不同方式避免这些问题：

- [直接从 ZIP 包运行你的应用](../articles/app-service/deploy-run-package.md)，而无需解包。
- 部署期间停止应用或对应用启用脱机模式。 有关详细信息，请参阅[在部署过程中处理锁定文件](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)。
- 部署到[过渡槽](../articles/app-service/deploy-staging-slots.md)且启用了[自动交换](../articles/app-service/deploy-staging-slots.md#configure-auto-swap)。 
