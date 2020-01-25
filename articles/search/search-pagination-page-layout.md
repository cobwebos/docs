---
title: 如何处理搜索结果
titleSuffix: Azure Cognitive Search
description: 对搜索结果进行结构和排序、获取文档计数，并向 Azure 认知搜索中的搜索结果添加内容导航。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: c32e58a43b5409fd9f8ede536167d185270c6a22
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721568"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>如何在 Azure 认知搜索中使用搜索结果
이 문서에서는 총 개수, 문서 검색, 정렬 순서 및 탐색과 같은 검색 결과 페이지의 표준 요소를 구현하는 방법에 대한 지침을 제공합니다. 向你的搜索结果提供数据或信息的页面相关选项通过发送到 Azure 认知搜索服务的[搜索文档](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)请求来指定。 

REST API의 요청에는 GET 명령, 경로 및 서비스에 필요한 것과 응답을 작성하는 방법을 서비스에 알려주는 쿼리 매개 변수가 포함됩니다. .NET SDK에서 해당하는 API는 [DocumentSearchResult Class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1)입니다.

若要快速为客户端生成搜索页面，请浏览以下选项：

+ 使用门户中的[应用程序生成器](search-create-app-portal.md)创建一个具有搜索栏、分面导航和结果区域的 HTML 页面。
+ 请按照[创建第一个应用教程C#中](tutorial-csharp-create-first-app.md)的步骤创建功能客户端。

几个代码示例包含一个 web 前端接口，可在此处找到：纽约的 "[城市作业" 演示应用](https://azjobsdemo.azurewebsites.net/)、[使用实时演示网站的 JavaScript 示例代码](https://github.com/liamca/azure-search-javascript-samples)和[CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd)。

> [!NOTE]
> 유효한 요청에는 서비스 URL 및 경로, HTTP 동사, `api-version` 등과 같은 요소의 숫자가 포함됩니다. 요약하자면, 페이지 매김에 관련된 구문만 강조하기 위해 예제를 잘라냈습니다. 有关请求语法的详细信息，请参阅[Azure 认知搜索 REST api](https://docs.microsoft.com/rest/api/searchservice)。
>

## <a name="total-hits-and-page-counts"></a>총 적중 수 및 페이지 수

쿼리에서 반환된 결과의 총 수를 표시한 후 해당 결과를 더 작은 청크로 반환하는 것은 모든 검색 페이지의 기반이 됩니다.

![][1]

在 Azure 认知搜索中，使用 `$count`、`$top`和 `$skip` 参数返回这些值。 下面的示例演示一个名为 "联机目录" 的索引上的命中总数示例请求，并返回 `@odata.count`：

    GET /indexes/online-catalog/docs?$count=true

첫 페이지에서 시작하여 15개 그룹에 대한 문서를 검색하고, 총 적중 수도 표시합니다.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

페이지 매김 결과에는 `$top`이(가) 일괄적으로 반환할 항목의 수를 지정하고 `$skip`은(는) 건너뛸 항목 수를 지정하는 `$top` 및 `$skip`이(가) 모두 필요합니다. 다음 예제에서는 각 페이지에서 `$skip` 매개 변수로 증분 점프하여 증가하는 다음 15개 항목을 보여줍니다.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Layout

검색 결과 페이지에서 축소판 이미지, 필드의 하위 집합 및 전체 제품 페이지에 대한 링크를 표시할 수 있습니다.

 ![][2]

在 Azure 认知搜索中，你将使用 `$select` 和[搜索 API 请求](https://docs.microsoft.com/rest/api/searchservice/search-documents)来实现这种体验。

타일화된 레이아웃에 대한 필드의 하위 집합을 반환하는 방법:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

이미지 및 미디어 파일은 직접 검색할 수 없으며, 비용을 줄이기위해 Azure Blob Storage와 같은 다른 스토리지 플랫폼에 저장해야 합니다. 인덱스 및 문서에서 외부 콘텐츠의 URL 주소를 저장하는 필드를 정의합니다. 그러면 필드를 이미지 참조로 사용할 수 있습니다. 이미지에 대한 URL은 문서에 있어야 합니다.

**onClick** 이벤트에 대한 제품 설명 페이지를 검색하려면 [문서 조회](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) 를 사용하여 검색할 문서의 키에 전달합니다. 키의 데이터 형식은 `Edm.String`입니다. 이 예제에서는 *246810*입니다.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>관련성, 등급, 또는 가격 기준으로 정렬

정렬 순서는 종종 기본적으로 관련도에 따르지만, 고객이 다른 순위 순서로 기존 결과를 신속하게 바꿀 수 있도록 대체 정렬 순서를 준비해 놓는 것이 일반적입니다.

 ![][3]

在 Azure 认知搜索中，排序基于 `$orderby` 表达式，对于索引为 `"Sortable": true.` `$orderby` 子句的所有字段，其为 OData 表达式。 구문에 대한 자세한 내용은 [필터 및 order-by 절의 OData 식 구문](query-odata-filter-orderby-syntax.md)을 참조하세요.

관련성은 프로필 점수 매기기와 강력하게 연관됩니다. 검색 단어가 더 많이 또는 더 강력하게 일치되는 문서에 더 높은 점수를 매기는 텍스트 분석 및 통계에 따라 모든 결과의 순서를 정하는 기본 점수를 사용할 수 있습니다.

대체 정렬 순서는 일반적으로 정렬 순서를 작성하는 메서드를 다시 호출하는 **onClick** 이벤트에 관련이 있습니다. 예를 들어 이 페이지 요소를 가정합니다.

 ![][4]

선택한 정렬 옵션을 입력으로 받아 메서드를 생성하고 해당 옵션과 관련된 조건에 대한 정렬된 목록을 반환할 수 있습니다.

 ![][5]

> [!NOTE]
> 기본 점수 매기기는 다양한 시나리오에 적용할 수 있으므로 대신 사용자 지정 점수 매기기 프로필의 관련성에 기반하는 것이 좋습니다. 사용자 지정 점수 매기기 프로필은 비즈니스에 더 많은 이점을 제공하는 항목 강화 방법을 제공합니다. 자세한 내용은 [점수 매기기 프로필 추가](index-add-scoring-profiles.md)를 참조하세요.
>

## <a name="hit-highlighting"></a>적중 항목 강조 표시

您可以在搜索结果中将格式应用于匹配的字词，这样就可以轻松地找到匹配项。 [查询请求](https://docs.microsoft.com/rest/api/searchservice/search-documents)上提供了命中突出显示说明。 

格式设置将应用于整个术语查询。 对于在引擎中导致查询扩展的部分术语（如模糊搜索或通配符搜索）的查询，不能使用命中突出显示。

```http
POST /indexes/hotels/docs/search?api-version=2019-05-06 
    {  
      "search": "something",  
      "highlight": "Description"  
    }
```



## <a name="faceted-navigation"></a>패싯 탐색

종종 페이지의 옆쪽 또는 위쪽에 있는 검색 탐색은 결과 페이지에서 일반적입니다. 在 Azure 认知搜索中，分面导航提供基于预定义筛选器的自定向搜索。 有关详细信息，请参阅[Azure 中的分面导航认知搜索](search-faceted-navigation.md)。

## <a name="filters-at-the-page-level"></a>페이지 수준의 필터

如果你的解决方案设计包含特定类型的内容（例如，页面顶部列出了部门的联机零售应用程序）的专用搜索页面，则可以插入[筛选表达式](search-filters.md)和**onClick**事件，以在预筛选状态下打开页面。

검색 식의 사용 여부에 관계 없이 필터를 보낼 수 있습니다. 예를 들어 다음 요청은 브랜드 이름으로 필터링하고 일치하는 문서만 반환합니다.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

有关 `$filter` 表达式的详细信息，请参阅[搜索文档（Azure 认知搜索 API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 。

## <a name="see-also"></a>참고 항목

- [Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice)
- [인덱스 작업](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [문서 작업](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Azure 认知搜索中的分面导航](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
