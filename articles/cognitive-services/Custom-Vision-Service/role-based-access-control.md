---
title: 基于角色的访问控制-自定义视觉
titleSuffix: Azure Cognitive Services
description: 本文介绍如何为自定义视觉项目配置基于角色的访问控制。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: fc536b3b0c7af113919f274fc4bdd5fad63cbd06
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2020
ms.locfileid: "90057160"
---
# <a name="role-based-access-control"></a>基于角色的访问控制

自定义视觉支持 azure RBAC)  (azure RBAC，这是用于管理对 Azure 资源的个人访问权限的授权系统。 使用 RBAC，可以为不同的团队成员分配自定义视觉项目的不同级别的权限。 有关 RBAC 的详细信息，请参阅 [AZURE RBAC 文档](https://docs.microsoft.com/azure/role-based-access-control/)。

## <a name="add-role-assignment-to-custom-vision-resource"></a>将角色分配添加到自定义视觉资源

可以将 Azure RBAC 分配到自定义视觉资源。 若要授予对 Azure 资源的访问权限，请添加角色分配。
1. 在 [Azure 门户](https://ms.portal.azure.com/)中，选择“所有服务”。 
1. 然后选择 **认知服务**，并导航到特定自定义视觉培训资源。
   > [!NOTE]
   > 还可以设置整个资源组、订阅或管理组的 RBAC。 为此，请选择所需的作用域级别，然后导航到所需的项目 (例如，选择 " **资源组** "，然后单击到所需的资源组) 。
1. 选择左侧导航窗格中的 " **访问控制 (IAM) ** 。
1. 选择 " **角色分配** " 选项卡以查看此作用域的角色分配。
1. 选择 "**添加**" "添加  ->  **角色分配**"。
1. 在 " **角色** " 下拉列表中，选择要添加的角色。
1. 在“选择”列表中，选择用户、组、服务主体或托管标识。 如果看不到列表中的安全主体，可以键入 "选择" 框，在目录中搜索显示名称、电子邮件地址和对象标识符。
1. 选择“保存”以分配该角色。

几分钟后，将向目标分配所选作用域中的选定角色。

## <a name="custom-vision-role-types"></a>自定义视觉角色类型

使用下表来确定自定义视觉资源的访问需求。

|角色  |权限  |
|---------|---------|
|`Cognitive Service Custom Vision Contributor`     | 对项目的完全访问权限，包括创建、编辑或删除项目的功能。        |
|`Cognitive Service Custom Vision Trainer`     | 完全访问权限，但不允许创建或删除项目。 讲师可以查看和编辑项目，并对模型进行定型、发布、取消发布或导出。        |
|`Cognitive Service Custom Vision Labeler`     | 能够上传、编辑或删除定型图像以及创建、添加、删除或删除标记。 Labelers 可以查看项目，但不能更新除定型图像和标记之外的任何内容。         |
|`Cognitive Service Custom Vision Deployment`     | 能够发布、取消发布或导出模型。 Deployer 可以查看项目，但不能更新项目、培训图像或标记。        |
|`Cognitive Service Custom Vision Reader`     | 可以查看项目。 读者不能进行任何更改。        |
