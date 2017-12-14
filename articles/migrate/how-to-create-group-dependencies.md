---
title: "在 Azure Migrate 中使用组依赖项映射优化评估组 | Microsoft 文档"
description: "介绍如何在 Azure Migrate 服务中使用组依赖项映射优化评估。"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: c30d6546e7c2d471d4b262a8af1ce593b2c1c3fb
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2017
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
