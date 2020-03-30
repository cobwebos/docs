---
title: 使用功能筛选器为用户子集启用功能
titleSuffix: Azure App Configuration
description: 了解如何使用功能筛选器为用户子集启用功能
ms.service: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 181c97615985283011834dcf9145810b1563fb4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056998"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>使用功能筛选器为用户子集启用功能

功能标志允许您激活或停用应用程序中的功能。 简单的功能标志是打开或关闭的。 应用程序始终以相同的方式执行。 例如，您可以在要素标志后面推出一个新功能。 启用功能标志后，所有用户都会看到新功能。 禁用功能标志将隐藏新功能。

相反，_条件要素标志_允许动态启用或禁用要素标志。 应用程序的行为可能不同，具体取决于功能标志条件。 假设您首先要向一小部分用户显示您的新功能。 条件要素标志允许您为某些用户启用功能标志，同时为其他用户禁用它。 _要素筛选器_在每次计算要素标志时确定其状态。

该`Microsoft.FeatureManagement`库包括两个功能筛选器：

- `PercentageFilter`基于百分比启用要素标志。
- `TimeWindowFilter`在指定的时间窗口内启用要素标志。

您还可以创建自己的功能筛选器来实现[Microsoft.功能管理.I功能筛选器界面](/dotnet/api/microsoft.featuremanagement.ifeaturefilter)。

## <a name="registering-a-feature-filter"></a>注册功能筛选器

通过调用`AddFeatureFilter`方法来注册要素筛选器，指定要素筛选器的名称。 例如，以下代码注册`PercentageFilter`：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>在 Azure 应用配置中配置功能筛选器

某些功能筛选器具有其他设置。 例如，`PercentageFilter`根据百分比激活要素。 它有一个定义要使用的百分比的设置。

您可以为 Azure 应用配置中定义的功能标志配置这些设置。 例如，按照以下步骤对`PercentageFilter`Web 应用的 50% 请求启用功能标志：

1. 按照["快速入门"中的说明操作：将功能标志添加到ASP.NET核心应用](./quickstart-feature-flag-aspnet-core.md)以创建具有功能标志的 Web 应用。

1. 在 Azure 门户中，转到配置存储并单击 **"功能管理器**"。

1. 单击在快速入门中创建的*Beta*功能标志的上下文菜单。 单击 **“编辑”**。

    > [!div class="mx-imgBorder"]
    > ![编辑测试版功能标志](./media/edit-beta-feature-flag.png)

1. 在 **"编辑"** 屏幕中，如果尚未选中"**打开**"按钮，请选择该按钮。 然后单击"**添加筛选器"** 按钮。 （"**打开**单选"按钮的标签将更改为"**条件**"。

1. 在 **"键"** 字段中，输入*Microsoft.百分比*。

    > [!div class="mx-imgBorder"]
    > ![添加功能筛选器](./media/feature-flag-add-filter.png)

1. 单击要素筛选器键旁边的上下文菜单。 单击 **"编辑参数**"。

    > [!div class="mx-imgBorder"]
    > ![编辑要素筛选器参数](./media/feature-flag-edit-filter-parameters.png)

1. 将鼠标悬停在 **"名称"** 标题下，以便文本框显示在网格中。 输入值**名称**和*Value***值**50。 "**值**"字段指示启用要素筛选器的请求的百分比。

    > [!div class="mx-imgBorder"]
    > ![设置功能筛选器参数](./media/feature-flag-set-filter-parameters.png)

1. 单击 **"应用"** 以返回到 **"编辑要素"标志**屏幕。 然后单击"**再次应用**"以保存功能标志设置。

1. 要素标志**的状态**现在显示为*条件*。 此状态表示功能标志将根据功能筛选器强制执行的条件根据请求启用或禁用。

    > [!div class="mx-imgBorder"]
    > ![条件要素标志](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>功能筛选器在操作中

要查看此功能标志的效果，请启动应用程序并多次点击浏览器中的 **"刷新**"按钮。 您将看到*Beta*项大约 50% 的时间显示在工具栏上。 它隐藏了其余时间，因为`PercentageFilter`停用了请求子集的*Beta*功能。 以下视频显示此行为在起作用。

> [!div class="mx-imgBorder"]
> ![操作中的百分比筛选](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [功能管理概述](./concept-feature-management.md)
