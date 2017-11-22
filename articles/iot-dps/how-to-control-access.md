---
title: "IoT 设备预配服务中的安全性终结点 | Microsoft Docs"
description: "概念 - 如何控制后端应用中 IoT 设备预配服务的访问权限。 包括安全令牌的相关信息。"
services: iot-dps
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-dps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: dkshir,rajeevmv
ms.openlocfilehash: 718fe9b3ca449f8f7b1420080ea75716e8badcf5
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>控制对 Azure IoT 中心设备预配服务的访问

本文介绍了用于保护 IoT 设备预配服务的方法。 预配服务使用“权限”向每个终结点授予访问权限。 权限可根据功能限制对服务实例的访问。

本文介绍：

* 可向后端应用授予不同的预配服务访问权限。
* 身份验证过程以及它用于验证权限的令牌。

### <a name="when-to-use"></a>使用时机

要访问某预配服务终结点，必须具有适当的权限。 例如，后端应用必须包含一个带安全证书的令牌，以及每条发送给该服务的消息。

## <a name="access-control-and-permissions"></a>访问控制和权限

可以通过以下方式授予[权限](#device-provisioning-service-permissions)：

* 共享访问授权策略。 共享访问策略可以授予任意[权限](#device-provisioning-service-permissions)组合。 可在 [Azure 门户][lnk-management-portal]中定义策略，也可使用[设备预配服务 REST API][lnk-resource-provider-apis] 以编程方式进行定义。 新建的预配服务有以下默认策略：

  * provisioningserviceowner：包含所有权限的策略。

> [!NOTE]
> 有关详细信息，请参阅[权限](#device-provisioning-service-permissions)。

## <a name="authentication"></a>身份验证

Azure IoT 中心设备预配服务通过针对共享访问策略验证令牌，授予对终结点的访问权限。 安全凭据（例如对称密钥）永远不会通过网络发送。

> [!NOTE]
> 如同 [Azure 资源管理器][lnk-azure-resource-manager]中的所有提供程序一样，设备预配服务资源提供程序也通过 Azure 订阅受到保护。

有关如何构造和使用安全令牌的详细信息，请参阅下一部分。

HTTP 是唯一受支持的协议，它通过在“Authorization”请求标头中包含有效的令牌来实现身份验证。

#### <a name="example"></a>示例
`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`

> [!NOTE]
> [Azure IoT 设备预配服务 SDK][lnk-sdks] 在连接到服务时自动生成令牌。

## <a name="security-tokens"></a>安全令牌
设备预配服务使用安全令牌对服务进行身份验证，以避免在线发送密钥。 并且安全令牌的有效期和范围有限。 [Azure IoT 设备预配服务 SDK][lnk-sdks] 无需任何特殊配置即可自动生成令牌。 在某些情况下，确实需要用户生成并直接使用安全令牌。 这种情况包括直接使用 HTTP。

### <a name="security-token-structure"></a>安全令牌结构

可使用安全令牌向服务授予限时访问 IoT 设备预配服务中特定功能的权限。 要获取连接到预配服务的权限，服务必须发送使用共享访问或对称密钥进行签名的安全令牌。

使用共享访问密钥进行签名的令牌可以授权访问与共享访问策略权限相关的所有功能。 

安全令牌采用以下格式：

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

以下是预期值：

| 值 | 说明 |
| --- | --- |
| {signature} |HMAC-SHA256 签名字符串的格式为：`{URL-encoded-resourceURI} + "\n" + expiry`。 **重要说明**：密钥是从 base64 解码得出的，用作执行 HMAC-SHA256 计算的密钥。|
| {expiry} |从纪元 1970 年 1 月 1日 00:00:00 UTC 时间至今秒数的 UTF8 字符串。 |
| {URL-encoded-resourceURI} | 小写资源 URI 的小写 URL 编码。 此令牌可访问的终结点的 URI 前缀（按分段），以 IoT 设备预配服务的主机名开头（无协议）。 例如，`mydps.azure-devices-provisioning.net`。 |
| {policyName} |此令牌所引用的共享访问策略名称。 |

**有关前缀的说明**：URI 前缀是根据分段而不是字符计算的。 例如，`/a/b` 是 `/a/b/c` 的前缀，而不是 `/a/bc` 的前缀。

以下 Node.js 代码片段显示名为 **generateSasToken** 的函数，该函数通过输入 `resourceUri, signingKey, policyName, expiresInMins` 计算令牌。 以下各节将详细讲解如何初始化不同令牌用例的不同输入。

```nodejs
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

作为对照，用于生成安全令牌的等效 Python 代码是：

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> 由于 IoT 设备预配服务计算机会验证令牌的有效期，因此生成令牌的计算机的时间偏差必须很小。


### <a name="use-security-tokens-from-service-components"></a>使用服务组件提供的安全令牌

如前所述，服务组件使用共享访问策略只能生成安全令牌，授予适当权限。

以下是终结点上显示的服务功能：

| 终结点 | 功能 |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |向设备注册操作提供设备预配服务。 |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |提供用于管理设备注册组的操作。 |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |提供用于检索和管理设备注册状态的操作。 |


例如，使用名为 enrollmentread 的预创建共享访问策略生成的服务将使用以下参数创建令牌：

* 资源 URI：`{mydps}.azure-devices-provisioning.net`，
* 签名密钥：`enrollmentread` 策略的密钥之一，
* 策略名称：`enrollmentread`，
* 任何过期时间。

![在门户中为 DPS 实例创建共享访问策略][img-add-shared-access-policy]

```nodejs
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

授权读取所有注册记录的结果将是：

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>参考主题：

以下参考主题详细删除如何控制对 IoT 设备预配服务的访问。

## <a name="device-provisioning-service-permissions"></a>设备预配服务权限

下表列出了可用于控制对 IoT 设备预配服务的访问的权限。

| 权限 | 说明 |
| --- | --- |
| ServiceConfig |授予用于更改服务配置的访问权限。 <br/>后端云服务将使用此权限。 |
| EnrollmentRead |授予对设备注册和注册组的读取访问权限。 <br/>后端云服务将使用此权限。 |
| EnrollmentWrite |授予对设备注册和注册组的写入访问权限。 <br/>后端云服务将使用此权限。 |
| RegistrationStatusRead |授予对设备注册状态的读取访问权限。 <br/>后端云服务将使用此权限。 |
| RegistrationStatusWrite  |授予对设备注册状态的删除访问权限。 <br/>后端云服务将使用此权限。 |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
