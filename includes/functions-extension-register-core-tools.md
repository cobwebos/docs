---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: a4f03223b5067d18f5d6e747b3bb630a86031b8f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689537"
---
如果无法使用扩展捆绑，则可以使用本地 Azure Functions Core Tools 来安装项目所需的特定扩展包。

> [!IMPORTANT]
> 不能在使用扩展捆绑的函数应用中显式安装扩展。 在 `extensionBundle` 显式安装扩展之前，删除 *host.js* 中的部分。

以下各项介绍了你可能需要手动安装扩展的一些原因：

* 需要访问在捆绑中不可用的特定扩展版本。
* 你需要访问未在捆绑中使用的自定义扩展插件。
* 你需要访问单个捆绑包中未提供的特定扩展组合。

> [!NOTE]
> 若要使用核心工具手动安装扩展，必须安装 [.Net Core 2.X SDK](https://dotnet.microsoft.com/download) 。 Azure Functions Core Tools 使用 .NET Core SDK 从 NuGet 安装扩展。 不需要知道 .NET 即可使用 Azure Functions 扩展。

显式安装扩展时，会将名为 extensions 的 .NET 项目文件添加到项目的根目录。 此文件定义函数所需的 NuGet 包集。 虽然你可以使用此文件中的 [NuGet 包引用](/nuget/consume-packages/package-references-in-project-files) ，但核心工具允许你安装扩展，而无需手动编辑该文件。

有多种方法可以使用核心工具在本地项目中安装所需的扩展。 

#### <a name="install-all-extensions"></a>安装所有扩展 

使用以下命令自动添加本地项目中的绑定所使用的所有扩展包：

```dotnetcli
func extensions install
```
命令读取文件 * 上的function.js* ，以查看所需的包，并将其安装，并 (extensions) 重新生成扩展项目。 它在当前版本中添加任何新绑定，但不更新现有绑定。 使用 `--force` 选项可在安装新版本时将现有绑定更新为最新版本。

如果函数应用使用核心工具无法识别的绑定，则必须手动安装特定扩展。

#### <a name="install-a-specific-extension"></a>安装特定扩展

使用以下命令在特定版本（在本例中为存储扩展插件）中安装特定扩展包：

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```
