---
title: 设置用于 Azure 自定义提供程序的 Azure Functions
description: 本教程将讲述如何创建 Azure Function 并将其设置为与 Azure 自定义提供程序配合使用
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7e4de43659db88bfd9aad40cc3b9f1753189bba
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799116"
---
# <a name="setup-azure-functions-for-azure-custom-providers"></a>设置用于 Azure 自定义提供程序的 Azure Functions

可以通过自定义提供程序自定义 Azure 上的工作流。 自定义提供程序是 Azure 与`endpoint`之间的协定。 本教程将详述将 Azure Function 设置为自定义提供程序`endpoint`的过程。

本教程分为以下步骤：

- 创建 Azure Function
- 安装 Azure 表绑定
- 更新 RESTful HTTP 方法
- 添加 Azure 资源管理器 NuGet 包

本教程将以下述教程为基础：

- [通过 Azure 门户创建第一个 Azure Function](../azure-functions/functions-create-first-azure-function.md)

## <a name="creating-the-azure-function"></a>创建 Azure Function

> [!NOTE]
> 在本教程中，我们将使用 Azure Function 创建一个简单的服务终结点，但自定义提供程序可以使用任何可公开访问的`endpoint`。 Azure 逻辑应用、Azure API 管理和 Azure Web 应用是很好的替代。

若要开始本教程，你应该按[在 Azure 门户中创建第一个 Azure Function](../azure-functions/functions-create-first-azure-function.md) 教程中的说明操作。 此教程会创建一个 .NET Core Webhook 函数，该函数可以在 Azure 门户中修改。

## <a name="install-azure-table-bindings"></a>安装 Azure 表绑定

此部分将介绍安装 Azure 表存储绑定的快速步骤。

1. 导航到用于 HttpTrigger 的`Integrate`选项卡。
2. 单击`+ New Input`。
3. 选择 `Azure Table Storage`。
4. 安装 `Microsoft.Azure.WebJobs.Extensions.Storage`（如果尚未安装）。
5. 将`Table parameter name`更新为“tableStorage”，将`Table name`更新为“myCustomResources”。
6. 保存更新的输入参数。

![自定义提供程序概述](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>更新 RESTful HTTP 方法

此部分将介绍设置 Azure Function 以使之包含自定义提供程序 RESTful 请求方法的快速步骤。

1. 导航到用于 HttpTrigger 的`Integrate`选项卡。
2. 将`Selected HTTP methods`更新为：GET、POST、DELETE 和 PUT。

![自定义提供程序概述](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="modifying-the-csproj"></a>修改 csproj

> [!NOTE]
> 如果目录中缺少 csproj，可以手动添加它，也可以在函数上安装 `Microsoft.Azure.WebJobs.Extensions.Storage` 扩展，然后它就会出现。

接下来，我们将更新 csproj 文件，使之包含有用的 NuGet 库。有了这些库，就可以更方便地分析自定义提供程序的传入请求。 按[从门户添加扩展](../azure-functions/install-update-binding-extensions-manual.md)中的步骤操作，更新 csproj，使之包含以下包引用：

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

示例 csproj 文件：

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>后续步骤

在本文中，我们设置了 Azure Function，将其用作 Azure 自定义提供程序`endpoint`。 若要了解如何创作 RESTful 自定义提供程序`endpoint`，请继续学习下一篇文章。

- [教程：创建 RESTful 自定义提供程序终结点](./tutorial-custom-providers-function-authoring.md)
