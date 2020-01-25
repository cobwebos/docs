---
title: Azure IoT Edge 模块的 Marketplace 说明 |Azure Marketplace
description: IoT Edge 모듈에 대한 마켓플레이스 설명을 만듭니다.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 8021b6fe40e006a95b3eeff1031606c38cc47b87
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721619"
---
# <a name="iot-edge-module-marketplace-tab"></a>IoT Edge 모듈 Marketplace 탭

**새 제품** 페이지의 **Marketplace** 탭을 사용하면 잠재 고객에게 마케팅, 판매 및 법적 정보와 계약을 제공하고 마켓플레이스에서 창출된 잠재 고객을 관리할 수 있습니다. 이 긴 양식은 **개요**, **마케팅 아티팩트**, **잠재 고객 관리** 및 **법적 정보**의 4개 섹션으로 나뉩니다.


## <a name="overview"></a>개요

이 섹션에서는 Azure Marketplace 제품에 대한 일반적인 정보를 입력합니다.  필드 이름에 추가된 별표(*)는 필수를 나타냅니다.

![IoT Edge 모듈에 대한 새 제품 양식에 있는 Marketplace 탭의 개요 섹션](./media/iot-edge-module-marketplace-tab-overview.png)

다음 표에서는 이러한 필드의 용도와 내용을 설명합니다. 필수 필드는 별표(*)로 표시됩니다.

