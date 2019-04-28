---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/13/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c8f45e4bb16c05c9f322dd04d2c80f6144744e64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60319993"
---
本文使用 PowerShell cmdlet。 若要运行 cmdlet，可以使用 Azure Cloud Shell。 Azure Cloud Shell 是免费的交互式 shell 具有常用 Azure 工具的预安装并配置为使用与你的帐户。 只需单击**复制**复制代码，将其粘贴到 Cloud Shell 中，并按 enter 来运行它。 可通过多种方式来启动 Cloud Shell：

|  |   |
|-----------------------------------------------|---|
| 单击代码块右上角的“试用”。 | ![本文中的 Cloud Shell](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| 在浏览器中打开 Cloud Shell。 | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| 单击 Azure 门户右上角菜单上的“Cloud Shell”按钮。 | [![门户中的 Cloud Shell](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

**在本地运行 PowerShell**

还可以在计算机本地安装并运行 Azure PowerShell cmdlet。 PowerShell cmdlet 经常更新。 如果未运行最新版本，在说明中指定的值可能无法使用。 若要查找你在本地运行的 PowerShell 版本，请使用 `Get-Module -ListAvailable Az` cmdlet。 若要进行安装或更新，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。