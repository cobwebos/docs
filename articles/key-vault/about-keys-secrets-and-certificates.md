---
title: Azure Key Vault 키, 비밀 및 인증서 정보 - Azure Key Vault
description: 키, 비밀 및 인증서에 대한 Azure Key Vault REST 인터페이스 및 개발자 정보의 개요입니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: affa182145645b9a91801a9c6b38e682e6bd77ec
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720055"
---
# <a name="about-keys-secrets-and-certificates"></a>키, 비밀 및 인증서 정보

Azure Key Vault는 Microsoft Azure 애플리케이션 및 사용자가 여러 가지 종류의 비밀/키 데이터를 저장하고 사용할 수 있게 도와줍니다.

- 암호화 키: Key Vault는 여러 키 유형과 알고리즘을 지원하며, 고부가 가치 키에 HSM(하드웨어 보안 모듈)을 사용할 수 있습니다. 
- 비밀: 암호, 데이터베이스 연결 문자열 등의 비밀을 안전하게 스토리지합니다.
- 인증서: 인증서를 지원합니다. 인증서는 키와 비밀을 기반으로 하며 자동 갱신 기능을 추가합니다.
- Azure Storage: 관리자를 대신하여 Azure Storage 계정의 키를 관리할 수 있습니다. 내부적으로 Key Vault는 Azure Storage 계정을 사용하여 키를 나열(동기화)하고, 주기적으로 키를 다시 생성(회전)할 수 있습니다. 

Key Vault에 대한 일반적 내용은 [Azure Key Vault란?](/azure/key-vault/key-vault-overview)을 참조하세요.

## <a name="azure-key-vault"></a>Azure Key Vault

다음 섹션에서는 Key Vault 서비스의 구현에 적용할 수 있는 일반 정보를 제공합니다.

### <a name="supporting-standards"></a>지원 표준

JSON(JavaScript Object Notation) 및 JOSE(JavaScript Object Signing and Encryption) 사양은 중요한 배경 정보입니다.  

