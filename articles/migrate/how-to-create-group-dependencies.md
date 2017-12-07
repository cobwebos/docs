---
title: "在 Azure Migrate 中使用组依赖项映射优化评估组 | Microsoft 文档"
description: "介绍如何在 Azure Migrate 服务中使用组依赖项映射优化评估。"
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0527e34e-a078-405e-aeb9-c91a5808112a
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: b4d6861f147fbb6e65a9d529f17f78b54075eb90
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2017
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>使用组依赖项映射优化组

本文介绍了如何为 [Azure Migrate](migrate-overview.md) 评估设置组依赖项映射。 当你想要在运行评估之前通过交叉检查组依赖项来优化现有组的设置时，通常都会使用此方法。 要运行组依赖项映射的组所包含的计算机不应超过 10 台。  

## <a name="modify-a-group"></a>修改组

1. 在 Azure Migrate 项目中，在“管理”下，单击“组” ****，然后选择组。
2. 在组页面上，单击“查看依赖项” ****，打开组依赖项映射。 

     ![查看组](./media/how-to-create-group-dependencies/create-group.png)

3. 若要查看更精细的依赖项，请单击时间范围并进行修改。 时间范围默认为 1 小时。 你可以修改时间范围，或指定开始和结束日期以及持续时间。
4. 按住 Ctrl 键单击以在映射上选择计算机。 从映射中添加或删除计算机。
    - 你只能添加已发现的计算机。
    - 在组中添加和删除计算机会使过去对此组的评估失效。
    - 修改组时，你可以视情况创建新的评估。
5. 单击“确定”以保存组。

    ![添加和删除](./media/how-to-create-group-dependencies/add-remove.png)

如果你想要检查显示在组依赖项映射中的特定计算机的依赖项，请[设置计算机依赖项映射](how-to-create-group-machine-dependencies.md)。


## <a name="next-steps"></a>后续步骤

[详细了解](concepts-assessment-calculation.md)如何计算评估。
