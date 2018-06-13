---
title: 嵌入 Azure Cloud Shell | Microsoft Docs
description: 了解如何嵌入 Azure Cloud Shell。
services: cloud-shell
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: juluk
ms.openlocfilehash: 3ceddb94336fc2703e6f916f05ab1ec3676cb50d
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
ms.locfileid: "27864878"
---
# <a name="embed-azure-cloud-shell"></a>嵌入 Azure Cloud Shell

嵌入 Cloud Shell 可让开发者及内容编写人员直接从专用 URL [shell.azure.com](https://shell.azure.com) 打开 Cloud Shell。这样就可立即为用户提供 Cloud Shell 身份验证、工具使用及最新 Azure CLI/Azure PowerShell 工具的完整功能。

常规尺寸按钮

[![](https://shell.azure.com/images/launchcloudshell.png "启动 Azure Cloud Shell")](https://shell.azure.com)

大型尺寸按钮

[![](https://shell.azure.com/images/launchcloudshell@2x.png "启动 Azure Cloud Shell")](https://shell.azure.com)

## <a name="how-to"></a>操作说明

复制以下内容，将 Cloud Shell 的启动按钮集成到 Markdown 文件：

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

用于嵌入弹出式 Cloud Shell 的 HTML 如下所示：
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>自定义体验

通过增加 URL 设置特定 shell 体验。
|体验   |代码   |
|---|---|
|最近使用的 shell   |shell.azure.com           |
|Bash                       |shell.azure.com/bash       |
|PowerShell                 |shell.azure.com/powershell |

## <a name="next-steps"></a>后续步骤
[Bash in Cloud Shell 快速入门](quickstart.md)<br>
[Cloud Shell 中的 PowerShell 快速入门](quickstart-powershell.md)
