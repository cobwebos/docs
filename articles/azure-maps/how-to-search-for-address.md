---
title: 使用搜索服务的 Azure Maps 搜索位置 |Microsoft Azure 映射
description: 在本文中，你将了解如何使用 Microsoft Azure Map 搜索服务搜索位置。
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 20a2c18875096680cd1eba7601e88965fcbcc568
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715352"
---
# <a name="using-azure-maps-search-services-for-geocoding-and-reverse-geocoding"></a>使用 Azure Maps Search services 进行地理编码和反向地理编码

Azure Maps[搜索服务](https://docs.microsoft.com/rest/api/maps/search)是一组 RESTful api，旨在帮助开发人员按名称或类别搜索地址、位置、业务列表和其他地理信息。 除了支持传统的地理编码，服务还可以根据纬度和经度来反转地理编码地址和交叉街道。 搜索返回的纬度和经度值可用作其他 Azure Maps 服务（如[路由](https://docs.microsoft.com/rest/api/maps/route)和[天气](https://docs.microsoft.com/rest/api/maps/weather)服务）中的参数。

我们来了解，如何：

* 使用[搜索地址 API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)请求地址的纬度和经度坐标（地理编码地址位置）
* 使用[模糊搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)搜索地址或兴趣点（POI）
* 搜索地址以及属性和坐标
* 进行[反向地址搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)，将坐标位置转换为街道地址
* 使用[搜索地址反向交叉街道 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)搜索交叉街道

## <a name="prerequisites"></a>필수 조건

若要完成本文中的步骤，你需要先创建一个 Azure Maps 帐户，并向你显示 Maps 帐户订阅密钥。 按照[创建帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)中的说明创建 Azure Maps 帐户订阅，并按照[获取主密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步骤获取帐户的主密钥。 有关 Azure Maps 中的身份验证的详细信息，请参阅[Azure Maps 中的管理身份验证](./how-to-manage-authentication.md)。

이 문서에서는 [우체부 앱](https://www.getpostman.com/apps)을 사용하여 REST 호출을 빌드합니다. 선호하는 모든 API 개발 환경을 사용할 수 있습니다.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>请求地址的纬度和经度（地理编码）

在此示例中，我们使用 Azure Maps[获取搜索地址 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)将街道地址转换为纬度和经度坐标。 可以将完整或部分街道地址传递到 API，并接收包含详细地址属性（如街道、邮政编码和国家/地区）以及纬度和经度中的位置值的响应。

如果你有一组要地理编码的地址，则可以使用[Post Search 地址批处理 API](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch)通过单个 API 调用发送一批查询。

1. Postman에서 **새 요청** | **GET 요청**을 클릭하고 이름을 **주소 검색**으로 지정합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 엔드포인트에 대한 요청 URL을 입력한 다음 권한 부여 프로토콜이 있는 경우 해당 프로토콜을 선택합니다.

![주소 검색](./media/how-to-search-for-address/address_search_url.png)

| 매개 변수 | 제안 값 |
|---------------|------------------------------------------------| 
| HTTP 메서드 | GET |
| 요청 URL | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| 권한 부여 | 인증 없음 |

3. **Params**를 클릭하고 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 다음 키 / 값 쌍을 입력합니다. 

![주소 검색](./media/how-to-search-for-address/address_search_params.png) 

| 키 | 값 | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| 구독 키 | \<사용자의 Azure 맵 키\> | 
| Query | 400 한 광범위 한 St, 시애틀, WA 98109 | 

4. **보내기**를 클릭하고 응답 본문을 검토합니다. 

이 경우 전체 주소 쿼리를 지정했으며 응답 본문에 단일 결과를 받습니다. 

5. 매개 변수에 다음 값에 대한 쿼리 문자열을 편집합니다. 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다. 

| 키 | 값 | 
|-----|------------| 
| typeahead | true | 

**typeahead** 플래그는 주소 검색 API에 쿼리를 부분 입력으로 처리하고 예측 값의 배열을 반환하도록 지시합니다.

## <a name="search-for-an-address-using-fuzzy-search-api"></a>使用模糊搜索 API 搜索地址

如果你不知道用户输入用于搜索查询，则建议使用 Azure Maps[模糊搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 。 该 API 将兴趣点（POI）搜索和地理编码组合到规范的 "单行搜索" 中。 예를 들어 API는 모든 주소 또는 POI 토큰 조합의 입력을 처리할 수 있습니다. 또한 API는 컨텍스트 위치(위도/경도 쌍)에 가중치를 부여하거나, 좌표와 반경으로 완전히 제한하거나, 지리적으로 편향된(geo biasing) 앵커 지점 없이 더 일반적으로 실행할 수도 있습니다.

대부분의 검색 쿼리는 성능을 달성하고 비정상적인 결과를 줄이기 위해 기본적으로 `maxFuzzyLevel=1`로 설정됩니다. 이 기본값은 요청에 따라 필요한 경우 쿼리 매개 변수에 `maxFuzzyLevel=2` 또는 `3`을 전달하여 재정의할 수 있습니다.

### <a name="search-for-an-address-using-fuzzy-search"></a>유사 항목 검색을 사용하여 주소 검색

1. Postman 앱을 열고 새로 만들기 새로 만들기를 클릭하고 **GET 요청**을 선택합니다. **유사 항목 검색**의 요청 이름을 입력하고 이를 저장할 컬렉션 또는 폴더를 선택하고 **저장**을 클릭합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 엔드포인트에 대한 요청 URL을 입력합니다.

    ![유사 항목 검색](./media/how-to-search-for-address/fuzzy_search_url.png)

    | 매개 변수 | 제안 값 |
    |---------------|------------------------------------------------|
    | HTTP 메서드 | GET |
    | 요청 URL | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | 권한 부여 | 인증 없음 |

    URL 경로의 **json** 특성은 응답 형식을 결정합니다. 本文使用 json 以方便使用和提高可读性。 사용 가능한 응답 형식은 [Maps 기능 API 참조](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)의 **유사 항목 검색 가져오기** 정의에서 찾을 수 있습니다.

3. **Params**를 클릭하고 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 다음 키 / 값 쌍을 입력합니다.

    ![유사 항목 검색](./media/how-to-search-for-address/fuzzy_search_params.png)

    | 키 | 값 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | 구독 키 | \<사용자의 Azure 맵 키\> |
    | Query | 피자 |

4. **보내기**를 클릭하고 응답 본문을 검토합니다.

    "比萨饼" 的不明确查询字符串在 "比萨饼" 和 "餐馆" 类别中返回10个[兴趣点](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse)（POI）。 每个结果都返回街道地址、纬度和经度值、查看端口和位置入口点。
  
    결과는 특정 기준 위치에 연결되지 않고 이 쿼리에 대해 변화됩니다. 你可以使用**countrySet**参数来仅指定你的应用程序需要覆盖的国家/地区。 默认行为是搜索整个世界，可能会返回不必要的结果。

5. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |------------------|-------------------------|
    | countrySet | 미국 |
  
    이제 결과는 국가 코드로 경계가 구분되며 쿼리는 미국의 피자 음식점을 반환합니다.
  
    위치에 대한 결과를 제공하려면 관심 지점을 쿼리하고 반환된 위도와 경도 값을 유사 항목 검색 서비스에 대한 호출에 사용할 수 있습니다. 이 경우 검색 서비스를 사용하여 시애틀의 스페이스 니들(Space Needle) 탑의 위치를 반환하고 위도 / 경도를 사용했습니다. 검색 방향을 지정하는 값입니다.
  
6. 매개 변수에서 다음 키 / 값 쌍을 입력하고 **보내기**를 클릭합니다.

    ![유사 항목 검색](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | 키 | 값 |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |

## <a name="search-for-address-properties-and-coordinates"></a>주소 속성 및 좌표 검색

可以将完整或部分街道地址传递到搜索地址 API。 你仍会收到包含详细地址属性的响应。 详细的地址属性是高度和经度、市政府或细分中的位置值等值。

1. Postman에서 **새 요청** | **GET 요청**을 클릭하고 이름을 **주소 검색**으로 지정합니다.
2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 엔드포인트에 대한 요청 URL을 입력한 다음 권한 부여 프로토콜이 있는 경우 해당 프로토콜을 선택합니다.

    ![주소 검색](./media/how-to-search-for-address/address_search_url.png)
  
    | 매개 변수 | 제안 값 |
    |---------------|------------------------------------------------|
    | HTTP 메서드 | GET |
    | 요청 URL | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) |
    | 권한 부여 | 인증 없음 |

3. **Params**를 클릭하고 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 다음 키 / 값 쌍을 입력합니다.
  
    ![주소 검색](./media/how-to-search-for-address/address_search_params.png)
  
    | 키 | 값 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | 구독 키 | \<사용자의 Azure 맵 키\> |
    | Query | 400 한 광범위 한 St, 시애틀, WA 98109 |
  
4. **보내기**를 클릭하고 응답 본문을 검토합니다.
  
    이 경우 전체 주소 쿼리를 지정했으며 응답 본문에 단일 결과를 받습니다.
  
5. 매개 변수에 다음 값에 대한 쿼리 문자열을 편집합니다.
    ```plaintext
        400 Broad, Seattle
    ```

6. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |-----|------------|
    | typeahead | true |

    **typeahead** 플래그는 주소 검색 API에 쿼리를 부분 입력으로 처리하고 예측 값의 배열을 반환하도록 지시합니다.

## <a name="make-a-reverse-address-search"></a>进行反向地址搜索

1. Postman에서 **새 요청** | **GET 요청**을 클릭하고 **역 주소 검색**으로 이름 지정합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 엔드포인트에 대한 요청 URL을 입력합니다.
  
    ![역 주소 검색 URL](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | 매개 변수 | 제안 값 |
    |---------------|------------------------------------------------|
    | HTTP 메서드 | GET |
    | 요청 URL | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | 권한 부여 | 인증 없음 |
  
3. **Params**를 클릭하고 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 다음 키 / 값 쌍을 입력합니다.
  
    ![역 주소 검색 매개 변수](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | 키 | 값 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | 구독 키 | \<사용자의 Azure 맵 키\> |
    | Query | 47.591180,-122.332700 |
  
4. **보내기**를 클릭하고 응답 본문을 검토합니다.

    응답에는 Safeco Field에 대한 주요 주소 정보가 포함됩니다.
  
5. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |-----|------------|
    | number | true |

    如果[number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) query 参数与请求一起发送，则响应可能包含街道的一侧（左侧或右侧）以及该数字的偏移位置。
  
6. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |-----|------------|
    | returnSpeedLimit | true |
  
    设置[returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)查询参数后，响应将返回已发布的速度限制。

7. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |-----|------------|
    | returnRoadUse | true |

    [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 쿼리 매개 변수가 설정되면 응답에서 도로 수준의 역방향 지역 코드에 대한 도로 사용 배열을 반환합니다.

8. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |-----|------------|
    | roadUse | true |

    可以使用[roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)查询参数将反向地理编码查询限制为特定类型的道路。
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>使用反向地址跨街道搜索搜索交叉街道

1. Postman에서 **새 요청** | **GET 요청**을 클릭하고 **역 주소 교차로 검색**으로 이름 지정합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 엔드포인트에 대한 요청 URL을 입력합니다.
  
    ![역 주소 교차로 검색](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | 매개 변수 | 제안 값 |
    |---------------|------------------------------------------------|
    | HTTP 메서드 | GET |
    | 요청 URL | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | 권한 부여 | 인증 없음 |
  
3. **Params**를 클릭하고 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 다음 키 / 값 쌍을 입력합니다.
  
    | 키 | 값 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | 구독 키 | \<사용자의 Azure 맵 키\> |
    | Query | 47.591180,-122.332700 |
  
4. **보내기**를 클릭하고 응답 본문을 검토합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Maps 검색 서비스](https://docs.microsoft.com/rest/api/maps/search) API 설명서를 살펴봅니다.
