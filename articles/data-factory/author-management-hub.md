---
title: 管理中心
description: 在 Azure 数据工厂管理中心管理连接、源代码管理配置和全局创作属性
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.date: 06/02/2020
ms.openlocfilehash: df5a13b3d7c3fea98cd571a241b9d25e36ffeea6
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570622"
---
# <a name="management-hub-in-azure-data-factory"></a>Azure 数据工厂中的管理中心

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

通过 Azure 数据工厂 UX 中的“管理”选项卡访问的管理中心是一个门户，托管数据工厂的全局管理操作。 在此处，你可以管理到数据存储和外部计算、源控制配置和触发器设置的连接。

## <a name="manage-connections"></a>管理连接

### <a name="linked-services"></a>链接服务

链接服务定义 Azure 数据工厂与外部数据存储和计算环境的连接信息。 有关详细信息，请参阅[链接服务概念](concepts-linked-services.md)。 链接服务的创建、编辑和删除在管理中心完成。

![管理链接服务](media/author-management-hub/management-hub-linked-services.png)

### <a name="integration-runtimes"></a>集成运行时

集成运行时是 Azure 数据工厂用于在不同的网络环境之间提供数据集成功能的计算基础结构。 有关详细信息，请参阅[集成运行时概念](concepts-integration-runtime.md)。 可以在管理中心创建、删除和监视集成运行时。

![管理集成运行时](media/author-management-hub/management-hub-integration-runtime.png)

## <a name="manage-source-control"></a>管理源代码管理

### <a name="git-configuration"></a>Git 配置

查看和编辑管理中心内配置的 git 存储库设置。 有关详细信息，请参阅 [Azure 数据工厂中的源代码管理](source-control.md)。

![管理 git 存储库](media/author-management-hub/management-hub-git.png)

### <a name="parameterization-template"></a>参数化模板

若要在从协作分支发布时重写生成的资源管理器模板参数，可以生成或编辑自定义参数文件。 有关详细信息，请了解如何 [使用资源管理器模板中的自定义参数](continuous-integration-deployment.md#use-custom-parameters-with-the-resource-manager-template)。 参数化模板仅在 git 存储库中工作时可用。 如果工作分支中不存在文件的 *arm-template-parameters-definition.js* ，则编辑默认模板将生成该文件。

![管理自定义参数](media/author-management-hub/management-hub-custom-parameters.png)

## <a name="manage-authoring"></a>管理创作

### <a name="triggers"></a>触发器

触发器确定何时应开始管道运行。 目前，触发器可以按挂钟计划触发，可以定期运行，也可以按事件触发。 有关详细信息，请参阅[触发器执行](concepts-pipeline-execution-triggers.md#trigger-execution)。 可以在管理中心创建、编辑、删除触发器或查看触发器的当前状态。

![屏幕截图，显示了创建、编辑、删除和查看触发器当前状态的位置。](media/author-management-hub/management-hub-triggers.png)

### <a name="global-parameters"></a>全局参数

全局参数是整个数据工厂的常量，可由管道在任何表达式中使用。 有关详细信息，请参阅[全局参数](author-global-parameters.md)。

![创建全局参数](media/author-global-parameters/create-global-parameter-3.png)

## <a name="next-steps"></a>后续步骤

了解如何在 ADF 中[配置 git 存储库](source-control.md)


