---
title: 快速入门：Python 和 REST API - Azure 搜索
description: 使用 Python、Jupyter Notebooks 和 Azure 搜索 REST API 创建、加载和查询索引。
ms.date: 06/11/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: c519cbd151ac3008593e3309930db4e9a9414e51
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056659"
---
# <a name="quickstart-create-an-azure-search-index-using-jupyter-python-notebooks"></a>快速入门：使用 Jupyter Python 笔记本创建 Azure 搜索索引
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [门户](search-create-index-portal.md)
> 

生成的创建、 加载和查询 Azure 搜索索引中使用 Python 的 Jupyter 笔记本并[Azure 搜索 REST Api](https://docs.microsoft.com/rest/api/searchservice/)。 本文介绍如何生成步骤的步骤中，从零开始的笔记本。 或者，可以运行已完成的笔记本。 若要下载副本，请转到 [Azure-Search-python-samples 存储库](https://github.com/Azure-Samples/azure-search-python-samples)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

本快速入门使用以下服务和工具。 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section)，提供 Python 3.x 和 Jupyter Notebooks。

+ [创建 Azure 搜索服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 对于本快速入门，可以使用免费层。 

## <a name="get-a-key-and-url"></a>获取密钥和 URL

REST 调用需要在每个请求中使用服务 URL 和访问密钥。 搜索服务是使用这二者创建的，因此，如果向订阅添加了 Azure 搜索，则请按以下步骤获取必需信息：

1. [登录到 Azure 门户](https://portal.azure.com/)，在搜索服务的“概述”页中获取 URL。  示例终结点可能类似于 `https://mydemo.search.windows.net`。

1. 在“设置” > “密钥”中，获取有关该服务的完全权限的管理员密钥   。 有两个可交换的管理员密钥，为保证业务连续性而提供，以防需要滚动一个密钥。 可以在请求中使用主要或辅助密钥来添加、修改和删除对象。

![获取 HTTP 终结点和访问密钥](media/search-fiddler/get-url-key.png "Get an HTTP endpoint and access key")

所有请求对发送到服务的每个请求都需要 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

## <a name="connect-to-azure-search"></a>连接到 Azure 搜索

在此任务中，启动 Jupyter notebook 并验证可以连接到 Azure 搜索。 将从你的服务请求的索引列表来执行此操作。 在装有 Anaconda3 的 Windows 上，可以使用 Anaconda Navigator 来启动笔记本。

1. 创建新的 Python3 笔记本。

1. 在第一个单元格中，加载用于处理 JSON 和构建 HTTP 请求的库。

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. 在第二个单元格中，输入在每个请求时都用作常量的请求元素。 将搜索服务名称 (YOUR-SEARCH-SERVICE-NAME) 和管理 API 密钥 (YOUR-ADMIN-API-KEY) 替换为有效的值。 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

1. 在第三个单元格中构建请求。 此 GET 请求以搜索服务的索引集合为目标，并选择现有的索引的名称属性。

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. 运行每个步骤。 如果存在索引，则响应将包含索引名称的列表。 在下面的屏幕截图，该服务已经有 azureblob 索引和 realestate-我们的示例索引。

   ![Jupyter 笔记本中的 Python 脚本，该脚本包含对 Azure 搜索发出的 HTTP 请求](media/search-get-started-python/connect-azure-search.png "Jupyter 笔记本中的 Python 脚本，该脚本包含对 Azure 搜索发出的 HTTP 请求")

   与此相反，空索引集合返回此响应： `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 - 创建索引

除非使用门户，否则在加载数据之前，服务中必须存在一个索引。 此步骤中使用[创建索引 REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)推送到该服务的索引架构。

所需的元素的索引包括名称、 字段集合和一个密钥。 字段集合定义文档的结构。  每个字段有一个名称、 类型和确定如何使用该字段的属性 (例如，是否是全文索引时才可搜索、 筛选或可在搜索结果中检索)。 在索引中，必须将一个 `Edm.String` 类型的字段指定为文档标识的键。 

此索引名为"hotels 快速入门"，并且请参阅下面的字段定义。 它是其他演练中使用的一个更大 [Hotels 索引](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON)的子集。 为简明起见，本快速入门已对其进行修整。

1. 在下一个单元格中，将以下示例粘贴到某个单元格以提供架构。 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. 在另一个单元格中构建请求。 该 PUT 请求以搜索服务的索引集合为目标，并创建基于在上一个单元格中提供的索引架构的索引。

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. 运行每个步骤。

   响应包含架构的 JSON 表示形式。 下面的屏幕截图显示响应的一部分。

    ![请求创建索引](media/search-get-started-python/create-index.png "请求创建索引")

> [!Tip]
> 若要验证创建索引的另一种方法是检查在门户中的索引列表。

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 加载文档

若要推送文档，请向索引的 URL 终结点发出 HTTP POST 请求。 REST API 为[添加、更新或删除文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)。 文档源自于 GitHub 上的 [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON)。

1. 在新的单元格，提供了四个符合索引架构的文档。 指定每个文档的上传操作。

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    ```   

2. 在另一个单元格中构建请求。 此 POST 请求以快速入门的酒店索引的文档集合为目标，并将推送上一步中提供的文档。

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. 运行每个步骤，将文档推送到搜索服务中的索引。 结果应类似于以下示例。 

    ![将文档发送到索引](media/search-get-started-python/load-index.png "将文档发送到索引")

## <a name="3---search-an-index"></a>3 - 搜索索引

此步骤说明如何使用[搜索文档 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 查询索引。

1. 在某一单元，提供了执行空搜索的查询表达式 (搜索 = *)，返回 unranked 的列表 (搜索分数 = 1.0) 的任意文档。 默认情况下，Azure 搜索一次返回 50 个匹配项。 作为结构化，此查询将返回整个文档结构和值。 添加 $count = true，以在结果中获取所有文档的计数。

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. 在新的单元格提供下面的示例，若要搜索的术语"酒店"和"wifi"。 添加 $select 可指定要在搜索结果中包括哪些字段。

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. 在另一个单元格中，明确表述请求。 此 GET 请求以快速入门的酒店索引的文档集合为目标，并将附加上一步中指定的查询。

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. 运行每个步骤。 结果应类似于以下输出。 

    ![搜索索引](media/search-get-started-python/search-index.png "搜索索引")

1. 请尝试其他几个查询示例，以大致了解语法。 您可以 searchstring 替换为下面的示例，然后重新运行搜索请求。 

   应用筛选器： 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   执行前两个结果：

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    按特定字段进行排序：

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>清理 

应该删除不再需要的索引。 在免费服务中最多只能创建三个索引。 应删除任何不主动使用为其他教程留出空间的索引。

若要删除的对象的最简单方法是通过门户中，但由于这是 Python 快速入门，以下语法将生成相同的结果：

   ```python
  url = endpoint + "indexes/hotels-quickstart" + api_version
  response  = requests.delete(url, headers=headers)
   ```

可以通过向现有索引的列表来验证索引删除。 如果酒店-快速入门将消失，就会知道您已成功的请求。

```python
url = endpoint + "indexes" + api_version + "&$select=name"

response  = requests.get(url, headers=headers)
index_list = response.json()
pprint(index_list)
```

## <a name="next-steps"></a>后续步骤

为简化本快速入门使用的 Hotels 索引的简化的版本。 可以创建要尝试更感兴趣的查询的完整版本。 若要获取完整版本和所有 50 个文档，请运行**导入数据**向导，并选择*酒店示例*从内置的示例数据源。

> [!div class="nextstepaction"]
> [快速入门：在 Azure 门户中创建索引](search-get-started-portal.md)
