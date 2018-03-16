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
ms.date: 02/15/2018
ms.author: juluk
ms.openlocfilehash: 72338a4c168b4d3b7c918fbb16758724f73fefc2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="overview-of-azure-cloud-shell"></a>Azure Cloud Shell 的概述
Azure Cloud Shell 是一个用于管理 Azure 资源的交互式的可通过浏览器访问的 shell。
它使用户能够灵活选择最适合自己工作方式的 shell 体验。
Linux 用户可以选择 Bash 体验，Windows 用户可以选择 PowerShell。

尝试从 shell.azure.com 使用此按钮。

[![](https://shell.azure.com/images/launchcloudshell.png "启动 Azure Cloud Shell")](https://shell.azure.com)

尝试从 Azure 门户使用 Cloud Shell 图标。

![门户启动](media/overview/portal-launch-icon.png)

## <a name="features"></a>功能
### <a name="browser-based-shell-experience"></a>基于浏览器的 shell 体验
Cloud Shell 能够访问以执行 Azure 管理任务为宗旨构建的基于浏览器的命令行体验。
利用 Cloud Shell 可以不受限制地以只有云才能提供的方式从本地计算机工作。

### <a name="choice-of-preferred-shell-experience"></a>选择偏好的 shell 体验
Linux 用户可以使用 Bash in Cloud Shell，而 Windows 用户可以使用 shell 下拉列表中的“PowerShell in Cloud Shell (预览版)”。

![Cloud Shell 中的 Bash](media/overview/overview-bash-pic.png)

![PowerShell in Cloud Shell（预览版）](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>经身份验证的已配置 Azure 工作站
Cloud Shell 由 Microsoft 管理，因此附带了常用的命令行工具和语言支持。 此外，Cloud Shell 能够安全地自动执行身份验证以立即通过 Azure CLI 2.0 或 Azure PowerShell cmdlet 访问资源。

查看 [Bash 体验](features.md#tools)和 [PowerShell（预览版）体验](features-powershell.md#tools)的完整工具列表。

### <a name="multiple-access-points"></a>多个访问点
Cloud Shell 是一个灵活的工具，可以通过以下项使用：
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Azure CLI 2.0“试用”文档](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)
* [Azure 移动应用](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [VS Code Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>连接 Microsoft Azure 文件存储
Cloud Shell 计算机是临时的，因此需要装载 Azure 文件共享作为 `clouddrive` 来持久保存文件。

首次启动时，Cloud Shell 会提示代表你创建资源组、存储帐户和 Azure 文件共享。 这是一个一次性步骤，将来会针对所有会话自动附加。 单个文件共享可以映射，将 Bash 和 PowerShell in Cloud Shell（预览版）使用。

#### <a name="create-new-storage"></a>创建新存储
![](media/overview/basic-storage.png)

系统可代你创建本地冗余存储 (LRS) 帐户和 Azure 文件共享。 如果选择同时使用 Bash 和 PowerShell 环境，Azure 文件共享将用于这两种环境。 将收取常规存储费用。

将替你创建以下三个资源：
1. 资源组：`cloud-shell-storage-<region>`
2. 存储帐户：`cs<uniqueGuid>`
3. 文件共享：`cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Cloud Shell 中的 Bash 还会创建一个默认的 5-GB 磁盘映像用于持久保存 `$Home`。 $Home 目录中的所有文件（如 SSH 密钥）将持久保存在已装载的 Azure 文件共享中存储的用户磁盘映像中。 在 $Home 目录和已装载的 Azure 文件共享中保存文件时，请应用最佳做法。

#### <a name="use-existing-resources"></a>使用现有资源
![](media/overview/advanced-storage.png)

提供了一个高级选项用于将现有资源关联到 Cloud Shell。
在存储设置提示下，单击“显示高级设置”显示其他选项。

> [!Note]
> 从下拉列表筛选预先分配的 Cloud Shell 区域和 LRS/GRS/ZRS 存储帐户。

[了解 Cloud Shell 存储、更新 Azure 文件共享和上传/下载文件。](persisting-shell-storage.md)

## <a name="concepts"></a>概念
* Cloud Shell 在按会话按用户提供的临时主机上运行
* Cloud Shell 在 20 分钟没有交互活动后将超时
* Cloud Shell 需要装载 Azure 文件共享
* Cloud Shell 对 Bash 和 PowerShell 使用相同的 Azure 文件共享
* 将针对每个用户帐户为 Cloud Shell 分配一台计算机
* Bash 使用文件共享中保存的 5 GB 映像持久保存 $Home
* 在 Bash 中权限是按常规 Linux 用户设置的

详细了解 [Bash in Cloud Shell](features.md) 和 [PowerShell in Cloud Shell（预览版）](features-powershell.md)的功能。

## <a name="pricing"></a>定价
托管 Cloud Shell 的计算机是免费的，先决条件是具有已装载的 Azure 文件共享。 将收取常规存储费用。

## <a name="next-steps"></a>后续步骤
[Bash in Cloud Shell 快速入门](quickstart.md) <br>
[PowerShell in Cloud Shell（预览版）快速入门](quickstart-powershell.md)