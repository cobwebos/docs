---
title: Python 快速入门：使用 REST API 创建、加载和查询索引 - Azure 搜索
description: 介绍如何使用 Python、Jupyter Notebooks 和 Azure 搜索 REST API 创建索引、加载数据以及运行查询。
ms.date: 08/28/2019
author: heidisteen
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.openlocfilehash: 352d26a1f3e781f7dbb6e502caea44bdb45b398a
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129403"
---
# <a name="quickstart-create-an-azure-search-index-in-python-using-jupyter-notebooks"></a>快速入门：在 Python 中使用 Jupyter Notebooks 创建 Azure 搜索索引
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-get-started-postman.md)
> * [门户](search-create-index-portal.md)
> 

使用 Python 和 [Azure 搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/) 生成可创建、加载和查询 Azure 搜索索引的 Jupyter 笔记本。 本文介绍如何逐步生成笔记本。 你也可以[下载并运行一个已完成的 Jupyter Python 笔记本](https://github.com/Azure-Samples/azure-search-python-samples)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

本快速入门需要以下服务和工具。 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section)，提供 Python 3.x 和 Jupyter Notebook。

+ [创建 Azure 搜索服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 对于本快速入门，可以使用免费层。 

## <a name="get-a-key-and-url"></a>获取密钥和 URL

REST 调用需要在每个请求中使用服务 URL 和访问密钥。 搜索服务是使用这二者创建的，因此，如果向订阅添加了 Azure 搜索，则请按以下步骤获取必需信息：

1. [登录到 Azure 门户](https://portal.azure.com/)，在搜索服务的“概述”页中获取 URL。  示例终结点可能类似于 `https://mydemo.search.windows.net`。

1. 在“设置” > “密钥”中，获取有关该服务的完全权限的管理员密钥   。 有两个可交换的管理员密钥，为保证业务连续性而提供，以防需要滚动一个密钥。 可以在请求中使用主要或辅助密钥来添加、修改和删除对象。

![获取 HTTP 终结点和访问密钥](media/search-get-started-postman/get-url-key.png "Get an HTTP endpoint and access key")

所有请求对发送到服务的每个请求都需要 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

## <a name="connect-to-azure-search"></a>连接到 Azure 搜索

在此任务中，请启动一个 Jupyter 笔记本并验证是否可以连接到 Azure 搜索。 为此，你将从服务请求索引列表。 在装有 Anaconda3 的 Windows 上，可以使用 Anaconda Navigator 来启动笔记本。

1. 创建新的 Python3 笔记本。

1. 在第一个单元格中，加载用于处理 JSON 和构建 HTTP 请求的库。

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. 在第二个单元格中，输入用作每个请求中的常量的请求元素。 将搜索服务名称 (YOUR-SEARCH-SERVICE-NAME) 和管理员 API 密钥 (YOUR-ADMIN-API-KEY) 替换为有效值。 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   如果收到 ConnectionError `"Failed to establish a new connection"`，请验证 api-key 是主管理密钥还是辅助管理密钥，以及所有前导和尾随字符（`?` 和 `/`）是否已到位。

1. 在第三个单元格中构建请求。 此 GET 请求针对搜索服务的索引集合，并选择现有索引的名称属性。

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. 运行每个步骤。 如果索引存在，则响应将包含索引名称列表。 在以下屏幕截图中，服务已有 azureblob-index 和 realestate-us-sample 索引。

   ![Jupyter 笔记本中的 Python 脚本，其中包含对 Azure 搜索的 HTTP 请求](media/search-get-started-python/connect-azure-search.png "Jupyter 笔记本中的 Python 脚本，其中包含对 Azure 搜索的 HTTP 请求")

   相比之下，空索引集合返回以下响应：`{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 - 创建索引

除非使用门户，服务中必须预先存在一个索引才能加载数据。 此步骤使用[创建索引 REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) 向服务推送索引架构。

索引的所需元素包括名称、字段集合和键。 字段集合定义文档的结构。  每个字段具有一个确定其用法的名称、类型和属性（例如，该字段在搜索结果是否可全文搜索、可筛选或可检索）。 在索引中，必须将一个 `Edm.String` 类型的字段指定为文档标识的键。 

此索引名为“hotels-quickstart”，使用下面所示的字段定义。 它是其他演练中使用的一个更大 [Hotels 索引](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON)的子集。 为简明起见，本快速入门已对其进行修整。

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

2. 在另一个单元格中构建请求。 此 PUT 请求针对搜索服务的索引集合，根据在上一单元格中提供的索引架构创建索引。

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. 运行每个步骤。

   响应包含架构的 JSON 表示形式。 以下屏幕截图只显示了响应的一部分。

    ![请求创建索引](media/search-get-started-python/create-index.png "请求创建索引")

> [!Tip]
> 验证索引创建结果的另一种方法是在门户中检查“索引”列表。

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 加载文档

若要推送文档，请向索引的 URL 终结点发出 HTTP POST 请求。 REST API 为[添加、更新或删除文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)。 文档源自 GitHub 上的 [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON)。

1. 在新单元格中，提供符合索引架构的四个文档。 指定每个文档的上传操作。

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

2. 在另一个单元格中构建请求。 此 POST 请求针对 hotels-quickstart 索引的文档集合，将推送在上一步骤中提供的文档。

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. 运行每个步骤，将文档推送到搜索服务中的索引。 结果应如以下示例所示。 

    ![将文档发送到索引](media/search-get-started-python/load-index.png "将文档发送到索引")

## <a name="3---search-an-index"></a>3 - 搜索索引

此步骤说明如何使用[搜索文档 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 查询索引。

1. 在单元格中提供一个查询表达式，用于执行空搜索 (search=*)，并返回任意文档的未排名列表 (search score = 1.0)。 默认情况下，Azure 搜索每次返回 50 个匹配项。 由于已结构化，此查询将返回整个文档结构和值。 添加 $count=true 以获取结果中所有文档的计数。

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. 在新单元格中提供以下示例，以根据字词“hotels”和“wifi”执行搜索。 添加 $select 以指定要包含在搜索结果中的字段。

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. 在另一个单元格中构建请求。 此 GET 请求针对 hotels-quickstart 索引的文档集合，将附加在上一步骤中指定的查询。

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. 运行每个步骤。 结果应如以下输出所示。 

    ![搜索索引](media/search-get-started-python/search-index.png "搜索索引")

1. 尝试其他查询示例来了解语法。 可将 `searchstring` 替换为以下示例，然后重新运行搜索请求。 

   应用筛选器： 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   采用前两个结果：

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    按特定的字段排序：

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>清理

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接   ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。 

## <a name="next-steps"></a>后续步骤

为简单起见，本快速入门使用了 Hotels 索引的缩写版本。 你可以创建完整的版本，以尝试进行更有意思的查询。 若要获取完整版本和所有 50 个文档，请运行“导入数据”向导，并从内置的示例数据源中选择“hotels-sample”。  

> [!div class="nextstepaction"]
> [快速入门：在 Azure 门户中创建索引](search-get-started-portal.md)
