---
title: 使用每个环境配置
titleSuffix: Azure App Configuration
description: 使用标签提供每个环境配置值
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056803"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>使用标签为不同的环境启用不同的配置

许多应用程序需要为不同的环境使用不同的配置。 假设应用程序具有定义要用于其后端数据库的连接字符串的配置值。 应用程序的开发人员使用与生产中使用的数据库不同的数据库。 应用程序使用的数据库连接字符串必须随着应用程序从开发移动到生产而更改。

在 Azure 应用配置中，可以使用*标签*为同一键定义不同的值。 例如，您可以定义具有不同值的*单个键，用于开发和**生产*。 您可以指定在连接到应用配置时要加载的标签。

为了演示此功能，我们将修改在"快速入门"中创建的 Web 应用[：使用 Azure 应用配置创建ASP.NET核心应用](./quickstart-aspnet-core-app.md)，以便使用不同的配置设置进行开发与生产。 请先完成快速入门，然后再继续。

## <a name="specify-a-label-when-adding-a-configuration-value"></a>添加配置值时指定标签

在 Azure 门户中，进入**配置资源管理器**并找到您在快速入门中创建的*TestApp：设置：FontColor*密钥。 选择其上下文菜单，然后单击"**添加值**"。

> [!div class="mx-imgBorder"]
> ![添加值菜单项](media/labels-add-value.png)

在 **"增值"** 屏幕上，输入红色**值**和**red****开发****标签**。 将**内容类型**留空。 选择“应用”。

## <a name="loading-configuration-values-with-a-specified-label"></a>使用指定标签加载配置值

默认情况下，Azure 应用配置仅加载没有标签的配置值。 如果已为配置值定义标签，则需要指定连接到应用配置时要使用的标签。

在最后一节中，您为*开发*环境创建了不同的配置值。 使用变量`HostingEnvironment.EnvironmentName`动态确定应用当前在哪个环境中运行的环境。 若要了解详细信息，请参阅[在 ASP.NET Core 中使用多个环境](/aspnet/core/fundamentals/environments)。

通过将环境名称传递到方法中，加载具有与当前环境对应的标签的`Select`配置值：

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
> 上述代码段从称为`AppConfigConnectionString`的环境变量加载应用配置连接字符串。 请确保此环境变量设置正确。

调用了两次 `Select` 方法。 第一次，它加载配置值，没有标签。 然后，它加载配置值与与当前环境对应的标签。 这些特定于环境的值将覆盖任何没有标签的相应值。 不需要为每个键定义特定于环境的值。 如果键没有与当前环境对应的标签的值，则使用没有标签的值。

## <a name="testing-in-different-environments"></a>在不同环境中进行测试

要测试不同的配置值，`launchSettings.json`请打开`Properties`目录下的文件。 找到`config`下的`profiles`条目。 在`environmentVariables`节中，将`ASPNETCORE_ENVIRONMENT`变量设置为`Production`。

设置新值后，生成并运行应用程序。

```dotnetcli
dotnet build
dotnet run
```

使用 Web 浏览器导航到`http://localhost:5000`。 您会注意到字体颜色为黑色。

![使用生产配置运行的 Web 应用程序](media/labels-website-prod.png)

现在更新`launchSettings.json`以将`ASPNETCORE_ENVIRONMENT`变量设置为`Development`。 再次运行 `dotnet run`。 您会注意到字体颜色现在是红色的。 这是因为应用程序现在使用具有`TestApp:Settings:FontColor``Development`标签的值。 所有其他配置值与其生产值保持不变。

![使用开发配置运行的 Web 应用程序](media/labels-website-dev.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [ASP.NET Core 中的配置](/aspnet/core/fundamentals/configuration/)
