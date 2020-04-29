---
title: 使用按环境配置
titleSuffix: Azure App Configuration
description: 使用标签提供按环境配置值
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80056803"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>使用标签为不同环境启用不同配置

许多应用程序需要针对不同的环境使用不同的配置。 假设应用程序具有定义要用于其后端数据库的连接字符串的配置值。 应用程序的开发人员使用的数据库与生产环境中使用的数据库不同。 当应用程序从开发环境转移到生产环境时，应用程序所使用的数据库连接字符串必须更改。

在 Azure 应用配置中，可以使用*标签*为同一键定义不同的值。 例如，可以定义一个具有不同值的单个键用于*开发*和*生产*。 可以指定在连接到应用配置时要加载的标签。

为了演示此功能，我们将修改在快速入门中创建的 web 应用[：使用 Azure 应用配置创建 ASP.NET Core 应用](./quickstart-aspnet-core-app.md)，以便使用不同的配置设置进行开发与生产。 请先完成快速入门，然后再继续。

## <a name="specify-a-label-when-adding-a-configuration-value"></a>添加配置值时指定标签

在 Azure 门户中，进入**配置资源管理器**，找到你在快速入门中创建的*TestApp： Settings： FontColor*键。 选择其上下文菜单，然后单击 "**添加值**"。

> [!div class="mx-imgBorder"]
> ![添加值菜单项](media/labels-add-value.png)

在 "**添加值**" 屏幕上，输入 "**红色**" 和 "**开发**"**标签**的**值**。 保留**内容类型**为空。 选择“应用”。 

## <a name="loading-configuration-values-with-a-specified-label"></a>使用指定的标签加载配置值

默认情况下，Azure 应用配置仅加载没有标签的配置值。 如果已为配置值定义了标签，则需要指定连接到应用配置时要使用的标签。

在上一部分中，为*开发*环境创建了不同的配置值。 使用`HostingEnvironment.EnvironmentName`变量来动态确定应用当前正在运行的环境。 若要了解详细信息，请参阅[在 ASP.NET Core 中使用多个环境](/aspnet/core/fundamentals/environments)。

通过将环境名称传递到`Select`方法，使用与当前环境对应的标签加载配置值：

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> 上面的代码片段从名`AppConfigConnectionString`为的环境变量加载应用配置连接字符串。 请确保正确设置了此环境变量。

调用了两次 `Select` 方法。 第一次，它会加载不带标签的配置值。 然后，它将加载与当前环境对应的标签的配置值。 这些特定于环境的值会重写任何不带标签的对应值。 不需要为每个键都定义环境特定的值。 如果键不具有与当前环境对应的标签值，则使用不带标签的值。

## <a name="testing-in-different-environments"></a>在不同环境中测试

若要测试不同的配置值，请`launchSettings.json`打开`Properties`目录下的文件。 在下`config` `profiles`找到该条目。 在`environmentVariables`部分中，将`ASPNETCORE_ENVIRONMENT`变量设置为`Production`。

设置新值后，生成并运行应用程序。

```dotnetcli
dotnet build
dotnet run
```

使用 web 浏览器导航到`http://localhost:5000`。 你会注意到，字体颜色为黑色。

![用生产配置运行的 Web 应用程序](media/labels-website-prod.png)

现在更新`launchSettings.json`以将`ASPNETCORE_ENVIRONMENT`变量设置为`Development`。 再次运行 `dotnet run`。 你会注意到，字体颜色现在为红色。 这是因为应用程序现在使用具有`TestApp:Settings:FontColor` `Development`标签的值。 所有其他配置值与其生产值保持相同。

![通过开发配置运行的 Web 应用程序](media/labels-website-dev.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [ASP.NET Core 中的配置](/aspnet/core/fundamentals/configuration/)
