---
title: 使用功能筛选器为一部分用户启用功能
titleSuffix: Azure App Configuration
description: 了解如何使用功能筛选器为一部分用户启用功能
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/9/2020
ms.author: lcozzens
ms.openlocfilehash: b01a22d5a7068ee49e718a66432f52a8f6ef02ac
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126488"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>使用功能筛选器为一部分用户启用功能

功能标志允许您在应用程序中激活或停用功能。 简单的功能标志为 on 或 off。 应用程序始终采用相同的行为方式。 例如，你可以在功能标志后推出新功能。 启用功能标志后，所有用户都将看到新功能。 禁用功能标志将隐藏新功能。

与此相反，_条件功能标志_允许动态启用或禁用功能标志。 根据功能标志条件，应用程序的行为可能有所不同。 假设要首先向小部分用户显示新功能。 使用条件功能标志，可以为某些用户启用功能标志，同时为其他用户禁用该标志。 _功能筛选器_将在每次计算时确定功能标志的状态。

`Microsoft.FeatureManagement` 库包括两个功能筛选器：

- `PercentageFilter` 基于百分比启用功能标志。
- `TimeWindowFilter` 在指定时间段内启用功能标志。

还可以创建自己的功能筛选器来实现[FeatureManagement。](/dotnet/api/microsoft.featuremanagement.ifeaturefilter)

## <a name="registering-a-feature-filter"></a>注册功能筛选器

可以通过调用 `AddFeatureFilter` 方法来注册功能筛选器，并指定功能筛选器的名称。 例如，以下代码将注册 `PercentageFilter`：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>在 Azure 应用配置中配置功能筛选器

某些功能筛选器具有其他设置。 例如，`PercentageFilter` 基于百分比激活某个功能。 它有一个设置用于定义要使用的百分比。

你可以为 Azure 应用配置中定义的功能标志配置这些设置。 例如，按照以下步骤操作可使用 `PercentageFilter` 为 web 应用的50% 请求启用功能标志：

1. 按照[快速入门：向 ASP.NET Core 应用添加功能标志](./quickstart-feature-flag-aspnet-core.md)中的说明创建具有功能标志的 web 应用。

1. 在 Azure 门户中，请切换到配置存储，并单击 "**功能管理器**"。

1. 单击在快速入门中创建的*Beta*功能标志的上下文菜单。 单击 **“编辑”** 。

    > [!div class="mx-imgBorder"]
    > ![编辑 Beta 功能标志](./media/edit-beta-feature-flag.png)

1. 在 "**编辑**" 屏幕上，选中 "**开**" 单选按钮（如果尚未选中）。 然后单击 "**添加筛选器**" 按钮。 （On 单选按钮的标签将更改为 **"** 读取**条件**"。）

1. 在 "**密钥**" 字段中，输入 " *Microsoft. 百分比*"。

    > [!div class="mx-imgBorder"]
    > ![添加功能筛选器](./media/feature-flag-add-filter.png)

1. 单击功能筛选键旁边的上下文菜单。 单击 "**编辑参数**"。

    > [!div class="mx-imgBorder"]
    > ![编辑功能筛选器参数](./media/feature-flag-edit-filter-parameters.png)

1. 将鼠标悬停在 "**名称**" 标题下，使文本框显示在网格中。 输入*值*的**名称**和**值**50。 **值**字段指示要为其启用功能筛选器的请求的百分比。

    > [!div class="mx-imgBorder"]
    > ![设置功能筛选器参数](./media/feature-flag-set-filter-parameters.png)

1. 单击 "**应用**" 以返回到**编辑功能标志**屏幕。 然后再次单击 "**应用**" 以保存功能标志设置。

1. 功能标志的**状态**现在显示为 "*条件*"。 此状态指示将根据功能筛选器强制执行的条件，按请求启用或禁用功能标志。

    > [!div class="mx-imgBorder"]
    > ![条件功能标志](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>操作中的功能筛选器

若要查看此功能标志的效果，请启动该应用程序，并多次点击浏览器中的 "**刷新**" 按钮。 你会看到*Beta*项出现在工具栏上约50% 的时间。 它在其余时间隐藏，因为 `PercentageFilter` 会停用部分请求的*测试*功能。 以下视频显示了此行为的操作。

> [!div class="mx-imgBorder"]
> ![PercentageFilter in action](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [功能管理概述](./concept-feature-management.md)