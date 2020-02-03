---
title: 更新 Visual Studio 的模板部署脚本以使用 Az PowerShell
description: 将 Visual Studio 模板部署脚本从 AzureRM 更新到 Az PowerShell
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963863"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>更新 Visual Studio 模板部署脚本以使用 Az PowerShell 模块

Visual Studio 16.4 支持在模板部署脚本中使用 Az PowerShell 模块。 但是，Visual Studio 不会自动安装该模块。 若要使用 Az 模块，需要执行以下四个步骤：

1. [卸载 AzureRM 模块](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [安装 Az module](/powershell/azure/install-az-ps)
1. 将 Visual Studio 更新到16.4
1. 更新项目中的部署脚本。

## <a name="update-visual-studio-to-164"></a>将 Visual Studio 更新到16.4

将 Visual Studio 的安装更新到版本16.4 或更高版本。 在升级过程中，请确保未选中 "Azure PowerShell" 组件。 由于已通过库安装了 Az 模块，因此不需要重新安装 AzureRM 模块。

如果已升级到16.4，并且已选中 Azure PowerShell 组件，则可以通过运行 Visual Studio 安装程序将其卸载。 请勿在 Azure 工作负荷或 "单个组件" 页上选择 "Azure PowerShell" 组件。

## <a name="update-the-deployment-script-in-your-project"></a>更新项目中的部署脚本

在部署脚本中，将出现的所有字符串“AzureRm”替换为“Az”。 可以参考此 [gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) 中的修订内容来查看更改。 有关将脚本升级到 Az 模块的详细信息，请参阅[将 Azure PowerShell 从 AzureRM 迁移到 az](/powershell/azure/migrate-from-azurerm-to-az)。

## <a name="next-steps"></a>后续步骤

若要了解如何使用 Visual Studio 项目，请参阅[通过 Visual Studio 创建和部署 Azure 资源组项目](create-visual-studio-deployment-project.md)。
