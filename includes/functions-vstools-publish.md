---
title: include 文件
description: include 文件
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/02/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f1a8ecd0e53d28aed66546c41f95cc3855d1783a
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608092"
---
1. **在“解决方案资源管理器”** 中，右键单击该项目并选择“发布”  。

2. 依次选择“Azure Function App”、“新建”、“发布”。   

    ![选取发布目标](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png) 

    单击“从包文件运行(建议)”时，  会在启用[从包运行](../articles/azure-functions/run-functions-from-deployment-package.md)模式的情况下使用 [Zip 部署](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy)来部署函数应用。 建议通过这种方式来运行函数，可以提高性能。

    >[!CAUTION]
    >选择“选择现有”  时，Azure 中现有函数应用中的所有文件都将被本地项目中的文件覆盖。 仅在将更新重新发布到现有函数应用时才使用此选项。

3. 如果尚未将 Visual Studio 连接到 Azure 帐户，请选择“添加帐户...”  。

4. 在“创建应用服务”对话框中，使用在图片下方的表中指定的“托管”设置：  

    ![“创建应用服务”对话框](./media/functions-vstools-publish/functions-visual-studio-publish.png)

    | 设置      | 建议的值  | 说明                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **应用名称** | 全局唯一名称 | 用于唯一标识新 Function App 的名称。 |
    | **订阅** | 选择订阅 | 要使用的 Azure 订阅。 |
    |  [资源组](../articles/azure-resource-manager/resource-group-overview.md) | myResourceGroup |  要在其中创建 Function App 的资源组的名称。 选择“新建”  创建新的资源组。|
    | **[应用服务计划](../articles/azure-functions/functions-scale.md)** | 消耗量计划 | 单击“新建”  创建某个无服务器计划后，确保在“大小”  下选择“消耗量”  。 此外，选择离你近或离函数访问的其他服务近的[区域](https://azure.microsoft.com/regions/)中的**位置**。 在“消耗量”之外的计划中运行时，必须管理[函数应用的缩放](../articles/azure-functions/functions-scale.md)。   |
    | **[存储帐户](../articles/storage/common/storage-quickstart-create-account.md)** | 常规用途存储帐户 | Functions 运行时需要 Azure 存储帐户。 单击“新建”，创建常规用途存储帐户。  也可使用符合[存储帐户要求](../articles/azure-functions/functions-scale.md#storage-account-requirements)的现有帐户。  |

5. 单击“创建”  以使用这些设置在 Azure 中创建函数应用和相关资源，并部署函数项目代码。 

6. 完成部署后，请记下“站点 URL”  值，这是函数应用在 Azure 中的地址。

    ![发布成功消息](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
