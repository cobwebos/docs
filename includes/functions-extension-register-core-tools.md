---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: a4f03223b5067d18f5d6e747b3bb630a86031b8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88689537"
---
如果无法使用扩展捆绑包，则可在本地使用 Azure Functions Core Tools 来安装项目所需的特定扩展包。

> [!IMPORTANT]
> 不能在使用扩展捆绑包的函数应用中显式安装扩展。 在显式安装扩展之前，请删除 host.json 中的 `extensionBundle` 部分。

以下各项介绍了你可能需要手动安装扩展的一些原因：

* 需要访问捆绑包中不可用的扩展的特定版本。
* 需要访问捆绑包中不可用的自定义扩展。
* 需要访问单个捆绑包中不可用的特定扩展组合。

> [!NOTE]
> 若要使用 Core Tools 手动安装扩展，必须安装 [.NET Core 2.x SDK](https://dotnet.microsoft.com/download)。 Azure Functions Core Tools 使用 .NET Core SDK 从 NuGet 安装扩展。 不需要了解 .NET 即可使用 Azure Functions 扩展。

当你显式安装扩展时，系统会将名为 extensions.csproj 的 .NET 项目文件添加到项目的根目录。 此文件定义函数所需的 NuGet 包集。 尽管你可以使用此文件中的 [NuGet 包引用](/nuget/consume-packages/package-references-in-project-files)，但 Core Tools 允许你在不需手动编辑文件的情况下安装扩展。

可以通过多种方法使用 Core Tools 在本地项目中安装所需的扩展。 

#### <a name="install-all-extensions"></a>安装所有扩展 

使用以下命令自动添加本地项目中的绑定所使用的所有扩展包：

```dotnetcli
func extensions install
```
该命令读取 function.json 文件以了解所需的程序包，安装这些包并重新生成扩展项目 (extensions.csproj)。 它在当前版本中添加任何新绑定，但不更新现有绑定。 使用 `--force` 选项可在安装新版本时将现有绑定更新为最新版本。

如果函数应用使用 Core Tools 无法识别的绑定，则必须手动安装特定扩展。

#### <a name="install-a-specific-extension"></a>安装特定扩展

使用以下命令安装特定版本的特定扩展包（在本例中为存储扩展）：

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```
