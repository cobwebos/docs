---
title: include 文件
description: include 文件
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: db6f5927e1cbdb3527014ccbb277c7ec20b02600
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "84231575"
---
1. **在“解决方案资源管理器”** 中，右键单击该项目并选择“发布”****。

1. 在“选取发布目标”**** 中，使用下表中指定的发布选项： 

    | 选项      | 说明                                |
    | ------------ |  -------------------------------------------------- |
    | **Azure Functions 消耗计划** | 在 Azure 云环境中创建一个在[消耗计划](../articles/azure-functions/functions-scale.md#consumption-plan)中运行的函数应用。 使用消耗计划时，只需为函数应用的执行付费。 其他托管计划会产生更高的成本。 如果在消耗计划之外的计划中运行，则必须管理[函数应用的缩放](../articles/azure-functions/functions-scale.md)。| 
    | **新建** | 在 Azure 中创建具有相关资源的新函数应用。 <br/>如果选择“选择现有”****，则 Azure 中现有函数应用中的所有文件都会被本地项目中的文件覆盖。 只有在将更新重新发布到现有函数应用时，才使用此选项。 |
    | **从包文件运行** | 启用[从包运行](../articles/azure-functions/run-functions-from-deployment-package.md)模式，函数应用将使用 [Zip 部署](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy)进行部署。 此部署可以提高性能，建议通过它来运行函数。 <br/>如果不使用此选项，请确保在将函数应用项目发布到 Azure 之前，先停止其在本地的运行。 |

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile.png" alt-text="创建发布配置文件":::


1. 选择“创建配置文件”****。 如果尚未从 Visual Studio 登录到 Azure 帐户，请选择“登录”****。 也可以创建免费 Azure 帐户。

1. 在“应用服务:**** 新建”中，使用下表中指定的值：

    | 设置      | Value  | 说明                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名称** | 全局唯一名称 | 用于唯一标识新 Function App 的名称。 接受此名称或输入新名称。 有效的字符是 `a-z`、`0-9` 和 `-`。 |
    | **订阅** | 你的订阅 | 要使用的 Azure 订阅。 接受此订阅，或从下拉列表中选择一个新订阅。 |
    | **[资源组](../articles/azure-resource-manager/management/overview.md)** | 资源组的名称 |  要在其中创建函数应用的资源组。 从下拉列表中选择现有资源组，或者选择“新建”来创建新的资源组。****|
    | **[托管计划](../articles/azure-functions/functions-scale.md)** | 托管计划的名称 | 选择“新建”即可配置一个无服务器计划。**** 确保在“大小”下选择“消耗量”。**** **** 将项目发布到在[消耗计划](../articles/azure-functions/functions-scale.md#consumption-plan)中运行的函数应用时，只需为函数应用的执行付费。 其他托管计划会产生更高的成本。 如果在“消耗”之外的计划中运行，则必须管理[函数应用的缩放](../articles/azure-functions/functions-scale.md)。****  |
    | **位置** | 应用服务的位置 | 在靠近你或者靠近函数访问的其他服务的[区域](https://azure.microsoft.com/regions/)中选择一个**位置**。 |
    | **[Azure 存储](../articles/storage/common/storage-account-create.md)** | 常规用途存储帐户 | Functions 运行时需要 Azure 存储帐户。 选择“新建”即可配置常规用途存储帐户。**** 也可选择一个符合[存储帐户要求](../articles/azure-functions/functions-scale.md#storage-account-requirements)的现有帐户。  |

    ![“创建应用服务”对话框](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. 选择“创建”****，使用这些设置在 Azure 中创建函数应用及其相关的资源，并部署函数项目代码。 

1. 选择“发布”，然后等待部署完成。 

    部署完成后，Azure 中函数应用的根 URL 将显示在“发布”选项卡中。 
    
1.  在“发布”选项卡中，请选择“在 Cloud Explorer 中管理”。 此操作会在 Cloud Explorer 中打开新的函数应用 Azure 资源。 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="创建发布配置文件":::
    
    借助 Cloud Explorer，可使用 Visual Studio 来查看网站的内容，启动和停止函数应用，并直接浏览到 Azure 上和 Azure 门户中的函数应用资源。 