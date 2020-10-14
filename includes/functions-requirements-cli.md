---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: e969520e226999bc2b104fb4f757619832eaa165
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88944449"
---
## <a name="configure-your-local-environment"></a>配置本地环境

在开始之前，必须满足以下条件：

+ 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846 或更高的 2.x 版本。
::: zone-end  
::: zone pivot="programming-language-python"
+ 对应于已安装的 Python 版本的 Azure Functions Core Tools 版本：

   | Python 版本 | Core Tools 版本 |
   | -------------- | ------------------ |
   | Python 3.8     | [版本 3.x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3.6<br/>Python 3.7 | [版本 2.7.1846 或更高版本](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) 2.4 或更高版本。 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/)，活动 LTS 和维护 LTS 版本（建议使用 8.11.1 和 10.14.1）。
::: zone-end

::: zone pivot="programming-language-python"
+ Azure Functions 支持的 [Python 3.8（64 位）](https://www.python.org/downloads/release/python-382/)、[Python 3.7（64 位）](https://www.python.org/downloads/release/python-375/)、[Python 3.6（64 位）](https://www.python.org/downloads/release/python-368/)。 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [.NET Core SDK 3.1](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java 开发人员工具包](https://aka.ms/azure-jdks)版本 8。 

    > [!IMPORTANT]
    > + Java 11 的函数支持目前处于预览状态，Maven 原型默认创建 Java 8 部署。 如果要改为在 Java 11 上运行函数应用，则必须使用 Java 11 值手动更新 pom.xml 文件。 若要了解详细信息，请参阅 [Java 版本](../articles/azure-functions/functions-reference-java.md#java-versions)。 
    > + `JAVA_HOME` 环境变量必须设置为正确版本的 JDK 的安装位置，以完成本快速入门。

+ [Apache Maven](https://maven.apache.org) 3.0 或更高版本。

::: zone-end
