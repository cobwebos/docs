---
title: 使用策略部署关联
description: 了解如何使用 Azure 策略服务部署自定义提供程序的关联。
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 536b95eb07619d0ce2d02ec01e1f51ed52c1b5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650469"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>使用 Azure 策略部署自定义提供程序的关联

Azure 策略可用于部署关联以将资源关联到自定义提供程序。 本文介绍了一种用于部署关联的内置策略，并介绍了如何使用该策略。

## <a name="built-in-policy-to-deploy-associations"></a>用于部署关联的内置策略

部署自定义提供程序的关联是一种内置策略，可用于部署关联以将资源关联到自定义提供程序。 此策略接受三个参数：

- 自定义提供程序 ID - 此 ID 是需要关联资源的自定义提供程序的资源 ID。
- 要关联的资源类型 - 这些资源类型是要与自定义提供程序关联的资源类型的列表。 您可以使用同一策略将多个资源类型关联到自定义提供程序。
- 关联名称前缀 - 此字符串是要添加到正在创建的关联资源的名称中的前缀。 默认值为“DeployedByPolicy”。

DeployIfNotExists 评估 它在资源提供程序处理创建或更新资源请求并返回成功状态代码后运行。 目前可以使用任何模板部署方法部署此模板。
有关关联的详细信息，请参阅 [Azure 自定义提供程序资源载入](./concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>如何使用部署关联内置策略 

### <a name="prerequisites"></a>先决条件
如果自定义提供程序需要对策略范围的权限来执行操作，则不授予权限，关联资源的策略部署将无法工作。

### <a name="policy-assignment"></a>策略分配
要使用内置策略，请创建策略分配并为自定义提供程序策略分配部署关联。 然后，策略将标识不合规的资源，并部署这些资源的关联。

![分配内置策略](media/concepts-built-in-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>获取帮助

如果您对 Azure 自定义资源提供程序开发有疑问，请尝试在[堆栈溢出](https://stackoverflow.com/questions/tagged/azure-custom-providers)上询问它们。 该论坛上可能已有类似问题的解答，因此，在发贴之前请先查看以往的提问。 添加标记 ```azure-custom-providers``` 可以快速得到回复！

## <a name="next-steps"></a>后续步骤

在本文中，您了解了如何使用内置策略来部署关联。 请参阅以下文章以了解更多信息：

- [概念：Azure 自定义提供程序资源载入](./concepts-resource-onboarding.md)
- [教程：使用自定义提供程序进行资源载入](./tutorial-resource-onboarding.md)
- [教程：在 Azure 中创建自定义操作和资源](./tutorial-get-started-with-custom-providers.md)
- [快速入门：创建自定义资源提供程序并部署自定义资源](./create-custom-provider.md)
- [如何：将自定义操作添加到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [如何：将自定义资源添加到 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