|  **필드**                |     **설명**                                                          |
|  ---------                |     ---------------                                                          |
| **标题\***                 | 제품의 제목입니다. 마켓플레이스에서 눈에 띄게 표시됩니다.  최대 길이는 50자입니다. <!--ADD PICTURE IN ACTION-->|
| **摘要\***               | 제품의 간단한 요약입니다. 최대 길이는 100자입니다. <!--ADD PICTURE IN ACTION-->|
| **长摘要\***          | 제품의 더 긴 요약입니다(**요약**과 동일할 수 있지만).  최대 길이는 256자입니다. <!--ADD PICTURE IN ACTION-->|
| **Description\***           | 제품에 대한 설명입니다.  최대 길이는 3,000자이며, 단순 HTML 형식을 지원합니다.<br/> 맨 아래에 *최소 하드웨어 요구 사항*을 포함해야 합니다. 예: <br/> <p><u>최소 하드웨어 요구 사항:</u> Linux x64 및 arm32 OS, 1GB의 RAM, 500Mb의 스토리지</p>
| **市场营销标识符\***  | 이 제안과 연결할 고유 URL이며, 일반적으로 조직 및 솔루션 이름을 포함하고 최대 길이는 50자입니다.  예: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **미리 보기 구독 ID** | 1-100개의 미리 보기 구독 식별자를 추가합니다. 제안이 게시된 후 라이브 상태가 되기 전에 이러한 허용 목록에 추가된 구독에서 해당 제안에 액세스할 수 있습니다. |
| **유용한 링크**          | 제품이 가장 적합하게 연결될 수 있는 비즈니스 및 기술 범주에 대한 다중 선택입니다.  최대 10개가 허용됩니다. [Azure IoT 디바이스 카탈로그](https://catalog.azureiotsolutions.com/)에서 설명서에 하나 이상의 링크 및 호환 IoT Edge 디바이스에 하나의 링크를 추가해야 합니다. |
| **建议的类别\*** | 최대 5개의 범주를 선택합니다. 이러한 범주는 제품 세부 정보 페이지에 표시됩니다. 찾아보기 페이지에서 모든 IoT Edge 모듈이 *사물 인터넷 \> IoT Edge 모듈* 범주에 표시됩니다.|
|  |  |


### <a name="offer-example"></a>제품 예제

 다음 예제에서는 제품 **제목**, **요약**, **설명**, **로고** 및 **스크린샷** 필드가 서로 다른 보기에 표시되는 방식을 보여줍니다.

 
#### <a name="on-the-azure-marketplace-website"></a>Azure Marketplace 웹 사이트에서:

- 제품을 검색하는 경우:

    ![마켓플레이스 제품이 Azure Marketplace 웹 사이트에 표시되는 방식 - 찾아보기](./media/iot-edge-module-ampdotcom-card.png)

- 제품 세부 정보를 보는 경우:

    ![웹 사이트에서 제품 세부 정보를 볼 때 IoT Edge 모듈이 표시되는 방식](./media/iot-edge-module-ampdotcom-pdp.png)


#### <a name="on-the-azure-portal-website"></a>Azure Portal 웹 사이트에서:

- 제품을 검색하는 경우:

    ![Azure Portal #1을 검색하는 경우 IoT Edge 모듈이 표시되는 방식](./media/iot-edge-module-portal-browse.png)

    ![Azure Portal #2를 검색하는 경우 IoT Edge 모듈이 표시되는 방식](./media/iot-edge-module-portal-product-picker.png)

- 제품을 검색하는 경우:

    ![Azure Portal을 검색하는 경우 IoT Edge 모듈이 표시되는 방식](./media/iot-edge-module-portal-search.png)

- 제품 세부 정보를 보는 경우:

    ![포털에서 제품 세부 정보를 볼 때 IoT Edge 모듈이 표시되는 방식](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>마케팅 아티팩트

이 섹션에는 다음 하위 섹션: **로고**, **스크린샷** 및 **비디오**가 있습니다. 

>[!Note]
>로고는 유일하게 필요한 마케팅 아티팩트이지만, 모든 아티팩트가 고객에게 가장 적절하게 호소하는 데 권장됩니다.

![가상 머신에 대한 새 제안 양식에 있는 Marketplace 탭의 마케팅 아티팩트 섹션](./media/publishvm_009.png)

|  **필드**                |     **설명**                                                          |
|  ---------                |     ---------------                                                          |
| *로고*  | 로고가 사용되는 방법 및 위치를 보려면 이전 화면 캡처를 참조하세요.  |
| **작음\***                 | 40x40 픽셀 PNG 형식                                                     |
| **중간\***                | 90x90 픽셀 PNG 형식                                                     |
| **큼\***                 | 115x115 픽셀 PNG 형식                                                  |
| **宽\***                  | 255x115 픽셀 PNG 형식                                                   |
| **대표**                  | 815x290 픽셀 PNG 형식  선택 사항이지만, 일단 업로드되면 대표 로고를 삭제할 수 없습니다. |
| *스크린샷*  | 스크린샷은 제품 세부 정보 페이지에 표시됩니다. IoT Edge 모듈의 의미 및 작동 방식을 시각적으로 알려주는 좋은 방법입니다. 아키텍처 다이어그램이나 사용 사례 그림을 예제로 표시할 수 있습니다. 선택 사항이지만, SKU당 최대 5개의 스크린샷을 제공합니다. |
| **이름**                  | 이름 또는 제목입니다. 최대 길이는 100자입니다.                             |
| **이미지**                 | 화면 캡처 이미지, 533x324 픽셀 PNG 형식                               |
| *비디오*  | 비디오는 제품 세부 정보 페이지에 표시됩니다. IoT Edge 모듈의 의미 및 작동 방식을 시각적으로 알려주는 좋은 방법입니다. |
| **이름**                  | 이름 또는 제목입니다. 최대 길이는 100자입니다.                             |
| **링크**                  | YouTube 또는 Vimeo에서 호스팅되는 비디오 URL입니다.                                        |
| **썸네일**             | 533x324 픽셀 PNG 형식                                                     |
|  |  |


### <a name="logo-guidelines"></a>로고 지침

<!-- It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Cloud 파트너 포털에 업로드되는 모든 로고는 다음 지침을 따라야 합니다.

*  Azure 디자인은 단순한 색 팔레트를 사용합니다. 로고의 기본 색상과 보조 색상 수는 적게 유지합니다.
*  Azure Portal의 테마 색은 흰색과 검은색입니다. 로고의 배경색으로는 이러한 색을 사용하지 마세요. Azure Portal에서 로고가 돋보이도록 하는 색을 사용합니다. 간단한 기본 색을 사용하는 것이 좋습니다. 투명한 배경을 사용하는 경우 로고/텍스트가 흰색, 검은색 또는 파란색이 아닌지 확인합니다.
*  로고의 배경에 그라데이션 효과를 사용하지 않습니다.
*  로고에 회사 또는 브랜드 이름을 포함한 텍스트를 추가하지 않습니다. 로고의 모양과 느낌은 "평면적"이어야 하며 그라데이션은 사용하지 않습니다.
*  로고를 늘리지 않습니다.


#### <a name="hero-logo"></a>대표 로고

대표 로고는 선택 사항입니다.

>[!Important]
>대표 로고를 업로드한 후에 삭제할 수 없습니다.

대표 로고에 다음 지침을 사용합니다. 

*  검은색, 흰색 및 투명한 배경이 허용되지 않습니다.
*  밝은 색을 로고의 배경으로 사용하지 않습니다.  게시자 표시 이름, 계획 제목 및 긴 제품 요약은 흰색 글꼴 색으로 표시되며 배경에서 눈에 잘 띄어야 합니다.
*  로고를 디자인할 때 대부분의 텍스트를 사용하지 마십시오. 제품이 나열되면 로고 내에 게시자 이름, 계획 제목, 긴 제품 요약 및 만들기 단추가 프로그래밍 방식으로 포함됩니다. 
* 대표 로고의 오른쪽에는 사용되지 않는 사각형 공간을 포함합니다. 이 빈 공간은 415x100 픽셀이며, 왼쪽에서 370 픽셀만큼 오프셋됩니다.  

<!-- P2: would be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>잠재 고객 관리

이 섹션을 통해 Azure Marketplace 제품에서 생성된 잠재 고객 수집을 위한 옵션을 설정할 수 있습니다. 드롭다운 목록에서 다음과 같은 스토리지 옵션을 선택할 수 있습니다.

* **없음** - 잠재 고객 정보가 기본적으로 수집되지 않습니다.
* Azure 테이블 - 연결 문자열로 지정된 Azure 테이블에 기록됩니다.
* Dynamics CRM Online - URL 및 인증 자격 증명으로 지정된 [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) 인스턴스에 기록됩니다.
* HTTPS 엔드포인트 - JSON 페이로드로 지정된 HTTPS 엔드포인트에 기록됩니다.
* Marketo - 서버 ID, munchkin ID 및 양식 ID로 지정된 [Marketo](https://www.marketo.com/) 인스턴스에 기록됩니다.
* Salesforce - 개체 식별자로 지정된 [Salesforce](https://www.salesforce.com/) 데이터베이스에 기록됩니다.

제품이 성공적으로 게시되면 잠재 고객 연결이 확인되고 테스트 잠재 고객이 구성한 대상으로 자동으로 전송됩니다. 

>[!Note]
>잠재 고객 정보는 지속적으로 관리해야 하며, 고객 관리 아키텍처가 변경될 때마다 이러한 설정을 즉시 업데이트해야 합니다.

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->


## <a name="legal"></a>법적 고지 사항

이 섹션에서는 각 제품에 필요한 두 가지 법적 문서, 즉 개인정보처리방침 및 사용 약관을 제공할 수 있습니다.

|  **필드**                    |     **설명**                                                          |
|  ---------                    |     ---------------                                                          |
| **隐私策略 URL\***      | 게시된 개인정보처리방침에 대한 URL                                            |
| **使用标准 Constract\***  | 是否使用标准 Microsoft 合同模板。  有关详细信息，请参阅[标准协定](https://docs.microsoft.com/azure/marketplace/standard-contract)。   |
| **사용 약관\***            | *使用条款*为内联简单 HTML 或链接到已发布的使用条款页面     |
|  |  |


## <a name="next-steps"></a>다음 단계

[지원](./cpp-support-tab.md) 탭을 사용하여 제품에 대한 기술 및 사용자 지원 리소스를 제공합니다.
