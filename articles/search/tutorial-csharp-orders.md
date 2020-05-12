---
title: 有关结果排序的 C# 教程
titleSuffix: Azure Cognitive Search
description: 本 C# 教程演示如何对搜索结果排序。 它在前一个酒店项目的基础上编写，按主要属性和次要属性排序，并包含计分概要文件以添加提升条件。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 699b3e5b7e51e12ec0bf39ec0c9045dd3a27a6fd
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780600"
---
# <a name="tutorial-order-search-results-using-the-net-sdk"></a>教程：使用 .NET SDK 对搜索结果排序

一直到本系列教程的此部分为止，结果都是按默认顺序返回并显示的。 这可以是数据所在的顺序，或者可能已定义了一个默认计分概要文件，将在未指定任何排序参数时使用该文件  。 在本教程中，我们将探讨如何根据主属性对结果排序，然后对于主属性相同的结果，如何根据辅助属性对所选结果进行排序。 除了根据数值进行排序，最后一个示例还介绍了如何根据自定义计分概要文件进行排序。 我们还将再深入一些探讨复杂类型的显示  。

为了轻松比较所返回的结果，此项目构建在 [C# 教程：搜索结果分页 - Azure 认知搜索](tutorial-csharp-paging.md)教程中创建的分页项目的基础上编写的。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 根据一个属性对结果排序
> * 根据多个属性对结果排序
> * 根据到某地理点的距离筛选结果
> * 根据计分概要文件对结果排序

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

已设置并运行 [C# 教程：搜索结果分页 - Azure 认知搜索](tutorial-csharp-paging.md)项目。 此项目可以是你自己的版本，也可从 GitHub 安装：[创建第一个应用](https://github.com/Azure-Samples/azure-search-dotnet-samples)。

## <a name="order-results-based-on-one-property"></a>根据一个属性对结果排序

根据一个属性（比如酒店评级）对结果排序时，我们不仅想要已排序的结果，还想要确定排序正确。 换言之，如果我们根据评级进行排序，应在视图中显示该评级。

在本教程中，我们还将对结果的显示添加一些要素，例如添加每家酒店的最低房价和最贵房价。 随着我们对排序的深入了解，我们还将添加价值来确保当前排序的内容也在视图中显示。

无需修改任何模型即可实现排序。 需更新视图和控制器。 首先打开主控制器。

### <a name="add-the-orderby-property-to-the-search-parameters"></a>向搜索参数添加 OrderBy 属性

1. 要根据单个数值属性对结果进行排序，只需将 OrderBy 参数设置为属性的名称  。 在 Index(SearchData model) 方法中，向搜索参数添加以下行  。

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > 默认顺序为升序，但可向属性添加 asc 来明确这一点  。 降序排序通过添加 desc 进行指定  。

2. 现在运行应用，再输入任意常见搜索词。 不清楚结果是否按正确顺序显示，因为你（开发人员）和用户都没法轻易验证结果！

3. 让我们来明确结果是按评级进行排序的。 首先，将 hotels.css 文件中的 box1 和 box2 类替换为以下类（这些类是我们需要用于此教程的所有新类）   。

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    >[!Tip]
    >浏览器通常会缓存 css 文件，而这可能导致使用旧的 css 文件，忽略你编辑的内容。 要解决此问题的一个好方法是，向链接添加一个具有版本参数的查询字符串。 例如：
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >如果你认为浏览器正在使用旧式 css 文件，请更新版本号。

4. 在 Index(SearchData model) 方法中，向 Select 参数添加 Rating 属性    。

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. 打开视图 (index.cshtml)，将呈现循环 (&lt;!-- 显示酒店数据。--&gt;) 替换为以下代码  。

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

6. 需要能在所显示的第一个页面以及通过无限滚动调用的后续页面中进行评级。 对于这两种情况的后一种情况，我们需要更新控制器中的 Next 操作，还需要更新 scrolled 函数   。 首先从控制器开始，将 Next 方法更改为以下代码  。 此代码会创建并传递评级文本。

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";

                // Add three strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

7. 接下来，更新视图中的 scrolled 函数来显示评级文本  。

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 3) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                            }
                        });
                    }
                }
            </script>

    ```

8. 然后，再次运行应用。 搜索任意常见词（例如“wifi”），并验证结果是否按酒店评级降序进行排序。

    ![根据评级进行排序](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    你将注意到多家酒店的评级相同，因此它们在显示结果中的顺序又是找到数据的顺序（即任意顺序）。

    在探讨第二层排序的添加之前，让我们来添加一些代码，显示客房评级的范围。 我们将添加此代码，同时显示来自复杂类型的提取数据；也正因如此，我们能探讨根据房价进行的结果排序（可能是最低房价排第一）  。

### <a name="add-the-range-of-room-rates-to-the-view"></a>向视图添加房价范围

1. 向 Hotel.cs 模型添加包含最低房价和最高房价的属性。

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. 在主控制器中，在 Index(SearchData model) 操作结束时计算房价  。 在存储临时数据后添加计算结果。

    ```cs
                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    // Calculate room rates.
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
    ```

3. 在控制器的 Index(SearchData model) 操作方法中，向 Select 参数添加 Rooms 属性    。

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. 更改视图中的呈现循环，显示第一个结果页面的房价范围。

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

5. 更改主控制器中的 Next 方法，传递房价供后续结果页面使用  。

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

6. 更新视图中的 scrolled 函数来处理房价文本  。

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 4) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

7. 运行应用并验证是否已显示房价范围。

    ![显示房价范围](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

搜索参数的 **OrderBy** 属性将不接受 **Rooms.BaseRate** 之类的条目来提供最低房价，即使客房已按房价进行排序。 在这种情况下，房间不按费率排序。 为了显示示例数据集中已按房价排序的酒店，你必须在主控制器中对结果进行排序，并按所需顺序将这些结果发送到视图中。

## <a name="order-results-based-on-multiple-values"></a>根据多个值对结果排序

现在的问题是如何区分评级相同的酒店。 一个好方法是，根据酒店上次翻新的时间进行排序。 换言之，酒店翻新越晚，在结果中显示的位置越高。

1. 要添加第二层排序，请更改 Index(SearchData model) 方法中的 OrderBy 和 Select 属性，使其包含 LastRenovationDate 属性     。

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >可在 OrderBy 列表中输入任意数量的属性  。 如果酒店评级和翻新日期都相同，则可输入第三个属性来区分它们。

2. 同样，我们需要在视图中查看翻新日期，从而确定排序正确。 对于翻新之类的问题，可能只需查看年份即可。 将视图中的呈现循环更改为以下代码。

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

3. 更改主控制器中的 Next 方法，推进上次翻新日期的年份组件  。

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";
                var lastRenovatedText = $"Last renovated: {model.resultList.Results[n].Document.LastRenovationDate.Value.Year}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. 更改视图中的 scrolled 函数来显示翻新文本  。

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 5) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

5. 运行应用。 搜索常用词（例如“泳池”或“视野”），并验证评级相同的酒店现在是否按翻新日期降序显示。

    ![按翻新日期排序](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>根据到某地理点的距离筛选结果

最好按降序显示的属性包括评级和翻新日期等。 按降序排序的一个很好的例子就是按字母顺序显示的列表；例如，如果只有一个 OrderBy 属性，而它被设置为 HotelName，则按字母顺序显示   。 但是，对于我们的示例数据，到某地理点的距离更为合适。

要根据地理距离显示结果，需执行几个步骤。

1. 需输入带有纬度、经度和半径参数的筛选器，筛选出在距给定点指定半径之外的所有酒店。 首先向 POINT 函数提供纬度。 半径以公里为单位。

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. 上述筛选器不根据距离对结果排序，它只是删除离群值  。 要对结果排序，请输入可指定 geoDistance 方法的 OrderBy 设置  。

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. 虽然结果是 Azure 认知搜索通过距离筛选器返回的，但不返回在数据与指定的点之间计算得出的距离  。 如果要在结果中显示此值，请在视图中重新计算它。

    以下代码将计算两个纬度/经度点之间的距离。

    ```cs
        const double EarthRadius = 6371;

        public static double Degrees2Radians(double deg)
        {
            return deg * Math.PI / 180;
        }

        public static double DistanceInKm( double lat1,  double lon1, double lat2, double lon2)
        {
            double dlon = Degrees2Radians(lon2 - lon1);
            double dlat = Degrees2Radians(lat2 - lat1);

            double a = (Math.Sin(dlat / 2) * Math.Sin(dlat / 2)) + Math.Cos(Degrees2Radians(lat1)) * Math.Cos(Degrees2Radians(lat2)) * (Math.Sin(dlon / 2) * Math.Sin(dlon / 2));
            double angle = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            return angle * EarthRadius;
        }
    ```

4. 现在，必须将这些概念关联起来。 但是，我们的教程中讲到了这些代码片段，而构建基于映射的应用这一操作留给读者做练习。 要进一步了解此示例，请考虑输入一个带半径的城市名，或者在地图上找一个点，然后选择一个半径。 要进一步探讨这些选项，请参阅以下资源：

* [Azure Maps 文档](https://docs.microsoft.com/azure/azure-maps/)
* [使用 Azure Maps 搜索服务查找地址](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>根据计分概要文件对结果排序

本教程中至今为止提供的示例展示了如何根据数值（评级、翻新日期和地理距离）进行排序，提供了确切的排序过程  。 但是，部分搜索和一些数据不会使自身实现两个数据元素之间的此类简单比较。 Azure 认知搜索包含了评分的概念  。 可针对一组数据指定计分概要文件，这些数据可用于提供更复杂和更定性的比较，在比较基于文本的数据来决定应显示哪项内容等情况下，这最具价值  。

计分概要文件不是由用户定义的，而通常是数据集的管理员定义的。 已针对酒店数据设置了多个计分概要文件。 让我们看看如何定义计分概要文件，然后试着编写代码来搜索这些文件。

### <a name="how-scoring-profiles-are-defined"></a>如何定义计分概要文件

我们来看一下计分概要文件的三个示例，考虑每个示例应如何影响结果顺序  。 身为应用开发人员，你并不会编写这些概要文件，它们是由数据管理员编写的，但这有助于你理解语法。

1. 这是酒店数据集的默认计分概要文件，你在不指定任何 OrderBy 或 ScoringProfile 参数时使用它   。 如果酒店名称、说明和标记列表（便利设施）中出现搜索文本，此概要文件可提高酒店的得分  。 请注意，评分的权重如何偏向某些字段。 如果搜索文本出现在未下述所列的另一字段中，则它的权重为 1。 很明显，分数越高，结果在视图中显示的时间越早。

     ```cs
    {
            "name": "boostByField",
            "text": {
                "weights": {
                    "HotelName": 2,
                    "Description": 1.5,
                    "Description_fr": 1.5,
                    "Tags": 3
                }
            }
        }

    ```

2. 如果提供的参数包含一个或多个标记列表（我们称为“便利设施”），以下计分概要文件可大幅提到分数。 此概要文件的要点是，必须提供参数且参数带有文本  。 如果未提供参数或参数为空，则将引发错误。
 
    ```cs
            {
            "name": "boostAmenities",
            "functions": [
                {
                    "type": "tag",
                    "fieldName": "Tags",
                    "boost": 5,
                    "tag": {
                        "tagsParameter": "amenities"
                    }
                }
            ]
        }
    ```

3. 在第三个示例中，评级可大幅提高分数。 上次翻新日期也将提高分数，但仅在该数据位于当前日期的 730 天（2 年）范围内才有效。

    ```cs
            {
            "name": "renovatedAndHighlyRated",
            "functions": [
                {
                    "type": "magnitude",
                    "fieldName": "Rating",
                    "boost": 20,
                    "interpolation": "linear",
                    "magnitude": {
                        "boostingRangeStart": 0,
                        "boostingRangeEnd": 5,
                        "constantBoostBeyondRange": false
                    }
                },
                {
                    "type": "freshness",
                    "fieldName": "LastRenovationDate",
                    "boost": 10,
                    "interpolation": "quadratic",
                    "freshness": {
                        "boostingDuration": "P730D"
                    }
                }
            ]
        }

    ```

    接下来，看看这些概要文件是否按我们所预想的发挥作用！

### <a name="add-code-to-the-view-to-compare-profiles"></a>向视图添加代码来比较概要文件

1. 打开 index.cshtml 文件，将 &lt;body&gt; 部分替换为以下代码。

    ```cs
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

