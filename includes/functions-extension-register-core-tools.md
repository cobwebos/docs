---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: 2936d22eacef73daef4433b3fd296dd4757fa410
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031111"
---
如果无法使用扩展捆绑，则可以使用本地 Azure Functions Core Tools 来安装项目所需的特定扩展包。 

> [!NOTE]
> 若要使用核心工具手动安装扩展，必须安装 .NET Core 2.x SDK。

显式安装扩展时，会将名为 extensions 的 .NET 项目文件添加到项目的根目录。 此文件定义函数所需的 NuGet 包集。 虽然你可以使用此文件中的[NuGet 包引用](/nuget/consume-packages/package-references-in-project-files)，但核心工具允许你安装扩展，而无需手动编辑该文件。

有多种方法可以使用核心工具在本地项目中安装所需的扩展。 

#### <a name="install-all-extensions"></a>安装所有扩展 

使用以下命令自动添加本地项目中的绑定所使用的所有扩展包：

```dotnetcli
func extensions install
```
命令读取文件*上的function.js* ，以查看所需的包，并将其安装，并 (extensions) 重新生成扩展项目。 它在当前版本中添加任何新绑定，但不更新现有绑定。 使用 `--force` 选项可在安装新版本时将现有绑定更新为最新版本。

#### <a name="install-a-specific-extension"></a>安装特定扩展

使用以下命令在特定版本（在本例中为存储扩展插件）中安装特定扩展包：

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```