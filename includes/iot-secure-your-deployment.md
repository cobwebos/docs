---
title: 포함 파일
description: 포함 파일
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 08cca67455df4b2d28bba0a7410fccc11446fcdc
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748928"
---
이 문서에서는 Azure IoT 기반 IoT(사물 인터넷) 인프라를 보호하기 위한 세부 정보를 제공합니다. 또한 각 구성 요소의 구성 및 배포에 대한 구현 수준의 세부 정보에 연결됩니다. 그밖에도 다양한 경쟁 방법 간을 비교하고 선택 항목을 제공합니다.

Azure IoT 배포 보안 유지 과정은 다음과 같은 세 가지 보안 영역으로 나눌 수 있습니다.

* **디바이스 보안**: 실제로 배포되는 동안 IoT 디바이스 보안 유지

* **연결 보안**: IoT 디바이스와 IoT Hub 간에 전송된 모든 데이터를 기밀 상태로 유지하고 변경 방지

* **클라우드 보안**: 데이터가 이동되고 클라우드에 저장되는 동안 보안을 유지하는 수단 제공

![세 가지 보안 영역](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>보안 디바이스를 프로비전 및 인증

IoT 솔루션 가속기는 다음 두 가지 방법으로 IoT 디바이스를 보호합니다.

* 각 디바이스에 대해 IoT Hub와 통신하는 데 사용할 수 있는 고유한 ID 키(보안 토큰) 제공

* 디바이스 기반의 [X.509 인증서](https://www.itu.int/rec/T-REC-X.509-201210-S) 및 프라이빗 키를 IoT Hub에 디바이스를 인증하는 수단으로 사용합니다. 이 인증 방법은 디바이스의 프라이빗 키를 디바이스 외부에서 항상 알 수 없도록 하여 더 높은 수준의 보안을 제공합니다.

보안 토큰 방법은 대칭 키를 각 호출에 연결하여 디바이스에서 IoT Hub에 대해 수행한 각 호출에 대한 인증을 제공합니다. X.509 기반 인증은 TLS 연결 설정의 일부로 물리적 레이어에서 IoT 디바이스 인증을 허용합니다. 보안 토큰 기반 방법은 X.509 인증 없이 사용할 수 있으므로 덜 안전한 패턴입니다. 두 방법 중에서 선택할 때는 우선, 프라이빗 키를 안전하게 저장하는 데 필요한 디바이스 인증의 보안 수준 및 디바이스에 있는 보안 스토리지의 가용성을 고려해야 합니다.

## <a name="iot-hub-security-tokens"></a>IoT Hub 보안 토큰

IoT Hub는 네트워크에서 토큰이 전송되는 것을 피하기 위해 보안 토큰을 사용하여 디바이스 및 서비스를 인증합니다. 또한 보안 토큰은 유효 기간 및 범위가 제한됩니다. Azure IoT SDK는 특별한 구성이 필요하지 않고 토큰을 자동으로 생성합니다. 그러나 일부 시나리오에서는 사용자가 보안 토큰을 직접 생성하고 사용해야 합니다. 이러한 시나리오에는 MQTT, AMQP 또는 HTTP 표면의 직접 사용 또는 토큰 서비스 패턴의 구현이 포함됩니다.

보안 토큰의 구조 및 사용법에 대한 자세한 내용은 다음 문서에 나와 있습니다.

* [보안 토큰 구조](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [디바이스로 SAS 토큰 사용](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

각 IoT Hub에는 진행 중인 클라우드-디바이스 메시지를 포함하는 큐와 같은 서비스에 디바이스 단위 리소스를 만들고 디바이스 지향 엔드포인트에 대한 액세스를 허용하는 데 사용할 수 있는 [ID 레지스트리](../articles/iot-hub/iot-hub-devguide-identity-registry.md)가 있습니다. IoT Hub ID 레지스트리는 디바이스 ID에 대한 보안 스토리지와 솔루션을 위한 보안 키를 제공합니다. 개별 또는 그룹 디바이스 ID를 허용 목록 또는 차단 목록에 추가하여 디바이스 액세스를 완벽하게 제어할 수 있습니다. 다음 문서에서는 ID 레지스트리의 구조 및 지원되는 작업에 대한 자세한 내용을 제공합니다.

[IoT Hub는 MQTT, AMQP 및 HTTP와 같은 프로토콜을 지원합니다](../articles//iot-hub/iot-hub-devguide-security.md). 이러한 각 프로토콜은 IoT 디바이스에서 IoT Hub로의 보안 토큰을 다음과 같이 다르게 사용합니다.

* AMQP: SASL PLAIN 및 AMQP 클레임 기반 보안(IoT 허브 수준 토큰의 경우 `{policyName}@sas.root.{iothubName}`, 디바이스 범위 토큰의 경우 `{deviceId}`)

* MQTT: CONNECT 패킷은 **사용자 이름** 필드에서는 `{deviceId}`를 `{ClientId}`, `{IoThubhostname}/{deviceId}`로 사용하고, **암호** 필드에서는 SAS 토큰을 사용합니다.

* HTTP: 올바른 토큰은 권한 부여 요청 헤더에 있습니다.

IoT Hub ID 레지스트리를 사용하여 디바이스별 보안 자격 증명 및 액세스 제어를 구성할 수 있습니다. 그러나 [사용자 지정 디바이스 ID 레지스트리 및/또는 인증 체계](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication)에 이미 상당한 투자를 한 IoT 솔루션의 경우 토큰 서비스를 만들어 IoT Hub가 있는 기존 인프라로 통합할 수 있습니다.

### <a name="x509-certificate-based-device-authentication"></a>X.509 인증서 기반 디바이스 인증

[디바이스 기반 X.509 인증서](../articles/iot-hub/iot-hub-devguide-security.md) 및 연결된 프라이빗 및 공개 키 쌍을 사용하면 물리적 계층에서 추가 인증이 수행될 수 있습니다. 프라이빗 키는 디바이스에 안전하게 저장되며 디바이스 외부에서 검색할 수 없습니다. X.509 인증서에는 디바이스 ID와 같은 디바이스 정보와 기타 조직 세부 정보가 포함되어 있습니다. 인증서의 서명은 프라이빗 키를 사용하여 생성됩니다.

고급 디바이스 프로비저닝 흐름:

* 물리적 디바이스에 식별자 연결 - 디바이스 ID 및/또는 디바이스 제조 또는 위임 동안 디바이스에 연결된 X.509 인증서

* IoT Hub에서 해당 ID 항목 만들기 – 디바이스 ID 및 IoT Hub ID 레지스트리의 연결된 디바이스 정보

* X.509 인증서 지문을 IoT Hub ID 레지스트리에 안전하게 저장

### <a name="root-certificate-on-device"></a>디바이스의 루트 인증서

IoT Hub와의 보안 TLS 연결을 설정하는 동안 IoT 디바이스는 디바이스 SDK의 일부인 루트 인증서를 사용하여 IoT Hub를 인증합니다. C 클라이언트 SDK의 경우 인증서는 repo의 루트 아래에 있는 "\\c\\certs" 폴더 아래에 있습니다. 이러한 루트 인증서는 수명이 길지만 만료되거나 해지될 수 있습니다. 디바이스의 인증서를 업데이트할 수 없는 경우 나중에 디바이스를 IoT Hub(또는 다른 클라우드 서비스)에 연결하지 못할 수 있습니다. IoT 디바이스가 배포된 후에 루트 인증서를 업데이트하는 방법을 유지하면 이러한 위험을 효과적으로 완화하는 데 도움이 됩니다.

## <a name="securing-the-connection"></a>연결 보안

IoT 디바이스와 IoT Hub 간 인터넷 연결은 TLS(전송 계층 보안) 표준을 사용하여 보안이 유지됩니다. Azure IoT는 [TLS 1.2](https://tools.ietf.org/html/rfc5246), TLS 1.1 및 TLS 1.0을 순서대로 지원합니다. TLS 1.0에 대한 지원은 이전 버전과의 호환성을 위해서만 제공됩니다. 请[在 IoT 中心内检查 TLS 支持](../articles/iot-hub/iot-hub-tls-support.md)，了解如何将集线器配置为使用 TLS 1.2，因为它提供了最高的安全性。

## <a name="securing-the-cloud"></a>클라우드 보안

Azure IoT Hub에서는 각 보안 키에 대해 [액세스 제어 정책](../articles/iot-hub/iot-hub-devguide-security.md)을 정의할 수 있습니다. 또한 다음 권한 집합을 사용하여 각 IoT Hub의 엔드포인트에 대한 액세스 권한을 부여합니다. 사용 권한은 기능에 따라 IoT Hub에 대한 액세스를 제한합니다.

* **RegistryRead**. ID 레지스트리에 대한 읽기 액세스 권한을 부여합니다. 자세한 내용은 [ID 레지스트리](../articles/iot-hub/iot-hub-devguide-identity-registry.md)를 참조하세요.

* **RegistryReadWrite**. ID 레지스트리에 대한 읽기 및 쓰기 액세스 권한을 부여합니다. 자세한 내용은 [ID 레지스트리](../articles/iot-hub/iot-hub-devguide-identity-registry.md)를 참조하세요.

* **ServiceConnect**. 클라우드 서비스 지향 통신 및 모니터링 중인 엔드포인트에 대한 액세스를 부여합니다. 예를 들어 디바이스-클라우드 메시지를 받고 클라우드-디바이스 메시지를 보내며 해당 전달 승인을 검색할 수 있는 권한을 백 엔드 클라우드 서비스에 부여합니다.

* **DeviceConnect**. 디바이스 지향 엔드포인트에 대한 액세스를 부여합니다. 예를 들어 디바이스-클라우드 메시지를 보내고 클라우드-디바이스 메시지를 받을 수 있는 권한을 클라우드에 부여합니다. 이 권한은 디바이스에서 사용됩니다.

디바이스 ID 키를 사용하거나 공유 액세스 키를 사용하는 등 두 가지 방법으로 [보안 토큰](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)과 함께 IoT Hub에서 **DeviceConnect** 권한을 얻을 수 있습니다. 또한 디바이스에서 액세스할 수 있는 모든 기능이 `/devices/{deviceId}`접두사가 있는 엔드포인트에서 디자인 시 노출되는 것을 유념해야 합니다.

[서비스 구성 요소는 적절한 권한을 부여하는 공유 액세스 정책을 사용하여 보안 토큰을 생성할 수 있습니다](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components).

Azure IoT Hub 및 이 솔루션에 속할 수 있는 기타 서비스는 Azure Active Directory를 사용하여 사용자를 관리할 수 있도록 허용합니다.

Azure IoT Hub를 통해 수집된 데이터는 Azure Stream Analytics, Azure Blob Storage 등과 같은 다양한 서비스에서 사용될 수 있습니다. 이러한 서비스는 관리 액세스를 허용합니다. 이러한 서비스 및 사용 가능한 옵션에 대해 읽어보세요.

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): 반구조화된 데이터에 대한 확장성 있고 완전히 인덱싱된 데이터베이스 서비스로, 프로비전하는 디바이스에 대한 메타데이터(예: 특성, 구성 및 보안 속성)를 관리합니다. Azure Cosmos DB는 높은 성능 및 처리량 처리, 데이터의 스키마와 관계 없는 인덱싱 및 풍부한 SQL 쿼리 인터페이스를 제공합니다.

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/): 디바이스, 센서, 인프라 및 애플리케이션에서 실시간 인사이트를 파악하는 저비용 분석 솔루션을 빠르게 개발 및 배포하는 클라우드의 실시간 스트림 처리입니다. 이 완전히 관리되는 서비스의 데이터를 높은 처리량, 낮은 대기 시간 및 복원력을 확보하면서 어떤 볼륨으로도 확장할 수 있습니다.

* [Azure App Services](https://azure.microsoft.com/services/app-service/): 강력한 웹 및 모바일 앱을 빌드하기 위한 클라우드 플랫폼으로 클라우드 또는 온-프레미스 등 어디서나 데이터에 연결할 수 있습니다. iOS, Android 및 Windows를 위한 유용한 모바일 앱을 빌드하세요. 수십 개의 클라우드 기반 서비스와 엔터프라이즈 애플리케이션에 즉시 연결 가능하므로 귀사의 SaaS(Software as a Service) 및 엔터프라이즈 애플리케이션과 통합이 용이합니다. 가장 자주 사용하는 언어와 IDE(.NET, Node.js, PHP, Python 또는 Java)로 코딩하여 더욱 빨리 웹앱과 API를 빌드하세요.

* [Logic Apps](https://azure.microsoft.com/services/app-service/logic/): Azure App Service의 Logic Apps 기능을 통해 IoT 솔루션을 기존의 LOB(기간 업무) 시스템에 통합하고 워크플로 프로세스를 자동화할 수 있습니다. Logic Apps를 사용하면 개발자는 트리거부터 시작하여 비즈니스 프로세스와 통합할 강력한 커넥터를 사용하는 규칙 및 작업으로 이루어진 일련의 단계를 수행하는 워크플로를 설계할 수 있습니다. Logic Apps는 SaaS, 클라우드 기반 및 온-프레미스 애플리케이션의 방대한 생태계에 즉시 연결을 제공합니다.

* [Azure Blob Storage](https://azure.microsoft.com/services/storage/): 디바이스가 클라우드로 전송하는 데이터에 대한 안정적이고 경제적인 클라우드 스토리지입니다.

## <a name="conclusion"></a>결론

이 문서에서는 Azure IoT를 사용하여 IoT 인프라를 디자인 및 배포하기 위한 구현 수준 세부 정보의 개요를 제공합니다. 각 구성 요소를 보안 상태로 구성하는 것은 전체 IoT 인프라 보안 설정에서 핵심이 됩니다. Azure IoT에서 사용할 수 있는 디자인 선택 옵션은 일정 수준의 유연성과 선택 다양성을 제공하지만 선택 항목마다 보안 문제가 관련되어 있을 수 있습니다. 위험/비용 평가를 통해 이러한 각 선택 옵션을 평가하는 것이 좋습니다.
