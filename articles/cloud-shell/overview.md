---
title: "Azure Cloud Shell（预览版）概述 | Microsoft Docs"
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
ms.date: 07/10/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 7165633cd354eeea2e3619f839338e6af1524e56
ms.contentlocale: zh-cn
ms.lasthandoff: 08/08/2017

---
# <a name="overview-of-azure-cloud-shell-preview"></a>Azure Cloud Shell（预览版）的概述
Azure Cloud Shell 是一个用于管理 Azure 资源的交互式的可通过浏览器访问的 shell。

![](media/overview-pic.png)

## <a name="features"></a>功能
### <a name="browser-based-shell-experience"></a>基于浏览器的 shell 体验
Cloud Shell 能够访问以执行 Azure 管理任务为宗旨构建的基于浏览器的命令行体验。 利用 Cloud Shell 可以不受限制地以只有云才能提供的方式从本地计算机工作。

### <a name="pre-configured-azure-workstation"></a>预配置的 Azure 工作站
Cloud Shell 预安装了常用的命令行工具和语言支持，因此可以更快速地执行工作。

[在此处查看 Azure Cloud Shell 的完整工具列表。](features.md#tools)

### <a name="automatic-authentication"></a>自动化身份验证
Cloud Shell 能够安全地自动针对每个会话执行身份验证以立即通过 Azure CLI 2.0 访问资源。

### <a name="connect-your-azure-file-storage"></a>连接 Azure 文件存储
Cloud Shell 计算机是临时的，因此需要装载 Azure 文件共享作为 `clouddrive` 来持久保存 $Home 目录。
首次启动时，Cloud Shell 会提示将替你创建资源组、存储帐户和文件共享。 这是一个一次性步骤，将来会针对所有会话自动附加。 

#### <a name="create-new-storage"></a>创建新存储
![](media/basic-storage.png)

可以替你创建一个本地冗余存储 (LRS) 帐户以及一个包含 5 GB 磁盘映像的 Azure 文件共享。 文件共享作为 `clouddrive` 装载，以便文件共享与用于同步和持久保存 $Home 目录的磁盘映像交互。 将收取常规存储费用。

将替你创建以下三个资源：
1. 资源组：`cloud-shell-storage-<region>`
2. 存储帐户：`cs<uniqueGuid>`
3. 文件共享：`cs-<user>-<domain>-com-uniqueGuid`

> [!Note]
> $Home 目录中的所有文件（如 SSH 密钥）将持久保存在已装载的文件共享中存储的用户磁盘映像中。 在 $Home 目录和已装载的文件共享中保存文件时，请应用最佳做法。

#### <a name="use-existing-resources"></a>使用现有资源
![](media/advanced-storage.png)

还提供了一个高级选项，支持将现有资源与 Cloud Shell 关联。 出现存储设置提示时，单击“显示高级设置”选择其他选项。 从下拉列表筛选分配的 Cloud Shell 区域和本地/全局冗余存储帐户。

[了解 Cloud Shell 存储、更新文件共享和上传/下载文件。] (persisting-shell-storage.md)

## <a name="concepts"></a>概念
* Cloud Shell 在按会话按用户提供的临时计算机上运行。
* Cloud Shell 在 20 分钟没有交互活动后将超时
* 只能通过所附加的文件共享访问 Cloud Shell
* 将针对每个用户帐户为 Cloud Shell 分配一台计算机
* 权限是按常规 Linux 用户设置的

[了解有关所有 Cloud Shell 功能的更多信息。](features.md)

## <a name="examples"></a>示例
* 创建或编辑脚本以自动管理 Azure 资源
* 同时通过 Azure 门户和 Azure CLI 2.0 管理资源
* 体验版 Azure CLI 2.0

[请尝试 Cloud Shell 快速入门中的所有这些示例。](quickstart.md)

## <a name="pricing"></a>定价
托管 Cloud Shell 的计算机是免费的，先决条件是具有已装载的 Azure 文件共享来持久保存 $Home 目录。 将收取常规存储费用。

## <a name="supported-browsers"></a>支持的浏览器
建议为 Cloud Shell 使用 Chrome、Edge 和 Safari。 虽然支持为 Cloud Shell 使用 Chrome、Firefox、Safari、IE 和 Edge，但 Cloud Shell 受制于具体的浏览器设置。

## <a name="troubleshooting"></a>故障排除
1. 使用 Azure Active Directory 订阅时，因出现以下错误而无法创建存储：400 DisallowedOperation。 若要解决此问题，请使用 Azure 订阅创建存储资源。 使用 AD 订阅无法创建 Azure 资源。

有关具体的已知限制，请访问 [Cloud Shell 的限制](limitations.md)。

