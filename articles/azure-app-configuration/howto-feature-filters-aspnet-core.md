---
title: 使用功能筛选器为一部分用户启用某个功能
titleSuffix: Azure App Configuration
description: 了解如何使用功能筛选器为一部分用户启用某个功能
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 5b2eb942581f6e4163012b0f767d04c02689bb7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88206769"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>使用功能筛选器为一部分用户启用某个功能

使用功能标志可以在应用程序中激活或停用功能。 简单的功能标志为“开”或“关”。 应用程序会始终采用相同的行为方式。 例如，你可以在一个功能标志后面推出新功能。 启用该功能标志后，所有用户都将看到新功能。 禁用功能标志将隐藏新功能。

相比之下，使用“条件功能标志”可以动态启用或禁用功能标志。 应用程序的行为可能会有所不同，具体取决于功能标志条件。 假设要首先向小部分用户显示新功能。 使用条件功能标志可以为某些用户启用功能标志，同时为其他用户禁用该标志。 功能筛选器在每次计算出其结果时确定功能标志的状态。

`Microsoft.FeatureManagement` 库包括两个功能筛选器：

- `PercentageFilter` 基于百分比启用功能标志。
- `TimeWindowFilter` 在指定时间段内启用功能标志。

还可以创建自己的功能筛选器来实现 [Microsoft.FeatureManagement.IFeatureFilter 接口](/dotnet/api/microsoft.featuremanagement.ifeaturefilter)。

## <a name="registering-a-feature-filter"></a>注册功能筛选器

可以通过调用 `AddFeatureFilter` 方法并指定功能筛选器名称来注册功能筛选器。 例如，以下代码将注册 `PercentageFilter`：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>在 Azure 应用配置中配置功能筛选器

某些功能筛选器具有其他设置。 例如，`PercentageFilter` 基于百分比激活某个功能。 它有一个设置用于定义要使用的百分比。

可以为 Azure 应用配置中定义的功能标志配置这些设置。 例如，执行以下步骤可使用 `PercentageFilter` 为针对某个 Web 应用的 50% 请求启用功能标志：

1. 遵循[快速入门：将功能标志添加到 ASP.NET Core 应用](./quickstart-feature-flag-aspnet-core.md)中的说明，以创建具有功能标志的 Web 应用。

1. 在 Azure 门户中，转到配置存储并单击“功能管理器”。

1. 单击在快速入门中创建的 Beta 功能标志的上下文菜单。 单击 **“编辑”** 。

    > [!div class="mx-imgBorder"]
    > ![编辑 Beta 功能标志](./media/edit-beta-feature-flag.png)

1. 在“编辑”屏幕上，选择“开”单选按钮（如果它尚未处于选中状态）。 然后单击“添加筛选器”按钮。 （“开”单选按钮的标签将更改为显示“条件”。） 

1. 在“键”字段中，输入“Microsoft.Percentage”。

    > [!div class="mx-imgBorder"]
    > ![添加功能筛选器](./media/feature-flag-add-filter.png)

1. 单击功能筛选键旁边的上下文菜单。 单击“编辑参数”。

    > [!div class="mx-imgBorder"]
    > ![编辑功能筛选器参数](./media/feature-flag-edit-filter-parameters.png)

1. 将鼠标悬停在“名称”标题上，使文本框显示在网格中。 输入“值”作为“名称”，并输入“50”作为“值”。 “值”字段指示要启用功能筛选器的请求的百分比。

    > [!div class="mx-imgBorder"]
    > ![设置功能筛选器参数](./media/feature-flag-set-filter-parameters.png)

1. 单击“应用”以返回到“编辑功能标志”屏幕。 然后再次单击“应用”以保存功能标志设置。

1. 功能标志的状态现在显示为“条件”。 此状态指示，功能标志将根据功能筛选器强制实施的条件，基于每个请求而被启用或禁用。

    > [!div class="mx-imgBorder"]
    > ![条件功能标志](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>正在起作用的功能筛选器

若要查看此功能标志的效果，请在浏览器中启动该应用程序，并多次点击“刷新”按钮。 你会看到 Beta 项在大约 50% 的时间中出现在工具栏上。 它在其余时间隐藏，因为 `PercentageFilter` 会为一部分请求停用 Beta 功能。 以下视频显示了正在起作用的这一行为。

> [!div class="mx-imgBorder"]
> ![正在起作用的 PercentageFilter](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [功能管理概述](./concept-feature-management.md)
