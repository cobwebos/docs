---
title: 实体链接 API 入门 | Microsoft Docs
description: 通过使用认知服务中的实体链接 API，可以分析文本，并将命名实体链接到知识库中的相关实体。
services: cognitive-services
author: DavidLiCIG
manager: wkwok
ms.service: cognitive-services
ms.component: entity-linking-intelligence
ms.topic: article
ms.date: 07/06/2016
ms.author: davl
ms.openlocfilehash: 54c4a3bbb3637c248bd7705ed291633368b542c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365524"
---
# <a name="get-started-with-entity-linking-api-in-c35"></a>C&#35; 中的实体链接 API 入门

Microsoft 实体链接是一款自然语言处理工具，可用于分析文本，并将命名实体链接到知识库中的相关实体。 

本教程通过将实体链接客户端库用作 NuGet 包来介绍实体链接。 

### <a name="Prerequisites">先决条件</a>

- Visual Studio 2015
- Microsoft 认知服务 API 密钥
- 获取客户端库和示例
- Microsoft 实体链接 NuGet 包

可以通过 [SDK](https://www.github.com/microsoft/cognitive-entitylinking-windows) 下载实体链接智能服务 API 客户端库。 必须将下载的 zip 文件解压缩到选定文件夹，许多用户选择解压缩到 Visual Studio 2015 文件夹。

### <a name="step-1-subscribe-entity-linking-intelligence-service-and-get-your-own-key">第 1 步：订阅实体链接智能服务并获取密钥</a>
必须先注册 API 密钥，然后才能使用实体链接智能服务。 请参阅[订阅](https://www.microsoft.com/cognitive-services/en-us/sign-up)。 主密钥和辅助密钥都可用于本教程。

### <a name="step-2-create-a-new-project-in-visual-studio">第 2 步：在 Visual Studio 中新建项目</a>

接下来，先在 Visual Studio 中新建项目。 首先，通过“开始”菜单启动 Visual Studio 2015。 然后，依次选择“已安装 → 模板 → Visual C# → Windows 通用 → 空白应用”作为项目模板，从而新建项目：

 ![创建通用应用](./Images/CreateUWP.png)

### <a name="step-3-add-the-entity-linking-nuget-package-to-your-project">第 3 步：向项目添加实体链接 NuGet 包</a>

将认知服务的实体链接发布为 NuGet.org 包。必须先安装此包，然后才能使用它。
若要将它添加到项目中，请转到“解决方案资源管理器”选项卡，右键单击项目，并选择“管理 Nuget 包”。

首先，在“NuGet 包管理器”窗口中的右上角，选择“NuGet.org”作为“包源”。 选择左上角中的“浏览”，并在搜索框中键入“ProjectOxford.EntityLinking”。 选择“Microsoft.ProjectOxford.EntityLinking”NuGet 包，并单击“安装”。

接下来，搜索并安装“Newtonsoft.Json”。 如果系统提示查看更改，请单击“确定”。 如果看到 EntityLinking 许可条款，请单击“我接受”。

实体链接现已安装为应用的一部分。 若要确认这一点，可检查解决方案资源管理器内的项目中是否显示“Microsoft.ProjectOxford.EntityLinking”****参考。

 ![项目中包含的 nuget 库](./Images/NugetLibraryInProject.png)
 
### <a name="step-4-add-an-input-and-output-text-block-to-your-apps-xaml">第 4 步：向应用的 XAML 添加输入和输出文本块</a>
转到“解决方案资源管理器”中的“MainPage.xaml”****，并双击此文件，在新窗口中打开它。 为方便起见，可以双击“设计器”选项卡中的“XAML”按钮，这会隐藏“可视化设计器”，并保留代码视图的所有空间。

 ![项目中包含的 nuget 库](./Images/UWPMainPage.png)
 
作为文本服务，功能可视化的最佳方法是，创建输入和输出文本块。 为此，请在“网格”中添加下列 XAML。 此代码添加以下三个组件：输入文本框、输出文本块和“开始”按钮。
 
 ```XAML
 <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="*"/>
        <RowDefinition Height="*" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <TextBox x:Name="inputBox" Grid.Row="0" TextWrapping="Wrap" Text="Enter a paragraph" Margin="10" AcceptsReturn="True" />
    <TextBlock x:Name="outputBlock" Grid.Row="1" TextWrapping="Wrap" Text="Result will be here" Margin="10" />
    <Button x:Name="button" Grid.Row="2" Content="Get Result" />
</Grid>
 ```
 
### <a name="step-5-proceed-to-add-entity-linking-intelligence-service">第 5 步：继续添加实体链接智能服务</a>
 
现已创建用户界面。 必须先添加按钮单击处理程序，然后才能使用实体链接服务。 在“解决方案资源管理器”中，打开“MainPage.xaml”。 在按钮末尾处添加 button_Click 处理程序。
 
 ```XAML
 <Button x:Name="button" Grid.Row="2" Content="Get Result" Click="button_Click" />
 ```
 
必须在代码中实现按钮单击处理程序。 在“解决方案资源管理器”中，打开“MainPage.xaml.cs”，以实现按钮单击处理程序。 EntityLinkingServiceClient 是用于检索实体链接响应的包装器。 EntityLinkingServiceClient 的构造函数参数是认知服务订阅密钥。 粘贴在“第 1 步”中获取的订阅密钥，以调用实体链接服务。 

下面的示例代码使用实体链接服务在响应中添加“wikipediaId”。 
 
 ```csharp
 private async void button_Click(object sender, RoutedEventArgs e)
{
    var text = this.inputBox.Text;
    var client = new EntityLinkingServiceClient("Your subscription key","https://api.labs.cognitive.microsoft.com");
    var linkResponse = await client.LinkAsync(text);
    var result = string.Join(", ", linkResponse.Select(i => i.WikipediaID).ToList());
    this.outputBlock.Text = result;
}
 ```
 
现在可运行你的首个自然语言处理实体链接应用。 按 F5 键编译和发布应用。 将文本片段或段落粘贴到输入框中。 按“获取结果”按钮，并观察输出块中确定的实体。
 
 ![UWP 示例结果](./Images/DemoCodeResult.png)
 
### <a name="summary">摘要</a>
 
本教程介绍了如何只使用几行 C# 和 XAML 代码，即可创建利用实体链接智能服务客户端库的应用。 

