---
title: 主动学习 - 个性化体验创建服务
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 8c1579be3d11ae14ca45ee861de2d4f705e5d62c
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663714"
---
# <a name="active-learning-and-learning-policies"></a>主动学习和学习策略 

当应用程序调用排名 API 时，你会收到内容的排名。 业务逻辑可以使用此排名来确定是否要向用户显示该内容。 显示排名的内容时，即表示发生了活动的排名事件。  如果应用程序未显示该排名的内容，即表示发生了非活动的排名事件。  

活动的排名事件信息将返回到个性化体验创建服务。 使用此信息可通过当前学习策略继续训练模型。

## <a name="active-events"></a>活动的事件

活动的事件应始终显示给用户，应该返回奖励调用来关闭学习循环。 

### <a name="inactive-events"></a>非活动事件 

非活动状态事件不应更改基础模型，因为用户没有任何机会从排名的内容中进行选择。

## <a name="dont-train-with-inactive-rank-events"></a>不要使用非活动排名事件进行训练 

对于某些应用程序，可能需要在尚不知道应用程序是否要向用户显示结果的情况下调用排名 API。 

在以下情况下会发生这种情况：

* 你可能正在预先呈现不一定可让用户看到的某个 UI。 
* 应用程序可能正在执行预测个性化，其中的“排名”调用是使用实时性不够高的上下文发出的，其输出不一定会由应用程序使用。 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>在排名调用期间禁用非活动排名事件的主动学习

若要禁用自动学习，请结合 `learningEnabled = False` 调用“排名”。

如果发送排名的奖励，则会隐式激活非活动事件的学习。

## <a name="learning-policies"></a>学习策略

学习策略确定模型训练的特定超参数。  基于不同的学习策略训练的两个相同数据模型会有不同的行为。

### <a name="importing-and-exporting-learning-policies"></a>导入和导出学习策略

可以通过 Azure 门户导入和导出学习策略文件。 在门户中可以保存、测试、替换现有策略，并在源代码管理中将其存档为项目，以供将来参考和审核。

### <a name="learning-policy-settings"></a>学习策略设置

不应更改**学习策略**中的设置。 仅当你了解所做的更改会对个性化体验创建服务造成何种影响时，才能更改设置。 在不了解这种后果的情况下更改设置会造成负面影响，包括使个性化体验创建服务模型失效。

### <a name="comparing-effectiveness-of-learning-policies"></a>比较学习策略的有效性

可以通过执行[脱机评估](concepts-offline-evaluation.md)，来比较不同的学习策略针对个性化体验创建服务日志中以往数据的有效性。

[上传自己的学习策略](how-to-offline-evaluation.md)以便与当前学习策略进行比较。

### <a name="discovery-of-optimized-learning-policies"></a>发现优化的学习策略

执行[脱机评估](how-to-offline-evaluation.md)时，个性化体验创建服务可以创建更优化的学习策略。 更优化的学习策略会在脱机评估中生成更好的奖励评分，在个性化体验创建服务中联机使用时会产生更好的结果。

创建优化的学习策略后，可将其直接应用到个性化体验创建服务，使之立即取代当前策略，或者可将其保存供进一步的评估，将来可以决定是要放弃、保存还是应用它。