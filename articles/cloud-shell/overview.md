---
title: "Azure Cloud Shell 概述 | Microsoft Docs"
description: "Azure Cloud Shell 的概述。"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: juluk
ms.openlocfilehash: ebf6f1256a280fdff18c0c9060614acf0d4a642b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="overview-of-azure-cloud-shell"></a>Azure Cloud Shell 的概述
Azure Cloud Shell 是一个用于管理 Azure 资源的交互式的可通过浏览器访问的 shell。
它使用户能够灵活选择最适合自己工作方式的 shell 体验。
Linux 用户可以选择 Bash 体验，Windows 用户可以选择 PowerShell。

通过 Azure 门户中的 Cloud Shell 图标启动：

![门户启动](media/overview/portal-launch-icon.png)

利用 Bash 或 PowerShell 中的 shell 选择器下拉列表：

![Cloud Shell 中的 Bash](media/overview/overview-bash-pic.png)

![PowerShell in Cloud Shell（预览版）](media/overview/overview-ps-pic.png)

## <a name="features"></a>功能
### <a name="browser-based-shell-experience"></a>基于浏览器的 shell 体验
Cloud Shell 能够访问以执行 Azure 管理任务为宗旨构建的基于浏览器的命令行体验。
利用 Cloud Shell 可以不受限制地以只有云才能提供的方式从本地计算机工作。

### <a name="choice-of-preferred-shell-experience"></a>选择偏好的 shell 体验
Azure Cloud Shell 使用户能够灵活选择最适合自己工作方式的 shell 体验。
Linux 用户可以选择 Bash in Cloud Shell，而 Windows 用户可以选择 PowerShell in Cloud Shell（预览版）。

### <a name="authenticated-and-configured-azure-workstation"></a>经身份验证的已配置 Azure 工作站
Cloud Shell 由 Microsoft 管理，因此预装了常用的命令行工具和语言支持，可以提高工作速度。 此外，Cloud Shell 能够安全地自动执行身份验证以立即通过 Azure CLI 2.0 或 Azure PowerShell cmdlet 访问资源。

查看 [Bash 体验](features.md#tools)和 [PowerShell（预览版）体验](features-powershell.md#tools)的完整工具列表。

### <a name="multiple-access-points"></a>多个访问点
除了从 Azure 门户访问以外，还可从以下位置访问 Cloud Shell：
* [Azure CLI 2.0“试用”文档](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)
* [Azure 移动应用](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-azure-files-storage"></a>连接 Azure 文件存储
Cloud Shell 计算机是临时的，因此需要装载 Azure 文件共享作为 `clouddrive` 来持久保存 $Home 目录。
首次启动时，Cloud Shell 会提示将替你创建资源组、存储帐户和文件共享。 这是一个一次性步骤，将来会针对所有会话自动附加。 单个文件共享可以映射，将 Bash 和 PowerShell in Cloud Shell（预览版）使用。

#### <a name="create-new-storage"></a>创建新存储
![](media/overview/basic-storage.png)

系统可代你创建本地冗余存储 (LRS) 帐户和 Azure 文件共享。 如果选择同时使用 Bash 和 PowerShell 环境，Azure 文件共享将用于这两种环境。 将收取常规存储费用。

将替你创建以下三个资源：
1. 资源组：`cloud-shell-storage-<region>`
2. 存储帐户：`cs<uniqueGuid>`
3. 文件共享：`cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Cloud Shell 中的 Bash 还会创建一个默认的 5-GB 磁盘映像用于持久保存 `$Home`。 $Home 目录中的所有文件（如 SSH 密钥）将持久保存在已装载的文件共享中存储的用户磁盘映像中。 在 $Home 目录和已装载的文件共享中保存文件时，请应用最佳做法。

#### <a name="use-existing-resources"></a>使用现有资源
![](media/overview/advanced-storage.png)

提供了一个高级选项用于将现有资源关联到 Cloud Shell。
在存储设置提示下，单击“显示高级设置”显示其他选项。
从下拉列表筛选分配的 Cloud Shell 区域和本地/全局冗余存储帐户。

[了解 Cloud Shell 存储、更新文件共享和上传/下载文件。](persisting-shell-storage.md)

## <a name="concepts"></a>概念
* Cloud Shell 在按会话按用户提供的临时计算机上运行。
* Cloud Shell 在 20 分钟没有交互活动后将超时
* 只能通过所附加的文件共享访问 Cloud Shell
* Cloud Shell 为 Bash 和 PowerShell 使用相同的文件共享
* 将针对每个用户帐户为 Cloud Shell 分配一台计算机
* 权限是按常规 Linux 用户设置的 (Bash)

详细了解 [Bash in Cloud Shell](features.md) 和 [PowerShell in Cloud Shell（预览版）](features-powershell.md)的功能。

## <a name="examples"></a>示例
* 使用脚本将 Azure 管理任务自动化
* 通过 Azure 门户和 Azure 命令行工具同时管理 Azure 资源
* 体验 Azure CLI 2.0 或 Azure PowerShell cmdlet

在 [Bash in Cloud Shell](quickstart.md) 和 [PowerShell in Cloud Shell（预览版）](quickstart-powershell.md)的快速入门中尝试这些示例。

## <a name="pricing"></a>定价
托管 Cloud Shell 的计算机是免费的，先决条件是具有已装载的 Azure 文件共享。 将收取常规存储费用。

## <a name="next-steps"></a>后续步骤
[Bash in Cloud Shell 快速入门](quickstart.md)
[PowerShell in Cloud Shell（预览版）快速入门](quickstart-powershell.md)