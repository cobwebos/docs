---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 3929ffa81a8b558d8cb0dbd74135acc9cf1ea47b
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82876030"
---
## <a name="configure-your-local-environment"></a>配置本地环境

在开始之前，必须满足以下条件：

+ 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846 或更高的 2.x 版本。
::: zone-end  
::: zone pivot="programming-language-python"
+ Python 3.6 和 3.7 需要 [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846 或更高的 2.x 版本。 Python 3.8 需要 Core Tools [版本 3.x](../articles/azure-functions/functions-run-local.md#v2)。
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) 2.4 或更高版本。 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/)，活动 LTS 和维护 LTS 版本（建议使用 8.11.1 和 10.14.1）。
::: zone-end

::: zone pivot="programming-language-python"
+ Azure Functions 支持的 [Python 3.8（64 位）](https://www.python.org/downloads/release/python-382/)、[Python 3.7（64 位）](https://www.python.org/downloads/release/python-375/)、[Python 3.6（64 位）](https://www.python.org/downloads/release/python-368/)。 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2+](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java 开发人员工具包](https://aka.ms/azure-jdks)版本 8。

+ [Apache Maven](https://maven.apache.org) 3.0 或更高版本。

> [!IMPORTANT]
> JAVA_HOME 环境变量必须设置为 JDK 的安装位置，以完成本快速入门。
::: zone-end
