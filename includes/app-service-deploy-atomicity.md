---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/08/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8709956adee06e4e783ac5a7b317b2c4dec43e73
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173422"
---
## <a name="what-happens-to-my-app-during-deployment"></a>部署期间我的应用会发生什么情况？

所有官方支持的部署方法均具有一个共同点：它们会更改应用的 `/home/site/wwwroot` 文件夹中的文件。 这些文件与生产中运行的文件相同。 因此，部署可能由于存在锁定文件而失败，或者由于并非所有文件同时更新，生产中的应用在部署期间可能出现不可预测的行为。 可通过多种不同方式避免这些问题：

- 部署期间停止应用或对应用启用脱机模式。 有关详细信息，请参阅 [Dealing with locked files during deployment](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)（处理部署过程中锁定的文件）。
- 部署到[过渡槽](../articles/app-service/deploy-staging-slots.md)且启用了[自动交换](../articles/app-service/deploy-staging-slots.md#configure-auto-swap)。 
- 改为使用[从程序包运行](https://github.com/Azure/app-service-announcements/issues/84)。