2. 打开 SearchData.cs 文件，将 SearchData 类替换为以下代码  。

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

3. 打开 hotels.css 文件并添加以下 HTML 类。

    ```html
    .facetlist {
        list-style: none;
    }
    
    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>向控制器添加代码来指定计分概要文件

1. 打开主控制器文件。 添加以下 using 语句来帮助创建列表  。

    ```cs
    using System.Linq;
    ```

2.  在本例中，我们需要首次调用 Index，使其不仅返回初始视图  。 此方法现最多搜索 20 个要在视图中显示的便利设施。

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 amenities.
                Facets = new List<string> { "Tags,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

3. 我们需要两个专用方法，以将分面保存到临时存储，同时从临时存储中返回这些分面并填充模型。

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

4. 我们需要根据需要设置 OrderBy 和 ScoringProfile 参数   。 将现有 Index(SearchData model) 方法替换为以下内容  。

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Recover the facet text, and the facet check box settings.
                    RecoverFacets(model, true);

                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First search with text. 
                    // Recover the facet text, but ignore the check box settings, and use the current model settings.
                    RecoverFacets(model,false);

                    // First call. Check for valid text input, and valid scoring profile.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    if (model.scoring == null)
                    {
                        model.scoring = "Default";
                    }
                    page = 0;
                }

                // Set empty defaults for ordering and scoring parameters.
                var orderby = new List<string>();
                string profile = "";
                var scoringParams = new List<ScoringParameter>();

                // Set the ordering based on the user's radio button selection.
                switch (model.scoring)
                {
                    case "RatingRenovation":
                        orderby.Add("Rating desc");
                        orderby.Add("LastRenovationDate desc");
                        break;

                    case "boostAmenities":
                        {
                            profile = model.scoring;
                            var setAmenities = new List<string>();

                            // Create a string list of amenities that have been clicked.
                            for (int a = 0; a < model.facetOn.Length; a++)
                            {
                                if (model.facetOn[a])
                                {
                                    setAmenities.Add(model.facetText[a]);
                                }
                            }
                            if (setAmenities.Count > 0)
                            {
                                // Only set scoring parameters if there are any.
                                var sp = new ScoringParameter("amenities", setAmenities);
                                scoringParams.Add(sp);
                            }
                            else
                            {
                                // No amenities selected, so set profile back to default.
                                profile = "";
                            }
                        }
                        break;

                    case "renovatedAndHighlyRated":
                        profile = model.scoring;
                        break;

                    default:
                        break;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Set the ordering/scoring parameters.
                    OrderBy = orderby,
                    ScoringProfile = profile,
                    ScoringParameters = scoringParams,

                    // Select the data properties to be returned.
                    Select = new[] { "HotelName", "Description", "Tags", "Rooms", "Rating", "LastRenovationDate" },
                    SearchMode = SearchMode.All,

                    // Skip past results that have already been returned.
                    Skip = page * GlobalVariables.ResultsPerPage,

                    // Take only the next page worth of results.
                    Top = GlobalVariables.ResultsPerPage,

                    // Include the total number of results.
                    IncludeTotalResultCount = true,
                };

                // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
                model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
                TempData["scoring"] = model.scoring;
                SaveFacets(model,true);

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    浏览每个 switch 选择项的注释  。

5. 如果你已为上一部分（“根据多个属性排序”）添加了代码，则我们不需要对 Next 操作进行任何更改  。

### <a name="run-and-test-the-app"></a>运行并测试应用

1. 运行应用。 你应会在视图中看到一组完整的便利设施。

2. 对于排序，如果选择“按数值评级”，将提供已在本教程中实现的数值排序，其中要在评级相同的酒店之间决定翻新日期。

![根据评级对“沙滩”进行排序](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. 接下来，尝试“按便利设施”概要文件。 进行各种便利设施选择，并验证配有这些便利设施的酒店在结果列表中的排名是否上升了。

![根据概要文件对“沙滩”进行排序](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. 请尝试“按翻新日期/评级概要文件”，查看是否获得所期望的结果。 只有近期翻新的酒店的新鲜度会提升  。

### <a name="resources"></a>资源

有关详细信息，请参阅下面的[将计分概要文件添加到 Azure 认知搜索索引](https://docs.microsoft.com/azure/search/index-add-scoring-profiles)。

## <a name="takeaways"></a>要点

请考虑此项目中的以下要点：

* 用户希望对搜索结果进行排序且最先排列最相关的结果。
* 需要实现数据结构化，使排序简单起来。 我们未能轻松地最先排列“最低房价”，因为数据未结构化，不添加代码就没法进行排序。
* 可能有很多排序级别，按更高级别的排序在具有相同值的结果之间进行区分。
* 自认而然地，一些结果会按升序排序（例如到某个点的距离），一些结果按降序排序（例如房客的评级）。
* 可在数值比较对数据集不可用或不够智能时定义计分概要文件。 通过对每个结果评分，有助于按智能方式进行结果排序和显示。

## <a name="next-steps"></a>后续步骤

你已学完本系列的 C# 教程，应该已经找到了有关 Azure 认知搜索 API 的宝贵知识。

有关进一步的参考和教程，请考虑浏览 [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure) 或 [Azure 认知搜索文档](https://docs.microsoft.com/azure/search/)中的其他教程。
