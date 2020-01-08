---
title: 创建自定义操作和资源
description: 本教程介绍如何在 Azure 资源管理器中创建自定义操作和资源。 它还展示了自定义工作流如何与 Azure 资源管理器模板、Azure CLI、Azure 策略和 Azure 活动日志互操作。
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 45f18727b53b802ba746da41b47fe955543ed9d0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75648544"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>在 Azure 中创建自定义操作和资源

自定义提供程序是 Azure 与终结点之间的协定。 使用自定义提供程序，可以通过将新 API 添加到 Azure 资源管理器来更改 Azure 中的工作流。 通过这些自定义 API，资源管理器可以使用新的部署和管理功能。

本教程介绍如何向 Azure 中添加新的操作和资源以及如何将其集成的简单示例。

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>设置用于 Azure 自定义提供程序的 Azure Functions

本教程的第一部分介绍如何设置 Azure 函数应用以使用自定义提供程序：

- [设置用于 Azure 自定义提供程序的 Azure Functions](./tutorial-custom-providers-function-setup.md)

自定义提供程序可以使用任何公共 URL。

## <a name="author-a-restful-endpoint-for-custom-providers"></a>创作自定义提供程序的 RESTful 终结点

本教程的第二部分介绍如何为自定义提供程序创作 RESTful 终结点：

- [创建用于自定义提供程序的 RESTful 终结点](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>创建并使用自定义提供程序

本教程的第三部分介绍如何创建自定义提供程序并使用其自定义操作和资源：

- [创建并使用自定义提供程序](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>后续步骤

在本教程中，我们已了解了自定义提供程序以及如何构建自定义提供程序。 若要继续学习下一个教程，请参阅[教程：设置用于 Azure 自定义提供程序的 Azure Functions](./tutorial-custom-providers-function-setup.md)。

如果你正在寻找参考资料或快速入门，下面是一些有用的链接：

- [快速入门：创建 Azure 自定义资源提供程序和部署自定义资源](./create-custom-provider.md)
- [如何：将自定义操作添加到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [如何：将自定义资源添加到 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
