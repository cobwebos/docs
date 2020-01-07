---
title: 设置 Azure Functions
description: 本教程讲述如何创建 Azure 函数应用并将其设置为与 Azure 自定义提供程序配合使用
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: b6984bfdf7ec078d02999bccf77f3592cfbcab27
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75648674"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>设置用于 Azure 自定义提供程序的 Azure Functions

自定义提供程序是 Azure 与终结点之间的协定。 使用自定义提供程序，可以在 Azure 中更改工作流。 本教程介绍如何设置 Azure 函数应用，将其用作自定义提供程序终结点。

## <a name="create-the-azure-function-app"></a>创建 Azure 函数应用

> [!NOTE]
> 在本教程中，我们创建一个简单的使用 Azure 函数应用的服务终结点。 但是，自定义提供程序可以使用任何可公开访问的终结点。 备用选项包括 Azure 逻辑应用、Azure API 管理，以及 Azure 应用服务的 Web 应用功能。

若要开始本教程，首先应该按[在 Azure 门户中创建第一个 Azure 函数应用](../../azure-functions/functions-create-first-azure-function.md)教程中的说明操作。 该教程创建一个 .NET Core Webhook 函数，该函数可以在 Azure 门户中修改。 它也是当前教程的基础。

## <a name="install-azure-table-storage-bindings"></a>安装 Azure 表存储绑定

若要安装 Azure 表存储绑定，请执行以下操作：

1. 转到用于 HttpTrigger 的“集成”选项卡。 
1. 选择“+ 新建输入”。 
1. 选择“Azure 表存储”。 
1. 安装 Microsoft.Azure.WebJobs.Extensions.Storage 扩展（如果尚未安装）。
1. 在“表参数名称”框中，输入 tableStorage。  
1. 在“表名称”框中，输入 myCustomResources。  
1. 选择“保存”，保存更新的输入参数。 

![显示表绑定的自定义提供程序概览](./media/create-custom-provider/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>更新 RESTful HTTP 方法

若要设置 Azure 函数，使之包含自定义提供程序 RESTful 请求方法，请执行以下操作：

1. 转到用于 HttpTrigger 的“集成”选项卡。 
1. 在“选定 HTTP 方法”下，选择 **GET**、**POST**、**DELETE** 和 **PUT**。 

![显示 HTTP 方法的自定义提供程序概览](./media/create-custom-provider/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>添加 Azure 资源管理器 NuGet 包

> [!NOTE]
> 如果 C# 项目文件在项目目录中缺失，可以手动添加它。 或者，它会在 Microsoft.Azure.WebJobs.Extensions.Storage 扩展安装到函数应用上以后出现。

接下来，请更新 C# 项目文件，使之包含有用的 NuGet 库。 有了这些库，就可以更方便地分析自定义提供程序的传入请求。 按步骤[从门户添加扩展](../../azure-functions/install-update-binding-extensions-manual.md)，更新 C# 项目文件，使之包含以下包引用：

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

以下 XML 元素是示例性的 C# 项目文件：

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

本教程介绍如何设置 Azure 函数应用，将其用作 Azure 自定义提供程序终结点。

若要了解如何创作 RESTful 自定义提供程序，请参阅[教程：创作 RESTful 自定义提供程序终结点](./tutorial-custom-providers-function-authoring.md)。

