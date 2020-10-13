---
title: 作业大小超出错误
description: 描述如何在作业大小或模板过大时排查错误。
ms.topic: troubleshooting
ms.date: 10/07/2020
ms.openlocfilehash: 638bdef246fc908ab997bfb99e7526febdb3792e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91822147"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>解决超出作业大小的错误

本文介绍如何解决 **JobSizeExceededException** 和 **DeploymentSizeExceededException** 错误。

## <a name="symptom"></a>症状

部署模板时，会收到一条错误，指出部署已超出限制。

## <a name="cause"></a>原因

如果模板的大小超过 4 MB，则会出现此错误。 为使用 [copy](copy-resources.md) 创建多个实例的资源定义展开后，4 MB 的限制适用于该模板的最终状态。 最终状态还包括变量和参数的已解析值。

部署作业还包括有关请求的元数据。 对于大型模板，与模板组合的元数据可能会超出作业允许的大小。

模板的其他限制如下：

* 256 个参数
* 256 个变量
* 800 个资源（包括副本计数）
* 64 个输出值
* 模板表达式中不超过 24,576 个字符

## <a name="solution-1---simplify-template"></a>解决方案 1-简化模板

第一种选择是简化模板。 如果模板部署了大量不同的资源类型，则可以使用此选项。 考虑将模板划分为 [链接的模板](linked-templates.md)。 将资源类型划分为逻辑组，并为每个组添加链接模板。 例如，如果需要部署大量网络资源，则可以将这些资源移到链接模板中。

你可以将其他资源设置为依赖于链接的模板，并 [从链接模板的输出中获取值](linked-templates.md#get-values-from-linked-template)。

## <a name="solution-2---reduce-name-size"></a>解决方案 2-缩减名称大小

请尝试缩短用于 [参数](template-parameters.md)、 [变量](template-variables.md)和 [输出](template-outputs.md)的名称的长度。 如果通过复制循环重复这些值，则会将较大的名称相乘。

## <a name="solution-3---use-serial-copy"></a>解决方案 3-使用串行复制

第二种方法是将复制循环从 [并行更改为串行处理](copy-resources.md#serial-or-parallel)。 仅当怀疑错误来自通过复制部署大量资源时，才使用此选项。 此更改可能会显著增加部署时间，因为不会并行部署资源。
