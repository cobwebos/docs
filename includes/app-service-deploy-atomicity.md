---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93332002cd2ac513d125e0f9eb75dff4a2d8760c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836854"
---
## <a name="what-happens-to-my-app-during-deployment"></a>部署期间我的应用会发生什么情况？

所有受支持的部署方法中的文件进行更改`/home/site/wwwroot`您的应用程序的文件夹。 这些文件是相同的生产环境中运行。 因此，部署可能会因锁定的文件而失败。 在生产环境中的应用还行为可能无法预测在部署期间，因为并非所有文件在同一时间都更新。 可通过多种不同方式避免这些问题：

- 部署期间停止应用或对应用启用脱机模式。 有关详细信息，请参阅[处理已锁定的文件在部署期间](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)。
- 部署到[过渡槽](../articles/app-service/deploy-staging-slots.md)且启用了[自动交换](../articles/app-service/deploy-staging-slots.md#configure-auto-swap)。 
- 使用[运行从包](https://github.com/Azure/app-service-announcements/issues/84)而不是连续部署。
