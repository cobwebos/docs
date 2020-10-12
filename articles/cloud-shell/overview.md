---
title: Azure Cloud Shell 概述 | Microsoft Docs
description: Azure Cloud Shell 的概述。
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/20/2020
ms.author: damaerte
ms.openlocfilehash: f824bddf833a1e2c01a3b779abc2c5252d8e0547
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89468651"
---
# <a name="overview-of-azure-cloud-shell"></a>Azure Cloud Shell 的概述

Azure Cloud Shell 是一个用于管理 Azure 资源的、可通过浏览器访问的交互式经验证 shell。 它使用户能够灵活选择最适合自己工作方式的 shell 体验，无论是 Bash 还是 PowerShell。

可以通过以下三种方式访问 Cloud Shell：

- **直接链接**：打开浏览器到 [https://shell.azure.com](https://shell.azure.com) 。

- **Azure 门户**：选择 [Azure 门户](https://portal.azure.com)上的 Cloud Shell 图标：

    ![用于从 Azure 门户启动 Cloud Shell 的图标](media/overview/portal-launch-icon.png)

- **代码片段**：在 [Docs.microsoft.com]() 和 [Microsoft Learn](/learn/)上，选择与 Azure CLI 和 Azure PowerShell 代码片段一起显示的 " **试用** " 按钮：

    ```azurecli-interactive
    az account show
    ```

    ```azurepowershell-interactive
    Get-AzSubscription
    ```

    " **试用** " 按钮会直接打开 Cloud Shell，并使用 Bash (将 Azure CLI 代码段) 或 PowerShell (用于 Azure PowerShell 片段) 。

    若要运行此命令，请使用代码段中的 "**复制**"，使用**Ctrl** + **shift** + **v** (Windows/Linux) 或**Cmd** + **Shift** + **v** (macOS) 以粘贴命令，然后按**enter**。

## <a name="features"></a>功能

### <a name="browser-based-shell-experience"></a>基于浏览器的 shell 体验

Cloud Shell 能够访问以执行 Azure 管理任务为宗旨构建的基于浏览器的命令行体验。 利用 Cloud Shell 可以不受限制地以只有云才能提供的方式从本地计算机工作。

### <a name="choice-of-preferred-shell-experience"></a>选择偏好的 shell 体验

用户可以在 Bash 或 PowerShell 之间进行选择。

1. 选择 **Cloud Shell**。

    ![Cloud Shell 图标](media/overview/overview-cloudshell-icon.png)

2. 选择 " **Bash** " 或 " **PowerShell**"。

    ![选择 Bash 或 PowerShell](media/overview/overview-choices.png)

    首次启动后，可以使用 "shell 类型" 下拉控件在 Bash 和 PowerShell 之间切换：

    ![用于选择 Bash 或 PowerShell 的下拉控件](media/overview/select-shell-drop-down.png)

### <a name="authenticated-and-configured-azure-workstation"></a>经身份验证的已配置 Azure 工作站

Cloud Shell 由 Microsoft 管理，因此附带了常用的命令行工具和语言支持。 此外，Cloud Shell 还能够安全地自动执行身份验证以立即通过 Azure CLI 或 Azure PowerShell cmdlet 访问资源。

查看[安装在 Cloud Shell 中的完整工具列表。](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>集成的 Cloud Shell 编辑器

Cloud Shell 提供基于开源 Monaco Editor 的集成图形文本编辑器。 只需通过运行 `code .` 来创建和编辑配置文件，即可借助 Azure CLI 或 Azure PowerShell 进行无缝部署。

[详细了解 Cloud Shell 编辑器](using-cloud-shell-editor.md)。

### <a name="multiple-access-points"></a>多个访问点

Cloud Shell 是一个灵活的工具，可以通过以下项使用：

* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Azure CLI 文档](/cli/azure)
* [Azure PowerShell 文档](/powershell/azure/)
* [Azure 移动应用](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio Code Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>连接 Microsoft Azure 文件存储

Cloud Shell 计算机是临时的，但文件通过以下两种方式持久保存：通过磁盘映像，并通过名为的已装载文件共享 `clouddrive` 。 首次启动时，Cloud Shell 将提示它会代你创建资源组、存储帐户和 Azure 文件共享。 这是一个一次性步骤，将来会针对所有会话自动附加。 单个文件共享可以映射，将由 Cloud Shell 中的 Bash 和 PowerShell 使用。

详细了解如何装载 [新的或现有的存储帐户](persisting-shell-storage.md) ，或了解 [Cloud Shell 中使用的持久性机制](persisting-shell-storage.md#how-cloud-shell-storage-works)。

> [!NOTE]
> Cloud shell 存储帐户不支持 Azure 存储防火墙。

## <a name="concepts"></a>概念

* Cloud Shell 在按会话按用户提供的临时主机上运行
* Cloud Shell 在 20 分钟没有交互活动后将超时
* Cloud Shell 需要装载 Azure 文件共享
* Cloud Shell 对 Bash 和 PowerShell 使用相同的 Azure 文件共享
* 将针对每个用户帐户为 Cloud Shell 分配一台计算机
* Cloud Shell 使用文件共享中保存的 5-GB 映像持久保存 $HOME
* 在 Bash 中权限是按常规 Linux 用户设置的

详细了解 [Cloud Shell 中的 Bash](features.md) 和 [Cloud Shell 中的 PowerShell](./features.md) 的功能。

## <a name="pricing"></a>定价

托管 Cloud Shell 的计算机是免费的，先决条件是具有已装载的 Azure 文件共享。 将收取常规存储费用。

## <a name="next-steps"></a>后续步骤

[Cloud Shell 快速入门中的 Bash](quickstart.md) <br>
[Cloud Shell 中的 PowerShell 快速入门](quickstart-powershell.md)