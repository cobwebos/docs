---
title: 管理模型和学习设置-Personalizer
description: 可以在自己的源代码管理系统中导出机器学习的模型和学习设置，以便进行备份。
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c544d058019c8d507f65dc6bfd854376b364abd1
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624293"
---
# <a name="how-to-manage-model-and-learning-settings"></a>如何管理模型和学习设置

可以在自己的源代码管理系统中导出机器学习的模型和学习设置，以便进行备份。

## <a name="export-the-personalizer-model"></a>导出个性化体验创建服务模型

从 "资源管理" 的 "**模型和学习设置**" 部分中，查看 "模型创建" 和 "上次更新日期" 并导出当前模型。 可以使用 Azure 门户或个性化体验创建服务 API 导出模型文件以进行存档。

![导出当前个性化体验创建服务模型](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>清除学习循环的数据

1. 在 Azure 门户的 Personalizer 资源的 "**模型和学习设置**" 页上，选择 "**清除数据**"。
1. 为了清除所有数据，并将学习循环重置为原始状态，请选择 "所有3个复选框"。

    ![在 Azure 门户中，清除 Personalizer 资源中的数据。](./media/settings/clear-data-from-personalizer-resource.png)

    |值|目的|
    |--|--|
    |记录个性化和奖励数据。|此日志记录数据用于脱机评估。 如果要重置资源，请清除数据。|
    |重置 Personalizer 模型。|此模型将在每次重新训练时进行更改。 此训练频率在**配置**页上的 "**上载模型频率**" 中指定。 |
    |将学习策略设置为默认值。|如果在脱机评估过程中更改了学习策略，则会将其重置为原始学习策略。|

1. 选择 "**清除所选数据**" 以开始清除过程。 状态在 Azure 通知的右上方导航中报告。

## <a name="import-a-new-learning-policy"></a>导入新的学习策略

[学习策略](concept-active-learning.md#understand-learning-policy-settings)设置确定模型定型的_超参数_。 执行[脱机评估](how-to-offline-evaluation.md)以查找新的学习策略。

1. 打开[Azure 门户](https://portal.azure.com)，然后选择 Personalizer 资源。
1. 选择 "**资源管理**" 部分中的 "**模型和学习设置**"。
1. 对于 "**导入学习设置**"，选择你用上面指定的 JSON 格式创建的文件，然后选择 "**上传**" 按钮。

    等待学习策略上传成功的通知。

## <a name="export-a-learning-policy"></a>导出学习策略

1. 打开[Azure 门户](https://portal.azure.com)，然后选择 Personalizer 资源。
1. 选择 "**资源管理**" 部分中的 "**模型和学习设置**"。
1. 对于 "**导入学习设置**"，请选择 "**导出学习设置**" 按钮。 这会将 `json` 文件保存到本地计算机。

## <a name="next-steps"></a>后续步骤

[了解如何管理学习策略](how-to-manage-model.md)
