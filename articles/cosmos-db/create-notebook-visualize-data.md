---
title: 教程：在 Azure Cosmos DB 中创建笔记本用于分析和可视化数据
description: 教程：了解如何使用内置的 Jupyter 笔记本将数据导入到 Azure Cosmos DB、分析数据和可视化输出。
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 45dd4e8dcfd74cdb5d96b935e239b9f4b5094a7c
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720927"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>教程：在 Azure Cosmos DB 中创建笔记本用于分析和可视化数据

本文介绍如何使用内置的 Jupyter 笔记本将示例零售数据导入 Azure Cosmos DB。 其中将会讲解如何使用 SQL 和 Azure Cosmos DB magic 命令来运行查询、分析数据和可视化结果。

## <a name="prerequisites"></a>先决条件

* [创建 Azure Cosmos 帐户时启用笔记本支持](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>创建资源并导入数据
 
在本部分，你将创建 Azure Cosmos 数据库和容器，并将零售数据导入该容器。

1. 导航到你的 Azure Cosmos 帐户，打开“数据资源管理器”。 

1. 转到“笔记本”选项卡，选择“我的笔记本”旁边的 `…`，然后创建一个**新笔记本**。   选择“Python 3”作为默认内核。 

   ![创建新的 Notebook](./media/create-notebook-visualize-data/create-new-notebook.png)

1. 创建新笔记本后，可将其重命名，例如 **VisualizeRetailData.ipynb**。

1. 接下来，创建名为“RetailDemo”的数据库，并创建名为“WebsiteData”的容器来存储零售数据。 可以使用 /CardID 作为分区键。 将以下代码复制并粘贴到笔记本的新单元中，然后运行此代码：

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   若要运行某个单元，请选择 `Shift + Enter`，或者选择该单元，然后选择数据资源管理器导航栏上的“运行活动单元”选项。 

   ![运行活动单元](./media/create-notebook-visualize-data/run-active-cell.png)

   将在当前的 Azure Cosmos 帐户中创建数据库和容器。 该容器的预配吞吐量为 400 RU/秒。 创建数据库和容器后，会显示以下输出。 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   也可以刷新“数据”选项卡并查看新建的资源： 

   ![刷新“数据”选项卡以查看新容器](media/create-notebook-visualize-data/refresh-data-tab.png)

1. 接下来，将示例零售数据导入 Azure Cosmos 容器。 下面是零售数据中的项的格式：

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   本教程中的示例零售数据存储在 Azure Blob 存储中。 可以通过将以下代码粘贴到新单元，将此数据导入 Azure Cosmos 容器。 可以通过运行一个选择项数的查询，来确认是否已成功导入数据。

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   运行上述查询时，它会返回以下输出：

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>将数据读入数据帧

在运行分析数据的查询之前，可将数据从容器读入到 [Pandas 数据帧](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html)进行分析。 使用以下 SQL magic 命令将数据读入数据帧：

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

有关详细信息，请参阅 [Azure Cosmos DB 中的内置笔记本命令和功能](use-notebook-features-and-commands.md)一文。 将运行查询 - `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c`。 结果将保存到名为 df_cosmos 的 Pandas 数据帧中。 将以下命令粘贴到新的笔记本单元，然后运行此命令：

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

在新的笔记本单元中，运行以下代码读取输出中的前 10 个项：

```python
# See a sample of the result
df_cosmos.head(10)
```

![运行查询获取前 10 个项](./media/create-notebook-visualize-data/run-query-get-top10-items.png)

## <a name="run-queries-and-analyze-your-data"></a>运行查询并分析数据

在本部分，你将对检索到的数据运行一些查询。

* **查询 1：** 对数据帧运行 Group by 查询，以获取在每个国家/地区的总销售收入之和，并显示结果中的 5 个项。 在新的笔记本单元中运行以下代码：

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   ![总销售收入输出](./media/create-notebook-visualize-data/total-sales-revenue-output.png)

* **查询 2**：若要获取前五个购买项的列表，请打开新的笔记本单元并运行以下代码：

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   ![前五个购买项](./media/create-notebook-visualize-data/top5-purchased-items.png)

## <a name="visualize-your-data"></a>可视化数据  

1. 从 Azure Cosmos 容器中获取有关收入的数据后，可以使用所选的可视化库将数据可视化。 本教程将使用 Bokeh 库。 打开新的笔记本单元，运行以下代码以安装 Bokeh 库。 满足所有要求后，将安装该库。

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. 接下来，准备在地图上绘制数据。 将 Azure Cosmos DB 中的数据联接到位于 Azure Blob 存储中的国家/地区信息，并将结果转换为 GeoJSON 格式。 将以下代码复制到新的笔记本单元，然后运行此代码。

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries dataframe with our data in Azure Cosmos DB, joining on country code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. 通过在新笔记本单元中运行以下代码，在世界地图上可视化不同国家/地区的销售收入：

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   输出将以不同的颜色显示在世界地图上。 颜色越深，表示所在国家/地区的收入越高。

   ![地图上所在国家/地区的收入数据可视化](./media/create-notebook-visualize-data/countries-revenue-map-visualization.png)

1. 让我们看看另一个数据可视化用例。 WebsiteData 容器包含查看了某个项、将该项添加到了购物车以及购买了该项的用户记录。 让我们绘制购买项的转换率。 在新的单元中运行以下代码，以可视化每个项的转换率：

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   ![可视化购买转换率](./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png)

## <a name="next-steps"></a>后续步骤

* 若要详细了解笔记本命令，请参阅[如何使用 Azure Cosmos DB 中的内置笔记本命令和功能](use-notebook-features-and-commands.md)一文。
