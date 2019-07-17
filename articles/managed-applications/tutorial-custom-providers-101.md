---
title: 在 Azure 中创建自定义操作和资源
description: 本教程将介绍如何在 Azure 资源管理器中创建自定义操作和资源，以及如何将其集成到用于 Azure 资源管理器模板、Azure CLI、Azure Policy 和活动日志的自定义工作流中。
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 4bbfcf070611e3df5c0fe47070f2ab6961111e07
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799186"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>在 Azure 中创建自定义操作和资源

使用自定义提供程序，可以在 Azure 上自定义工作流。 自定义提供程序是 Azure 与 `endpoint` 之间的协定。 它允许将新的自定义 API 添加到 Azure 资源管理器，以启用新的部署和管理功能。 本教程将介绍如何向 Azure 中添加新的操作和资源以及如何将其集成的简单示例。

本教程分为以下步骤：

- 设置用于 Azure 自定义提供程序的 Azure Functions
- 创建用于自定义提供程序的 RESTful 终结点
- 创建和利用自定义提供程序

## <a name="setup-azure-functions-for-azure-custom-providers"></a>设置用于 Azure 自定义提供程序的 Azure Functions

本教程的此部分将详细介绍如何设置 Azure Function 以使用自定义提供程序。 自定义提供程序可以使用任何公共 URL。

- [设置用于 Azure 自定义提供程序的 Azure Functions](./tutorial-custom-providers-function-setup.md)

## <a name="authoring-a-restful-endpoint-for-custom-providers"></a>创建用于自定义提供程序的 RESTful 终结点

本教程的此部分将详细介绍如何创建用于自定义提供程序的 RESTful 终结点。

- [创建用于自定义提供程序的 RESTful 终结点](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-and-utilizing-the-custom-provider"></a>创建和利用自定义提供程序

本教程的此部分将详细介绍如何创建自定义提供程序并使用其自定义操作和资源。

- [创建和利用 Azure 自定义提供程序](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>后续步骤

在本文中，我们已了解了自定义提供程序以及如何构建自定义提供程序。 若要继续了解本教程的下一步，请参考以下内容：

- [教程：设置用于 Azure 自定义提供程序的 Azure Functions](./tutorial-custom-providers-function-setup.md)

如果你正在寻找参考资料或快速入门，下面是一些有用的链接：

- [快速入门：创建 Azure 自定义提供程序和部署自定义资源](./create-custom-provider.md)
- [如何：将自定义操作添加到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [如何：将自定义资源添加到 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