-   [JWK(JSON 웹 키)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)  
-   [JWE(JSON 웹 암호화)](https://tools.ietf.org/html/draft-ietf-jose-json-web-encryption-40)  
-   [JWA(JSON 웹 알고리즘)](https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40)  
-   [JWS(JSON 웹 서명)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41)  

### <a name="data-types"></a>데이터 형식

키, 암호화 및 서명에 대한 관련 데이터 형식은 JOSE 사양을 참조하세요.  

-   **알고리즘** - 키 작업에 지원되는 알고리즘(예: RSA1_5)  
-   **암호 텍스트 값** - Base64URL을 사용하여 인코딩된 암호 텍스트 옥텟  
-   **다이제스트 값** - Base64URL을 사용하여 인코딩된 해시 알고리즘의 출력  
-   **키 유형** - 지원되는 키 유형 중 하나(예: RSA(Rivest-Shamir-Adleman))  
-   **일반 텍스트 값** - Base64URL을 사용하여 인코딩된 일반 텍스트 옥텟  
-   **서명 값** - Base64URL을 사용하여 인코딩된 서명 알고리즘의 출력  
-   **base64URL** - Base64URL[RFC4648]을 사용하여 인코딩된 이진 값  
-   **부울** - true 또는 false 중 하나  
-   **ID** - AAD(Azure Active Directory)의 ID  
-   **IntDate** - 1970-01-01T0:0:0Z UTC에서 지정된 UTC 날짜/시간까지의 초 수를 나타내는 JSON 10진수 값입니다. 일반적 날짜/시간, 특히 UTC에 대한 자세한 내용은 RFC3339를 참조하세요.  

### <a name="objects-identifiers-and-versioning"></a>개체, 식별자 및 버전 관리

Key Vault에 저장된 개체는 개체의 새 인스턴스가 만들어질 때마다 버전 관리됩니다. 각 버전에는 고유 식별자 및 URL이 할당됩니다. 개체가 처음으로 만들어지면 고유한 버전 식별자가 지정되고 개체의 현재 버전으로 표시됩니다. 개체 이름이 동일한 새 인스턴스를 만들면 새 개체에 고유한 버전 식별자가 제공되어 현재 버전이 됩니다.  

Key Vault의 개체는 현재 식별자 또는 버전별 식별자를 사용하여 처리할 수 있습니다. 예를 들어 `MasterKey`라는 이름의 키가 지정되는 경우 현재 식별자를 사용하여 작업을 수행하면 시스템에서 사용 가능한 최신 버전을 사용하게 됩니다. 버전별 식별자를 사용하여 작업을 수행하면 시스템에서 해당 특정 버전의 개체를 사용하게 됩니다.  

개체는 URL을 사용하여 Key Vault 내에서 고유하게 식별됩니다. 지리적 위치에 관계 없이, 시스템의 두 개체가 동일한 URL을 가질 수 없습니다. 개체의 전체 URL을 개체 식별자라고 합니다. URL은 Key Vault를 식별하는 접두사, 개체 형식, 사용자가 입력한 개체 이름 및 개체 버전으로 구성됩니다. 개체 이름은 대/소문자를 구분하지 않으며 변경할 수 없습니다. 개체 버전이 포함되지 않은 식별자를 기본 식별자라고 합니다.  

자세한 내용은 [인증, 요청 및 응답](authentication-requests-and-responses.md)을 참조하세요.

개체 식별자의 일반적인 형식은 다음과 같습니다.  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

장소:  

|||  
|-|-|  
|`keyvault-name`|Microsoft Azure Key Vault 서비스의 키 자격 증명 모음에 대한 이름입니다.<br /><br /> Key Vault 이름은 사용자가 선택하며 전역적으로 고유합니다.<br /><br /> Key Vault 이름은 0~9, a~z, A~Z 및 -만 포함된 3-24자 길이의 문자열이어야 합니다.|  
|`object-type`|개체의 형식은 "키" 또는 "비밀"입니다.|  
|`object-name`|`object-name`은 사용자가 제공한 이름이며 Key Vault 내에서 고유해야 합니다. 이름은 0~9, a~z, A~Z 및 -만 포함된 1-127자 길이의 문자열이어야 합니다.|  
|`object-version`|`object-version`은 시스템에서 생성된 32자의 문자열 식별자이며, 필요에 따라 고유한 버전의 개체를 처리하는 데 사용됩니다.|  

## <a name="key-vault-keys"></a>Key Vault 키

### <a name="keys-and-key-types"></a>키 및 키 유형

Key Vault의 암호화 키는 JWK[JSON 웹 키] 개체로 표현됩니다. 또한 기본 JWK/JWA 사양을 확장하여 Key Vault 구현에 고유한 키 유형을 지원할 수 있습니다. 예를 들어 HSM 공급업체별 패키징을 사용하여 키를 가져오면 Key Vault HSM에서만 사용할 수 있는 키를 안전하게 전송할 수 있습니다.  

- **"소프트" 키**: 소프트웨어에서 Key Vault를 통해 처리되지만, 사용되지 않을 때에는 HSM에 있는 시스템 키를 사용하여 암호화되는 키입니다. 클라이언트는 기존 RSA 또는 EC(타원 곡선) 키를 가져오거나 Key Vault에서 생성하도록 요청할 수 있습니다.
- **"하드" 키**: HSM(하드웨어 보안 모듈)에서 처리되는 키입니다. 이러한 키는 Key Vault HSM 보안 권역 중 하나에서 보호됩니다(격리를 유지하기 위해 지역별로 보안 권역이 하나씩 있음). 클라이언트는 소프트 형식으로 또는 호환되는 HSM 디바이스에서 내보내는 방식으로 RSA 또는 EC 키를 가져올 수 있습니다. 클라이언트는 Key Vault에 키를 생성하라고 요청할 수도 있습니다. 此密钥类型将 key_hsm 特性添加到 JWK 获取以携带 HSM 密钥材料。

     지리적 경계에 대한 자세한 내용은 [Microsoft Azure 보안 센터](https://azure.microsoft.com/support/trust-center/privacy/)를 참조하세요.  

Key Vault는 RSA 및 타원 곡선 키만 지원합니다. 

-   **EC**: "소프트" 타원 곡선 키입니다.
-   **EC-HSM**: "하드" 타원 곡선 키입니다.
-   **RSA**: "소프트" RSA 키입니다.
-   **RSA-HSM**: "하드" RSA 키입니다.

Key Vault는 크기가 2048, 3072, 4096인 RSA 키를 지원합니다. Key Vault는 타원 곡선 키 유형 p-256, p-384, p-521 및 P-256K(SECP256K1)를 지원합니다.

### <a name="cryptographic-protection"></a>암호화 보호

Key Vault에서 사용하는 암호화 모듈(HSM 또는 소프트웨어)은 FIPS에 유효합니다. FIPS 모드에서 실행되는 어떤 특별한 작업도 수행할 필요가 없습니다. HSM으로 보호되는 키로 **만들어진** 또는 **가져온** 키는 FIPS 140-2 Level 2에서 유효한 HSM 내부에서 처리됩니다. 소프트웨어로 보호되는 키로 **만들어진** 또는 **가져온** 키는 FIPS 140-2 Level 1에서 유효한 암호화 모듈 내부에서 처리됩니다. 자세한 내용은 [키 및 키 유형](#keys-and-key-types)을 참조하세요.

###  <a name="ec-algorithms"></a>EC 알고리즘
 Key Vault의 EC 및 EC-HSM 키에서 지원되는 알고리즘 식별자는 다음과 같습니다. 

#### <a name="curve-types"></a>곡선 유형

-   **P-256** - [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf)에 정의되는 NIST 곡선 P-256.
-   **P-256K** - [SEC 2: Recommended Elliptic Curve Domain Parameters](https://www.secg.org/sec2-v2.pdf)에 정의되는 SEC 곡선 SECP256K1.
-   **P-384** - [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf)에 정의되는 NIST 곡선 P-384.
-   **P-521** - [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf)에 정의되는 NIST 곡선 P-521.

#### <a name="signverify"></a>SIGN/VERIFY

-   **ES256** - 곡선 P-256을 사용하여 생성된 SHA-256 메시지 다이제스트 및 키에 대한 ECDSA입니다. 이 알고리즘은 [RFC7518](https://tools.ietf.org/html/rfc7518)에 설명되어 있습니다.
-   **ES256K** - 곡선 P-256K를 사용하여 생성된 SHA-256 메시지 다이제스트 및 키에 대한 ECDSA입니다. 이 알고리즘 표준화 보류 중입니다.
-   **ES384** - 곡선 P-384를 사용하여 생성된 SHA-384 메시지 다이제스트 및 키에 대한 ECDSA입니다. 이 알고리즘은 [RFC7518](https://tools.ietf.org/html/rfc7518)에 설명되어 있습니다.
-   **ES512** - 곡선 P-521을 사용하여 생성된 SHA-512 메시지 다이제스트 및 키에 대한 ECDSA입니다. 이 알고리즘은 [RFC7518](https://tools.ietf.org/html/rfc7518)에 설명되어 있습니다.

###  <a name="rsa-algorithms"></a>RSA 알고리즘  
 Key Vault의 RSA 및 RSA-HSM 키에서 지원되는 알고리즘 식별자는 다음과 같습니다.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, ENCRYPT/DECRYPT

-   **RSA1_5** - RSAES-PKCS1-V1_5[RFC3447] 키 암호화입니다.  
-   **RSA-OAEP** - OAEP(Optimal Asymmetric Encryption Padding)[RFC3447]를 사용하는 RSAES입니다(A.2.1 섹션의 RFC 3447에 명시된 기본 매개 변수 포함). 이러한 기본 매개 변수는 SHA-1의 해시 함수와 SHA-1이 포함된 MGF1의 마스크 생성 함수를 사용합니다.  

#### <a name="signverify"></a>SIGN/VERIFY

-   **RS256** - SHA-256을 사용하는 RSASSA-PKCS-v1_5입니다. 애플리케이션에서 제공하는 다이제스트 값은 SHA-256을 사용하여 계산되어야 하며, 길이는 32바이트여야 합니다.  
-   **RS384** - SHA-384를 사용하는 RSASSA-PKCS-v1_5입니다. 애플리케이션에서 제공하는 다이제스트 값은 SHA-384를 사용하여 계산되어야 하며, 길이는 48바이트여야 합니다.  
-   **RS512** - SHA-512를 사용하는 RSASSA-PKCS-v1_5입니다. 애플리케이션에서 제공하는 다이제스트 값은 SHA-512를 사용하여 계산되어야 하며, 길이는 64바이트여야 합니다.  
-   **RSNULL** - 특정 TLS 시나리오를 사용하려면 특수 사용 사례인 [RFC2437]을 참조하세요.  

###  <a name="key-operations"></a>키 작업

Key Vault에서 키 개체에 대해 지원하는 작업은 다음과 같습니다.  

-   **만들기**: 클라이언트가 Key Vault에 키를 만들 수 있습니다. 키 값은 Key Vault에서 생성되고 저장되며 클라이언트에 릴리스되지 않습니다. Key Vault에 비대칭 키를 만들 수 있습니다.  
-   **가져오기**: 클라이언트가 기존 키를 Key Vault로 가져올 수 있습니다. JWK 구조 내에서 다양한 패키징 메서드를 사용하여 Key Vault로 비대칭 키를 가져올 수 있습니다. 
-   **업데이트**: 충분한 권한이 있는 클라이언트는 이전에 Key Vault에 저장된 키와 연결된 메타데이터(키 특성)를 수정할 수 있습니다.  
-   **삭제**: 충분한 권한이 있는 클라이언트는 Key Vault에서 키를 삭제할 수 있습니다.  
-   **나열**: 클라이언트가 지정된 Key Vault의 모든 키를 나열할 수 있습니다.  
-   **버전 나열**: 클라이언트가 지정된 Key Vault의 지정된 키 버전을 모두 나열할 수 있습니다.  
-   **가져오기**: 클라이언트가 Key Vault에서 지정된 키의 공개 부분을 검색할 수 있습니다.  
-   **백업**: 키를 보호된 형식으로 내보냅니다.  
-   **복원**: 이전에 백업한 키를 가져옵니다.  

자세한 내용은 [Key Vault REST API 참조에서 키 작업](/rest/api/keyvault)을 참조하세요.  

키가 Key Vault에 만들어지면 해당 키를 사용하여 수행할 수 있는 암호화 작업은 다음과 같습니다.  

-   **서명 및 확인**: 엄격히 말해서, Key Vault는 서명 만들기의 일부로 콘텐츠의 해싱을 지원하지 않으므로 이 작업은 "해시 서명" 또는 "해시 확인"입니다. 애플리케이션은 로컬로 서명할 데이터를 해시한 다음, Key Vault에서 이 해시에 서명하도록 요청해야 합니다. [공개] 키 자료에 액세스할 수 없는 애플리케이션을 편리하게 사용할 수 있도록 서명된 해시 확인이 지원됩니다. 애플리케이션 성능을 최적화하려면 작업이 로컬로 수행되는지 확인합니다.  
-   **키 암호화/래핑**: Key Vault에 저장된 키는 다른 키(일반적으로 대칭 CEK(콘텐츠 암호화 키))를 보호하는 데 사용할 수 있습니다. Key Vault의 키가 비대칭이면 키 암호화가 사용됩니다. 예를 들어 RSA-OAEP 및 WRAPKEY/UNWRAPKEY 작업은 ENCRYPT/DECRYPT와 동일합니다. Key Vault의 키가 대칭이면 키 래핑이 사용됩니다. 예: AES-KW. [공개] 키 자료에 액세스할 수 없는 애플리케이션을 편리하게 사용할 수 있도록 WRAPKEY 작업이 지원됩니다. 애플리케이션 성능을 최적화하려면 WRAPKEY 작업을 로컬로 수행해야 합니다.  
-   **암호화 및 암호 해독**: Key Vault에 저장된 키는 데이터의 단일 블록을 암호화 또는 암호 해독하는 데 사용할 수 있습니다. 블록의 크기는 키 유형과 선택한 암호화 알고리즘에 따라 결정됩니다. [공개] 키 자료에 액세스할 수 없는 애플리케이션을 간편하게 사용할 수 있도록 암호화 작업이 제공됩니다. 애플리케이션 성능을 최적화하려면 암호화 작업을 로컬로 수행해야 합니다.  

비대칭 키를 사용하는 WRAPKEY/UNWRAPKEY가 필요 없는 것처럼 보일 수도 있지만(작업이 ENCRYPT/DECRYPT와 동일하므로), 구분되는 작업을 사용하는 것이 중요합니다. 구분을 통해 이러한 작업의 의미 체계 및 권한 부여를 분리하고, 서비스에서 다른 키 유형을 지원할 때 일관성을 유지할 수 있습니다.  

Key Vault는 EXPORT 작업을 지원하지 않습니다. 일단 키가 시스템에 프로비전되면 키를 추출하거나 키 자료를 수정할 수 없습니다. 그러나 Key Vault 사용자가 다른 사용 사례에 사용하기 위해 키가 필요할 수 있습니다(예: 키가 삭제된 후). 이 경우 BACKUP 및 RESTORE 작업을 사용하여 키를 보호되는 형식으로 내보낼/가져올 수 있습니다. BACKUP 작업으로 만든 키는 Key Vault 외부에서 사용할 수 없습니다. 또는 IMPORT 작업을 여러 Key Vault 인스턴스에 사용할 수 있습니다.  

사용자는 Key Vault에서 JWK 개체의 key_ops 속성을 사용하여 키별로 지원하는 암호화 작업을 제한할 수 있습니다.  

JWK 개체에 대한 자세한 내용은 [JWK(JSON 웹 키)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)를 참조하세요.  

###  <a name="key-attributes"></a>키 특성

키 자료 외에도 지정할 수 있는 특성은 다음과 같습니다. JSON 요청에서 특성이 지정되지 않은 경우에도 특성 키워드 및 중괄호('{' '}')가 필요합니다.  

- *enabled*: 부울, 선택 사항, 기본값은 **true**입니다. 암호화 작업을 위해 사용 설정 및 사용 가능한 키인지 여부를 지정합니다. *Enabled*属性与*nbf*和*exp*结合使用。当*nbf*和*exp*之间发生操作时，只有在 " *enabled* " 设置为**true**时才允许使用该操作。 [특정 조건](#date-time-controlled-operations)에 따른 특정 작업 유형을 제외하고는 *nbf* / *exp* 시간 범위에 속하지 않은 작업이 자동으로 허용되지 않습니다.
- *nbf*: IntDate, 선택 사항, 기본값은 now입니다. *nbf*(이전이 아님) 특성은 [특정 조건](#date-time-controlled-operations)에 따른 특정 작업 유형을 제외하고는 암호화 작업에 키를 사용하지 않아야 하는 시간을 식별합니다. *nbf* 특성을 처리하려면 현재 날짜/시간이 *nbf* 특성에 나열된 날짜/시간 이전이 아닌 시간 이후이거나 같아야 합니다. Key Vault는 클록 오차를 처리하기 위해 일반적으로 약간(몇 분 이내)의 여유를 제공해야 합니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.  
- *exp*: IntDate, 선택 사항, 기본값은 "forever"입니다. *exp*(만료 시간) 특성은 [특정 조건](#date-time-controlled-operations)에 따른 특정 작업 유형을 제외하고는 암호화 작업에 키를 사용하지 않아야 하는 만료 시간 또는 그 이후를 식별합니다. *exp* 특성을 처리하려면 현재 날짜/시간이 *exp* 특성에 나열된 만료 날짜/시간 이전이어야 합니다. Key Vault는 클록 오차를 처리하기 위해 일반적으로 약간(몇 분 이내)의 여유를 제공해야 합니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.  

키 특성이 포함된 모든 응답에 포함되는 추가 읽기 전용 특성이 있습니다.  

- *created*: IntDate, 선택 사항입니다. *created* 특성은 이 버전의 키를 만든 시점을 나타냅니다. 이 특성을 추가하기 전에 만든 키의 값은 null입니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.  
- *updated*: IntDate, 선택 사항입니다. *updated* 특성은 이 버전의 키를 업데이트한 시점을 나타냅니다. 이 특성을 추가하기 전에 마지막으로 업데이트한 키의 값은 null입니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.  

IntDate 및 기타 데이터 형식에 대한 자세한 내용은 [데이터 형식](#data-types)을 참조하세요.  

#### <a name="date-time-controlled-operations"></a>날짜 및 시간 제어 작업

*nbf* / *exp* 시간 범위에 속하지 않은 아직 유효하지 않은 키와 만료된 키는 **decrypt**, **unwrap** 및 **verify** 작업에 대해 작동합니다(403, 금지됨 오류를 반환하지 않음). 아직 유효하지 않은 상태를 사용하는 이유는 프로덕션에서 사용하기 전에 키를 테스트할 수 있도록 하기 위한 것입니다. 만료된 상태를 사용하는 이유는 키가 유효할 때 만들어진 데이터에 대한 복구 작업을 허용하기 위한 것입니다. 또한 Key Vault 정책을 사용하거나 *enabled* 키 특성을 **false**로 업데이트하여 키에 대한 액세스를 사용하지 않도록 설정할 수 있습니다.

데이터 형식에 대한 자세한 내용은 [데이터 형식](#data-types)을 참조하세요.

사용 가능한 다른 특성에 대한 자세한 내용은 [JWK(JSON 웹 키)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)를 참조하세요.

### <a name="key-tags"></a>키 태그

애플리케이션 관련 메타데이터를 태그 형식으로 추가로 지정할 수 있습니다. Key Vault는 최대 15개의 태그를 지원하며, 각 태그는 256자 이름과 256자의 값을 가질 수 있습니다.  

>[!Note]
>해당 개체 형식(키, 비밀 또는 인증서)에 대한 *list* 또는 *get* 권한이 태그에 있는 경우 호출자에서 해당 태그를 읽을 수 있습니다.

###  <a name="key-access-control"></a>키 액세스 제어

Key Vault에서 관리하는 키에 대한 액세스 제어는 키 컨테이너 역할을 하는 Key Vault 수준에서 제공됩니다. 密钥的访问控制策略不同于同一 Key Vault 中机密的访问控制策略。 사용자는 키를 보관할 하나 이상의 자격 증명 모음을 만들 수 있으며, 시나리오에 따라 키를 적절하게 세분화하고 관리해야 합니다. 키 액세스 제어는 비밀 액세스 제어와 무관합니다.  

자격 증명 모음의 키 액세스 제어 항목에 대해 사용자/서비스 사용자별로 부여할 수 있는 권한은 다음과 같습니다. 这些权限会密切镜像对密钥对象允许的操作。  向密钥保管库中的服务主体授予访问权限是一项一次性操作，对于所有 Azure 订阅，它将保持不变。 可以使用它来部署所需数量的证书。 

- 키 관리 작업에 필요한 권한
  - *get*: 키의 공개 부분 및 해당 특성 읽기
  - *list*: 키 자격 증명 모음에 저장된 키 또는 키 버전 나열
  - *update*: 키 특성 업데이트
  - *create*: 새 키 만들기
  - *import*: 키 자격 증명 모음으로 키 가져오기
  - *delete*: 키 개체 삭제
  - *recover*: 삭제된 키 복구
  - *backup*: 키를 키 자격 증명 모음에 백업
  - *restore*: 키 자격 증명 모음에 백업된 키 복원

- 암호화 작업에 필요한 권한
  - *decrypt*: 키를 사용하여 바이트 시퀀스 보호 해제
  - *encrypt*: 키를 사용하여 임의의 바이트 시퀀스 보호
  - *unwrapKey*: 키를 사용하여 래핑된 대칭 키 보호 해제
  - *wrapKey*: 키를 사용하여 대칭 키 보호
  - *verify*: 키를 사용하여 다이제스트 확인  
  - *sign*: 키를 사용하여 다이제스트에 서명
    
- 권한 있는 작업에 필요한 권한
  - *purge*: 삭제된 키 제거(영구적으로 삭제)

키 사용에 대한 자세한 내용은 [Key Vault REST API 참조에서 키 작업](/rest/api/keyvault)을 참조하세요. 권한 설정에 대한 내용은 [자격 증명 모음 - 만들기 또는 업데이트](/rest/api/keyvault/vaults/createorupdate) 및 [자격 증명 모음 - 액세스 정책 업데이트](/rest/api/keyvault/vaults/updateaccesspolicy)를 참조하세요. 

## <a name="key-vault-secrets"></a>Key Vault 비밀 

### <a name="working-with-secrets"></a>비밀 사용

개발자의 관점에서 Key Vault API는 비밀 값을 수락하고 문자열로 반환합니다. 내부적으로 Key Vault는 비밀을 8진수(8비트 바이트) 시퀀스로 저장 및 관리하며, 각각의 최대 크기는 25k 바이트입니다. Key Vault 서비스는 비밀에 대한 의미 체계를 제공하지 않습니다. 단순히 데이터를 수락하고, 암호화하고, 저장하고, 비밀 식별자("id")를 반환할 뿐입니다. 식별자는 나중에 비밀을 검색하는 데 사용할 수 있습니다.  

매우 중요한 데이터의 경우 클라이언트가 데이터에 대한 추가 보호 레이어를 고려해야 합니다. 예를 들어 별도의 보호 키를 사용하여 데이터를 암호화한 후 Key Vault에 스토리지합니다.  

Key Vault는 비밀에 대한 contentType 필드도 지원합니다. 클라이언트는 비밀 데이터에 대해 검색 시 해당 데이터를 해석하는 데 도움이 되는 콘텐츠 형식을 지정할 수 있습니다. 이 필드의 최대 길이는 255자이며, 미리 정의된 값이 없습니다. 비밀 데이터를 해석하기 위한 힌트로 사용하는 것이 좋습니다. 예를 들어 구현에서는 암호와 인증서를 모두 비밀로 저장한 다음, 이 필드를 사용하여 구분할 수 있습니다. 미리 정의된 값이 없습니다.  

### <a name="secret-attributes"></a>비밀 특성

비밀 데이터 외에도 지정할 수 있는 특성은 다음과 같습니다.  

- *exp*: IntDate, 선택 사항, 기본값은 **forever**입니다. *exp*(만료 시간) 특성은 [특정 조건](#date-time-controlled-operations)에 있는 경우를 제외하고는 비밀 데이터를 검색하지 않아야 하는 만료 시간 또는 그 이후를 식별합니다. 이 필드는 키 자격 증명 모음 서비스의 사용자에게 특정 비밀이 사용되지 않음을 알리므로 오직 **정보 제공**을 목적으로 합니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.   
- *nbf*: IntDate, 선택 사항, 기본값은 **now**입니다. *nbf*(이전이 아님) 특성은 [특정 조건](#date-time-controlled-operations)에 있는 경우를 제외하고는 비밀 데이터를 검색하지 않아야 하는 시간 이전을 식별합니다. 이 필드는 오직 **정보 제공**을 목적으로 합니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다. 
- *enabled*: 부울, 선택 사항, 기본값은 **true**입니다. 이 특성은 비밀 데이터이 검색 가능 여부를 지정합니다. enabled 특성은 *nbf* 및 *exp* 사이에 작업이 발생하는 경우 *nbf* 및 *exp*와 함께 사용되며, enabled가 **true**로 설정된 경우에만 허용됩니다. [특정 조건](#date-time-controlled-operations)에 있는 경우를 제외하고는 *nbf* 및 *exp* 시간 범위에 속하지 않은 작업이 자동으로 허용되지 않습니다.  

비밀 특성이 포함된 모든 응답에 포함되는 추가 읽기 전용 특성이 있습니다.  

- *created*: IntDate, 선택 사항입니다. created 특성은 이 버전의 비밀을 만든 시점을 나타냅니다. 이 특성을 추가하기 전에 만든 비밀에 대한 값은 null입니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.  
- *updated*: IntDate, 선택 사항입니다. updated 특성은 이 버전의 비밀을 업데이트한 시점을 나타냅니다. 이 특성을 추가하기 전에 마지막으로 업데이트한 비밀에 대한 값은 null입니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.

#### <a name="date-time-controlled-operations"></a>날짜 및 시간 제어 작업

*nbf* / *exp* 시간 범위에 속하지 않은 비밀의 **get** 작업은 아직 유효하지 않은 비밀과 만료된 비밀에 대해 작동합니다. 테스트를 위해 비밀의 **get** 작업 호출을 아직 유효하지 않은 비밀에 사용할 수 있습니다. 복구 작업을 위해 만료된 비밀을 검색(**get**)할 수 있습니다.

데이터 형식에 대한 자세한 내용은 [데이터 형식](#data-types)을 참조하세요.  

### <a name="secret-access-control"></a>비밀 액세스 제어

Key Vault에서 관리되는 비밀에 대한 액세스 제어는 해당 비밀을 포함하는 Key Vault 수준에서 제공됩니다. 비밀에 대한 액세스 제어 정책은 동일한 Key Vault의 키에 대한 액세스 제어 정책과 다릅니다. 사용자는 비밀을 보관할 하나 이상의 자격 증명 모음을 만들 수 있으며, 시나리오에 따라 비밀을 적절하게 세분화하고 관리해야 합니다.   

자격 증명 모음의 비밀 액세스 제어 항목에서 보안 주체별로 사용할 수 있고 비밀 개체에 허용되는 작업과 매우 비슷한 권한은 다음과 같습니다.  

- 비밀 관리 작업에 필요한 권한
  - *get*: 비밀 읽기  
  - *list*: Key Vault에 저장된 비밀 또는 비밀 버전 나열  
  - *set*: 비밀 만들기  
  - *delete*: 비밀 삭제  
  - *recover*: 삭제된 비밀 복구
  - *backup*: 비밀을 키 자격 증명 모음에 백업
  - *restore*: 키 자격 증명 모음에 백업된 비밀 복원

- 권한 있는 작업에 필요한 권한
  - *purge*: 삭제된 비밀 제거(영구적으로 삭제)

비밀을 사용하는 방법에 대한 자세한 내용은 [Key Vault REST API 참조에서 비밀 작업](/rest/api/keyvault)을 참조하세요. 권한 설정에 대한 내용은 [자격 증명 모음 - 만들기 또는 업데이트](/rest/api/keyvault/vaults/createorupdate) 및 [자격 증명 모음 - 액세스 정책 업데이트](/rest/api/keyvault/vaults/updateaccesspolicy)를 참조하세요. 

### <a name="secret-tags"></a>비밀 태그  
애플리케이션 관련 메타데이터를 태그 형식으로 추가로 지정할 수 있습니다. Key Vault는 최대 15개의 태그를 지원하며, 각 태그는 256자 이름과 256자의 값을 가질 수 있습니다.  

>[!Note]
>해당 개체 형식(키, 비밀 또는 인증서)에 대한 *list* 또는 *get* 권한이 태그에 있는 경우 호출자에서 해당 태그를 읽을 수 있습니다.

## <a name="key-vault-certificates"></a>Key Vault 인증서

Key Vault 인증서 지원은 x509 인증서 및 다음 동작의 관리를 위해 제공됩니다.  

-   인증서 소유자는 Key Vault 만들기 프로세스 또는 기존 인증서의 가져오기를 통해 인증서를 만들 수 있습니다. 자체 서명된 인증서 및 인증 기관 생성 인증서가 모두 포함됩니다.
-   Key Vault 인증서 소유자는 프라이빗 키 자료와 상호 작용하지 않고 X509 인증서의 안전한 스토리지 및 관리를 구현할 수 있습니다.  
-   인증서 소유자는 인증서의 수명 주기를 관리하도록 Key Vault를 지시하는 정책을 만들 수 있습니다.  
-   인증서 소유자는 인증서의 만료 및 갱신이라는 수명 주기 이벤트에 대한 알림을 위해 연락처 정보를 제공할 수 있습니다.  
-   선택한 발급자 - Key Vault 파트너 X509 인증서 공급자/인증서 기관을 통한 자동 갱신을 지원합니다.

>[!Note]
>파트너가 아닌 공급자/기관도 허용되지만 자동 갱신 기능은 지원하지 않습니다.

### <a name="composition-of-a-certificate"></a>인증서 작성

Key Vault 인증서가 만들어지면 주소 지정 가능한 키와 암호도 동일한 이름으로 만들어집니다. Key Vault 키는 키 작업을 허용하고, Key Vault 비밀은 인증서 값을 비밀로 검색할 수 있게 합니다. Key Vault 인증서에는 공용 x509 인증서 메타데이터도 포함됩니다.  

인증서의 식별자 및 버전은 키 및 암호의 식별자 및 버전과 비슷합니다. Key Vault 인증서 버전으로 만든 특정 버전의 주소 지정 가능한 키와 암호는 Key Vault 인증서 응답에서 사용할 수 있습니다.
 
![인증서는 복잡한 개체입니다.](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>내보내기 가능/불가능 키

Key Vault 인증서가 만들어지면 PFX 또는 PEM 형식의 프라이빗 키를 사용하여 주소 지정 가능한 비밀에서 해당 인증서를 검색할 수 있습니다. 인증서를 만드는 데 사용된 정책은 키를 내보낼 수 있다고 표시해야 합니다. 정책에서 내보낼 수 없다고 표시하는 경우 프라이빗 키는 비밀로 검색될 때 값에 포함되지 않습니다.  

주소 지정이 가능한 키는 내보낼 수 없는 KV 인증서와 더 관련이 있습니다. 주소 지정 가능한 KV 키의 작업은 KV 인증서를 만드는 데 사용되는 KV 인증서 정책의 *keyusage* 필드에서 매핑됩니다.  

두 가지 유형의 키, 즉 *RSA* 또는 인증서가 있는 *RSA HSM*이 지원됩니다. 내보내기 가능한 키는 RSA에서만 허용되며, RSA HSM에서는 지원되지 않습니다.  

### <a name="certificate-attributes-and-tags"></a>인증서 특성 및 태그

Key Vault 인증서에는 인증서 메타데이터, 주소 지정 가능한 키 및 주소 지정 가능한 비밀 외에도 특성 및 태그가 포함됩니다.  

#### <a name="attributes"></a>특성

인증서 특성은 KV 인증서를 만들 때 만들어지는 주소 지정 가능한 키와 암호의 특성으로 미러됩니다.  

Key Vault 인증서에 포함되는 특성은 다음과 같습니다.  

-   *enabled*: 부울, 선택 사항, 기본값은 **true**입니다. 인증서 데이터를 비밀로 검색할 수 있는지 또는 키로 작동할 수 있는지 여부를 나타내기 위해 지정할 수 있습니다. 또한 *nbf*과 *exp* 사이에 작업이 발생할 때 *nbf* 및 *exp*와 함께 사용되며, enabled가 true로 설정된 경우에만 허용됩니다. *nbf* 및 *exp* 시간 범위에 속하지 않은 작업은 자동으로 허용되지 않습니다.  

응답에 포함되는 추가 읽기 전용 특성은 다음과 같습니다.

-   *created*: IntDate: 이 버전의 인증서를 만든 시점을 나타냅니다.  
-   *updated*: IntDate: 이 버전의 인증서를 업데이트한 시점을 나타냅니다.  
-   *exp*: IntDate: x509 인증서의 만료 날짜 값을 포함합니다.  
-   *nbf*: IntDate: x509 인증서의 날짜 값을 포함합니다.  

> [!Note] 
> Key Vault 인증서가 만료되면 주소 지정 가능한 해당 키와 비밀이 작동하지 않게 됩니다.  

#### <a name="tags"></a>태그

 클라이언트에서 지정하는 키 값 쌍의 사전이며, 키와 비밀의 태그와 비슷합니다.  

 > [!Note]
> 해당 개체 형식(키, 비밀 또는 인증서)에 대한 *list* 또는 *get* 권한이 태그에 있는 경우 호출자에서 해당 태그를 읽을 수 있습니다.

### <a name="certificate-policy"></a>인증서 정책

인증서 정책에는 Key Vault 인증서의 수명 주기를 만들고 관리하는 방법에 대한 정보가 포함됩니다. 프라이빗 키가 있는 인증서를 키 자격 증명 모음에 가져오면 x509 인증서를 읽어 기본 정책을 만듭니다.  

Key Vault 인증서를 처음부터 새로 만드는 경우 정책을 제공해야 합니다. 정책은 이 Key Vault 인증서 버전 또는 그 다음 Key Vault 인증서 버전을 만드는 방법을 지정합니다. 일단 정책이 설정되면 이후 버전에 대한 연속 만들기 작업에는 해당 정책이 필요하지 않습니다. Key Vault 인증서의 모든 버전에 대한 정책 인스턴스는 하나만 있습니다.  

크게 보자면, 인증서 정책에 다음 정보가 포함됩니다.  

-   X509 인증서 속성: 주체 이름, 주체 대체 이름, x509 인증서 요청을 만드는 데 사용되는 기타 속성을 포함합니다.  
-   키 속성: 키 유형, 키 길이, 내보낼 수 있는 항목, 재사용 키 필드를 포함합니다. 이러한 필드는 키를 생성하는 방법을 키 자격 증명 모음에 지시합니다.  
-   비밀 속성: 인증서를 비밀 번호로 검색하기 위해 비밀 값을 생성하기 위한 주소 지정 가능한 비밀의 콘텐츠 형식과 같은 비밀 속성을 포함합니다.  
-   수명 작업: KV 인증서에 대한 수명 작업을 포함합니다. 각 수명 작업에 포함되는 항목은 다음과 같습니다.  

     - 트리거: 만료 이전 잔존 일 수 또는 수명 범위 백분율을 통해 지정됩니다.  

     - 작업: *emailContacts* 또는 *autoRenew* 작업 유형이 지정됩니다.  

-   발급자: x509 인증서를 발급하는 데 사용할 인증서 발급자에 대한 매개 변수입니다.  
-   정책 특성: 정책과 관련된 특성을 포함합니다.  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 및 Key Vault 사용 매핑

다음 표에서는 x509 키 사용 정책과 Key Vault 인증서 만들기의 일부로 만들어진 키의 효과적인 키 작업을 매핑하고 있습니다.

|**X509 키 사용 플래그**|**Key Vault 키 작업**|**기본 동작**|
|----------|--------|--------|
|DataEncipherment|encrypt, decrypt| N/A |
|DecipherOnly|decrypt| N/A  |
|DigitalSignature|sign, verify| 인증서를 만들 때 사용하도록 지정하지 않은 Key Vault 기본값 | 
|EncipherOnly|encrypt| N/A |
|KeyCertSign|sign, verify|N/A|
|KeyEncipherment|wrapKey, unwrapKey| 인증서를 만들 때 사용하도록 지정하지 않은 Key Vault 기본값 | 
|NonRepudiation|sign, verify| N/A |
|crlsign|sign, verify| N/A |

### <a name="certificate-issuer"></a>인증서 발급자

Key Vault 인증서 개체에는 선택한 인증서 발급자 공급자와 통신하여 x509 인증서를 요청하는 데 사용되는 구성이 있습니다.  

-   具有以下证书颁发者提供程序的 Key Vault 合作伙伴： TLS/SSL 证书

|**공급자 이름**|**위치**|
|----------|--------|
|DigiCert|퍼블릭 클라우드 및 Azure Government의 모든 키 자격 증명 모음 서비스 위치에서 지원됩니다.|
|GlobalSign|퍼블릭 클라우드 및 Azure Government의 모든 키 자격 증명 모음 서비스 위치에서 지원됩니다.|

Key Vault에 인증서 발급자를 만들려면 먼저 다음 필수 조건 1 및 2의 단계를 수행해야 합니다.  

1. CA(인증 기관) 공급자에 등록  

    -   조직 관리자는 하나 이상의 CA 공급자와 함께 자신의 회사(예: Contoso)를 등록해야 합니다.  

2. 管理员为用于注册（和续订） TLS/SSL 证书的 Key Vault 创建申请人凭据  

    -   공급자의 발급자 개체를 만드는 데 사용할 구성을 키 자격 증명 모음에 제공합니다.  

인증서 포털에서 발급자 개체를 만드는 방법에 대한 자세한 내용은 [Key Vault 인증서 블로그](https://aka.ms/kvcertsblog)를 참조하세요.  

Key Vault를 사용하면 서로 다른 발급자 구성으로 여러 발급자 개체를 만들 수 있습니다. 발급자 개체가 만들어지면 하나 또는 여러 개의 증명서 정책에서 해당 이름을 참조할 수 있습니다. 발행자 개체를 참조하면 인증서를 만들고 갱신하는 동안 CA 공급자로부터 x509 인증서를 요청할 때 발행자 개체에서 지정한 대로 구성을 사용하도록 Key Vault에 지시합니다.  

발급자 개체는 자격 증명 모음에 만들어지며, 동일한 자격 증명 모음의 KV 인증서에만 사용할 수 있습니다.  

### <a name="certificate-contacts"></a>인증서 연락처

인증서 연락처에는 인증서 수명 이벤트에서 트리거된 알림을 보내도록 연락처 정보가 포함됩니다. 연락처 정보는 키 자격 증명 모음의 모든 인증서에서 공유합니다. 알림은 키 자격 증명 모음의 모든 인증서에 대한 이벤트에 대해 지정한 모든 연락처로 보내집니다.  

인증서를 자동으로 갱신하도록 정책이 설정되는 경우 알림을 보내는 이벤트는 다음과 같습니다.  

- 인증서 갱신 전
- 인증서 갱신 후 - 인증서가 성공적으로 갱신된 경우에 시작되거나, 오류가 있는 경우 인증서를 수동으로 갱신해야 합니다.  

  인증서를 수동으로(이메일을 통해서만) 갱신하는 정책을 설정하는 경우 인증서를 갱신할 시간이 되면 알림이 전송됩니다.  

### <a name="certificate-access-control"></a>인증서 액세스 제어

 인증서에 대한 액세스 제어는 Key Vault를 통해 관리되고, 해당 인증서를 포함하는 Key Vault를 통해 제공됩니다. 인증서에 대한 액세스 제어 정책은 동일한 Key Vault의 키 및 비밀에 대한 액세스 제어 정책과 다릅니다. 사용자는 인증서를 보관할 하나 이상의 자격 증명 모음을 만들어서 시나리오를 적절하게 세분화하고 인증서를 관리할 수 있습니다.  

 키 자격 증명 모음의 비밀 액세스 제어 항목에서 보안 주체별로 사용할 수 있고 비밀 개체에 허용되는 작업과 매우 비슷한 권한은 다음과 같습니다.  

- 인증서 관리 작업에 필요한 권한
  - *get*: 현재 인증서 버전 또는 모든 인증서 버전 가져오기 
  - *list*: 현재 인증서 또는 인증서 버전 나열  
  - *update*: 인증서 업데이트
  - *create*: Key Vault 인증서 만들기
  - *import*: 인증서 자료를 Key Vault 인증서로 가져오기
  - *delete*: 인증서, 해당 정책 및 모든 해당 버전 삭제  
  - *recover*: 삭제된 인증서 복구
  - *backup*: 인증서를 키 자격 증명 모음에 백업
  - *restore*: 키 자격 증명 모음에 백업된 인증서 복원
  - *managecontacts*: Key Vault 인증서 연락처 관리  
  - *manageissuers*: Key Vault 인증서 기관/발급자 관리
  - *getissuers*: 인증서 기관/발급자 가져오기
  - *listissuers*: 인증서 기관/발급자 나열  
  - *setissuers*: Key Vault 인증서의 기관/발급자 만들기 또는 업데이트  
  - *deleteissuers*: Key Vault 인증서의 기관/발급자 삭제  
 
- 권한 있는 작업에 필요한 권한
  - *purge*: 삭제된 인증서 제거(영구적으로 삭제)

자세한 내용은 [Key Vault REST API 참조에서 인증서 작업](/rest/api/keyvault)을 참조하세요. 권한 설정에 대한 내용은 [자격 증명 모음 - 만들기 또는 업데이트](/rest/api/keyvault/vaults/createorupdate) 및 [자격 증명 모음 - 액세스 정책 업데이트](/rest/api/keyvault/vaults/updateaccesspolicy)를 참조하세요.

## <a name="azure-storage-account-key-management"></a>Azure Storage 계정 키 관리

Key Vault는 Azure Storage 계정 키를 관리할 수 있습니다.

- 내부적으로 Key Vault는 키를 Azure Storage 계정과 함께 나열(동기화)할 수 있습니다. 
- Key Vault는 정기적으로 키를 다시 생성(회전)합니다.
- 키 값은 호출자에게 응답으로 반환되지 않습니다.
- Key Vault는 스토리지 계정과 클래식 스토리지 계정의 키를 모두 관리합니다.

자세한 내용은 [Azure Key Vault 스토리지 계정 키](key-vault-ovw-storage-keys.md)를 참조하세요.

### <a name="storage-account-access-control"></a>스토리지 계정 액세스 제어

다음 권한은 관리되는 스토리지 계정에서 작업을 수행할 사용자 또는 애플리케이션 보안 주체에 권한을 부여할 때 사용할 수 있습니다.  

- 관리되는 스토리지 계정 및 SaS 정의 작업에 대한 권한
  - *get*: 스토리지 계정에 대한 정보 가져오기 
  - *list*: Key Vault에서 관리하는 스토리지 계정 나열
  - *update*: 스토리지 계정 업데이트
  - *delete*: 스토리지 계정 삭제  
  - *recover*: 삭제된 스토리지 계정 복구
  - *backup*: 스토리지 계정 백업
  - *restore*: Key Vault에 백업된 스토리지 계정 복원
  - *set*: 스토리지 계정 만들기 또는 업데이트
  - *regeneratekey*: 스토리지 계정에 대해 지정된 키 값을 다시 생성
  - *getsas*: 스토리지 계정의 SAS 정의에 대한 정보 가져오기
  - *listsas*: 스토리지 계정에 대한 스토리지 SAS 정의 나열
  - *deletesas*: 스토리지 계정에서 SAS 정의 삭제
  - *setsas*: 스토리지 계정에 대한 새 SAS 정의/특성 만들기 또는 업데이트

- 권한 있는 작업에 필요한 권한
  - *제거*: 관리되는 스토리지 계정 제거(영구적으로 삭제)

자세한 내용은 [Key Vault REST API 참조의 스토리지 계정 작업](/rest/api/keyvault)을 참조하세요. 권한 설정에 대한 내용은 [자격 증명 모음 - 만들기 또는 업데이트](/rest/api/keyvault/vaults/createorupdate) 및 [자격 증명 모음 - 액세스 정책 업데이트](/rest/api/keyvault/vaults/updateaccesspolicy)를 참조하세요.

## <a name="see-also"></a>참고 항목

- [인증, 요청 및 응답](authentication-requests-and-responses.md)
- [Key Vault 개발자 가이드](/azure/key-vault/key-vault-developers-guide)
