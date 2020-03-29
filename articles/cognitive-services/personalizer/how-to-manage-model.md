---
title: 管理模型和学习设置 - 个性化
description: 机器学习的模型和学习设置可以导出到您自己的源代码管理系统中进行备份。
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c544d058019c8d507f65dc6bfd854376b364abd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624293"
---
# <a name="how-to-manage-model-and-learning-settings"></a>如何管理模型和学习设置

机器学习的模型和学习设置可以导出到您自己的源代码管理系统中进行备份。

## <a name="export-the-personalizer-model"></a>导出个性化体验创建服务模型

从"**模型"和学习设置**的资源管理部分，查看模型创建和上次更新日期并导出当前模型。 可以使用 Azure 门户或个性化体验创建服务 API 导出模型文件以进行存档。

![导出当前个性化体验创建服务模型](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>清除学习循环的数据

1. 在 Azure 门户中，对于个性化程序资源，在 **"模型和学习设置"** 页上，选择 **"清除数据**"。
1. 为了清除所有数据，并将学习循环重置为原始状态，请选择所有 3 个复选框。

    ![在 Azure 门户中，清除来自个性化程序资源的数据。](./media/settings/clear-data-from-personalizer-resource.png)

    |“值”|目的|
    |--|--|
    |记录个性化和奖励数据。|此日志记录数据用于脱机评估。 如果要重置资源，请清除数据。|
    |重置个性化程序模型。|这种模式在每次再培训时都会发生变化。 此训练频率在 **"配置**"页上的**上载模型频率**中指定。 |
    |将学习策略设置为默认值。|如果更改了学习策略作为脱机评估的一部分，则这将重置为原始学习策略。|

1. 选择 **"清除所选数据**"以开始清除过程。 状态在 Azure 通知中报告，在右上角的导航中。

## <a name="import-a-new-learning-policy"></a>导入新的学习策略

[学习策略](concept-active-learning.md#understand-learning-policy-settings)设置确定模型训练的_超参数_。 执行[脱机评估](how-to-offline-evaluation.md)以查找新的学习策略。

1. 打开[Azure 门户](https://portal.azure.com)，然后选择个性化工具资源。
1. 在 **"资源管理**"部分中选择 **"模型和学习设置**"。
1. 对于 **"导入学习设置**"，请选择使用上面指定的 JSON 格式创建的文件，然后选择 **"上传**"按钮。

    等待已成功上载学习策略的通知。

## <a name="export-a-learning-policy"></a>导出学习策略

1. 打开[Azure 门户](https://portal.azure.com)，然后选择个性化工具资源。
1. 在 **"资源管理**"部分中选择 **"模型和学习设置**"。
1. 对于 **"导入学习设置**"，请选择 **"导出学习设置"** 按钮。 这将文件`json`保存到本地计算机。

## <a name="next-steps"></a>后续步骤

[了解如何管理学习策略](how-to-manage-model.md)
