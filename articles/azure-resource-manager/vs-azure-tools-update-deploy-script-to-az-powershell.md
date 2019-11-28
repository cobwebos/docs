---
title: 更新 Visual Studio 的模板部署脚本以使用 Az Powershell
description: 将 Visual Studio 模板部署脚本从 AzureRM 更新到 Az Powershell
author: cweining
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: cweining
ms.openlocfilehash: c34cde5593b48c301826be3dd2641dc2490ee88d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149063"
---
# <a name="updating-the-visual-studio-template-deployment-script-to-use-az-powershell"></a>更新 Visual Studio 模板部署脚本以使用 Az Powershell

Visual Studio 16.4 支持在模板部署脚本中使用 Az Powershell。 Visual Studio 不会安装 Az Powershell 或自动更新资源组项目中的部署脚本。 若要使用较新的 Az Powershell，需要执行以下四项操作：
1. 卸载 AzureRM Powershell
1. 安装 Az Powershell
1. 更新到 Visual Studio 16.4
1. 更新项目中的部署脚本。

## <a name="uninstall-azurerm-powershell"></a>卸载 AzureRM Powershell
按照这些[说明](https://docs.microsoft.com/powershell/azure/uninstall-az-ps?view=azps-2.7.0#uninstall-the-azurerm-module)卸载 AzureRM Powershell。

## <a name="install-az-powershell"></a>安装 Az Powershell
按照这些[说明](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.7.0)安装 Az Powershell。

## <a name="update-visual-studio-to-164"></a>将 Visual Studio 更新到16.4
更新到 Visual Studio 16.4（如果可用）。 在升级过程中，请务必不要选中 Azure Powershell 组件。 由于 Az Powershell 是通过库安装的，你不希望连同 Visual Studio 一起安装 AzureRM 版本的 Powershell。

如果已升级到 16.4 并选中了 Azure Powershell 组件，可通过以下方式运行卸载该组件：运行 Visual Studio 安装程序，在“Azure 工作负荷”或者在单个“组件”页上取消选中该 Azure Powershell 组件。

## <a name="update-the-deployment-script-in-your-project"></a>更新项目中的部署脚本
在部署脚本中，将出现的所有字符串“AzureRm”替换为“Az”。 可以参考此 [gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) 中的修订内容来查看更改。 有关将脚本升级到 Az Powershell 的详细信息，请参阅此[文档](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-2.5.0)。


