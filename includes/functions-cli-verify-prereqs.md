---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 19f3a99c087c0755a82ce7940326708fd1f6a3e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80673140"
---
### <a name="prerequisite-check"></a>先决条件检查

+ 在终端或命令窗口中，运行 `func --version` 检查 Azure Functions Core Tools 的版本是否为 2.7.1846 或以上。

+ 运行 `az --version` 检查 Azure CLI 版本是否为 2.0.76 或以上。

+ 运行 `az login` 登录到 Azure 并验证活动订阅。

::: zone pivot="programming-language-python"  
+ 运行 `python --version` (Linux/MacOS) 或 `py --version` (Windows)，以检查 Python 版本是否报告 3.8.x、3.7.x 或 3.6.x。
::: zone-end