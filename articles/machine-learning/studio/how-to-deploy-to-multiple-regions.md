---
title: 将工作室 Web 服务部署到多个区域 - Azure 机器学习工作室 | Microsoft Docs
description: 将新的 Web 服务部署（复制）到其他区域的步骤。 无需多个订阅或工作区即可轻松地将 Web 服务部署到多个区域。
services: machine-learning
documentationcenter: ''
author: ericlicoding
editor: cgronlun
ms.assetid: 36c60411-f2db-4ee2-9b66-b1f1d77a8f44
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.custom: seodec18
ms.author: amlstudiodocs
ms.openlocfilehash: 742f462ebc3bd191a045be2a0213b1d8bc52adc5
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2018
ms.locfileid: "53252671"
---
# <a name="deploy-an-azure-machine-learning-studio-web-service-to-multiple-regions"></a>将 Azure 机器学习工作室 Web 服务部署到多个区域

借助新的 Azure Web 服务，无需多个订阅或工作区即可轻松地将 Azure 机器学习工作室 Web 服务部署到多个区域。 

定价因区域而异，因此必须要部署 Web 服务的每个区域定义收费计划。

## <a name="to-create-a-plan-in-another-region"></a>在另一个区域中创建计划
1. 登录到 [Microsoft Azure 机器学习 Web 服务](https://services.azureml.net/)。
2. 单击“计划”菜单选项。
3. 在视图页中的“计划”上，单击“新建”。
4. 从“订阅”下拉列表中，选择新计划将驻留的订阅。
5. 从“区域”下拉列表中，选择适用于新计划的区域。 所选区域的“计划选项”会显示在该页的“计划选项”部分中。
6. 从“资源组”下拉列表中，选择适用于该计划的资源组。 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/resource-group-overview.md)。
7. 在“计划名称”中，键入计划的名称。
8. 在“计划选项”中，单击新计划的计费级别。
9. 单击“创建”。

## <a name="deploying-the-web-service-to-another-region"></a>将 Web 服务部署到另一个区域
1. 单击“Web 服务”菜单选项。
2. 选择要部署到新区域的 Web 服务。
3. 单击“复制”。
4. 在“Web 服务名称”中，键入 Web 服务的新名称。
5. 在“Web 服务描述”中，键入 Web 服务的描述。
6. 从“订阅”下拉列表中，选择新的 Web 服务将驻留的订阅。
7. 从“资源组”下拉列表中，选择适用于该 Web 服务的资源组。 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/resource-group-overview.md)。
8. 从“区域”下拉列表中，选择要部署 Web 服务的区域。
9. 从“存储帐户”下拉列表中，选择要存储 Web 服务的存储帐户。
10. 从“价格计划”下拉列表中，选择步骤 8 中所选的区域中的计划。
11. 单击“复制”。

