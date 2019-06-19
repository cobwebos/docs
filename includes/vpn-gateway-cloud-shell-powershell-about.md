---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 01358d13d30358a9950cbe35622df065fc5a6de5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133068"
---
本文使用 PowerShell cmdlet。 若要运行这些 cmdlet，可以使用 Azure Cloud Shell（在 Azure 中托管并通过浏览器使用的交互式 shell 环境）。 Azure Cloud Shell 随预安装的 Azure PowerShell cmdlet 一起提供。

若要在 Azure Cloud Shell 上运行本文中包含的任何代码，请打开 Cloud Shell 会话，对代码块使用“复制”  按钮以复制代码，然后使用 __Ctrl + Shift + V__（在 Windows 和 Linux 上）或 __Cmd + Shift + V__（在 macOS 上）将其粘贴到 Cloud Shell 会话中。 粘贴的文本不会自动执行，因此请按 **Enter** 运行代码。

可以通过以下方式启动 Azure Cloud Shell：

|  |   |
|-----------------------------------------------|---|
| 选择代码块右上角的“试用”。  这__不__会自动将文本复制到 Cloud Shell。 | ![Azure Cloud Shell 的“试用”示例](./media/cloud-shell-try-it/cli-try-it.png) |
| 在浏览器中打开 [shell.azure.com](https://shell.azure.com)。 | [![“启动 Azure Cloud Shell”按钮](./media/cloud-shell-try-it/launchcloudshell.png)](https://shell.azure.com) |
| 选择 [Azure 门户](https://portal.azure.com)右上角菜单上的“Cloud Shell”  按钮。 | ![Azure 门户中的“Cloud Shell”按钮](./media/cloud-shell-try-it/cloud-shell-menu.png) |

**在本地运行 PowerShell**

还可以在计算机本地安装并运行 Azure PowerShell cmdlet。 PowerShell cmdlet 经常更新。 如果未运行最新版本，在说明中指定的值可能无法使用。 若要查找的计算机上安装 Azure PowerShell 版本，请使用`Get-Module -ListAvailable Az`cmdlet。 若要进行安装或更新，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。
