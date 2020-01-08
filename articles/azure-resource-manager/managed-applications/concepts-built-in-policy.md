---
title: 使用策略为托管应用程序部署关联
description: 了解如何使用 Azure 策略服务为托管应用程序部署关联。
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: ec0fe8f66ef2ad2458b4ffad0e848591793e5b05
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650937"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>使用 Azure 策略为托管应用程序部署关联

Azure 策略可用于部署关联，以将资源关联到托管应用程序。 本文介绍了一种用于部署关联的内置策略以及如何使用该策略。

## <a name="built-in-policy-to-deploy-associations"></a>用于部署关联的内置策略

部署托管应用程序的关联是一种内置策略，可用于部署关联以将资源关联到托管应用程序。 此策略接受三个参数：

- 托管应用程序 ID-此 ID 是资源需要关联的托管应用程序的资源 ID。
- 要关联的资源类型-这些资源类型是要关联到托管应用程序的资源类型的列表。 可以使用相同的策略将多个资源类型关联到托管应用程序。
- 关联名称前缀-此字符串是要添加到正在创建的关联资源的名称中的前缀。 默认值为 "DeployedByPolicy"。

此策略使用 DeployIfNotExists 评估。 它在资源提供程序处理了所选资源类型的创建或更新资源请求之后运行，并且该计算返回了成功状态代码。 然后，使用模板部署来部署关联资源。
有关关联的详细信息，请参阅[Azure 自定义提供程序资源载入](../custom-providers/concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>如何使用部署关联内置策略 

### <a name="prerequisites"></a>必备组件
如果托管应用程序需要订阅的权限来执行操作，则关联资源的策略部署不起作用，无需授予权限。

### <a name="policy-assignment"></a>策略分配
若要使用内置策略，请创建一个策略分配，并为托管应用程序策略分配部署关联。 成功分配策略后，策略将标识不合规的资源并为这些资源部署关联。

![分配内置策略](media/concepts-built-in-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>获取帮助

如果有关于 Azure 自定义资源提供程序开发的问题，请尝试[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers)。 该论坛上可能已有类似问题的解答，因此，在发贴之前请先查看以往的提问。 添加标记 ```azure-custom-providers``` 可以快速得到回复！

## <a name="next-steps"></a>后续步骤

本文介绍如何使用内置策略来部署关联。 请参阅以下文章了解详细信息：

- [概念： Azure 自定义提供程序资源载入](../custom-providers/concepts-resource-onboarding.md)
- [教程：资源加入自定义提供程序](../custom-providers/tutorial-resource-onboarding.md)
- [教程：在 Azure 中创建自定义操作和资源](../custom-providers/tutorial-get-started-with-custom-providers.md)
- [快速入门：创建自定义资源提供程序并部署自定义资源](../custom-providers/create-custom-provider.md)
- [如何：向 Azure REST API 添加自定义操作](../custom-providers/custom-providers-action-endpoint-how-to.md)
- [如何：向 Azure REST API 添加自定义资源](../custom-providers/custom-providers-resources-endpoint-how-to.md)
