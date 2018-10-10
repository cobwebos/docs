---
title: Azure IoT 中心设备预配服务 - 对称密钥证明
description: 本文以概念的方式概述了使用 IoT 设备预配服务的对称密钥证明。
author: wesmc7777
ms.author: wesmc
ms.date: 08/18/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 80828876ffe8b58697cfaacad4991354ac070730
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971784"
---
# <a name="symmetric-key-attestation"></a>对称密钥证明

本文介绍了在使用含设备预配服务的对称密钥时的标识证明过程。 

对称密钥证明是一种通过设备预配服务实例对设备进行身份验证的简单方法。 此证明方法表示不熟悉设备预配或不具备严格安全要求的开发人员的“Hello world”体验。 使用 [TPM](concepts-tpm-attestation.md) 或 [X.509 证书](concepts-security.md#x509-certificates)的设备证明更加安全，且应用于更严格的安全要求。

对称密钥注册还为具有有限安全功能的旧设备提供了通过 Azure IoT 启动到云的绝佳方法。 有关旧设备的对称密钥证明的详细信息，请参阅[如何为旧设备使用对称密钥](how-to-legacy-device-symm-key.md)。


## <a name="symmetric-key-creation"></a>对称密钥创建

默认情况下，当保存新注册且启用“自动生成密钥”选项时，设备预配服务会创建默认长度为 32 个字节的新对称密钥。

![自动生成对称密钥](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

还可以通过禁用此选项来为注册提供你自己的对称密钥。 在指定你自己的对称密钥时，你的密钥长度必须介于 16 个字节与 64 个字节之间。 此外，对称密钥必须以有效的 Base64 格式提供。



## <a name="detailed-attestation-process"></a>详细证明过程

使用 IoT 中心支持的相同[安全令牌](../iot-hub/iot-hub-devguide-security.md#security-token-structure)来执行含设备预配服务的对称密钥证明，以标识设备。 这些安全令牌都是[共享访问签名 (SAS) 令牌](../service-bus-messaging/service-bus-sas.md)。 

SAS 令牌具有使用对称密钥创建的哈希签名。 设备预配服务会重新创建该签名，以验证在证明期间显示的安全令牌是否可信。

SAS 令牌的格式如下：

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

下面是每个令牌的组成元素：

| 值 | Description |
| --- | --- |
| {signature} |HMAC-SHA256 签名字符串。 对于单个注册，此签名通过使用对称密钥（主密钥或辅助密钥）执行哈希而生成。 对于注册组，从注册组密钥中派生的密钥用于执行哈希。 哈希在以下格式的消息上执行：`URL-encoded-resourceURI + "\n" + expiry`。 **重要说明**：必须先从 base64 解码密钥，然后才能将其用于执行 HMAC-SHA256 计算。 此外，签名结果必须为 URL 编码。 |
| {resourceURI} |以设备预配服务实例的范围 ID 开头、可通过此令牌访问的注册终结点的 URI。 例如： `{Scope ID}/registrations/{Registration ID}` |
| {expiry} |从纪元 1970 年 1 月 1日 00:00:00 UTC 时间至今秒数的 UTF8 字符串。 |
| {URL-encoded-resourceURI} |小写资源 URI 的小写 URL 编码 |
| {policyName} |此令牌所引用的共享访问策略名称。 使用对称密钥证明预配时使用的策略名称是“注册”。 |

当设备使用单个注册进行证明时，设备将使用在单个注册条目中定义的对称密钥创建 SAS 令牌的哈希签名。

有关创建 SAS 令牌的代码示例，请参阅[安全令牌](../iot-hub/iot-hub-devguide-security.md#security-token-structure)。

Azure IoT C SDK 支持创建对称密钥证明的安全令牌。 有关使用 Azure IoT C SDK 来证明单个注册的示例，请参阅[使用对称密钥预配模拟设备](quick-create-simulated-device-symm-key.md)。


## <a name="group-enrollments"></a>组注册

预配时，设备不直接使用组注册的对称密钥。 属于注册组的设备使用派生的设备密钥进行预配。 

首先，为通过注册组进行证明的每个设备定义一个唯一注册 ID。 注册 ID 的有效字符为小写字母数字和短划线（“-”）。 此注册 ID 应是标识设备的唯一 ID。 例如，旧设备可能不支持多种安全功能。 旧设备可能只有可用于唯一标识该设备的 MAC 地址或序列号。 在这种情况下，注册 ID 可由类似于以下内容的 MAC 地址和序列号组成：

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

[如何使用对称密钥预配旧设备](how-to-legacy-device-symm-key.md)一文中使用了该示例。

为设备定义注册 ID 后，注册组的对称密钥用于计算注册 ID 的 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) 哈希，以生成派生的设备密钥。 可使用以下 C# 代码执行注册 ID 的哈希处理：

```C#
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```C#
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

然后使用生成的设备密钥来生成要用于证明的 SAS 令牌。 注册组中的每个设备都需要使用从唯一派生密钥中生成的安全令牌进行证明。 注册组对称密钥不能直接用于证明。

#### <a name="installation-of-the-derived-device-key"></a>派生设备密钥的安装

理想情况下，在中心中派生和安装设备密钥。 此方法可保证不会在部署到设备的任何软件中包含组密钥。 向设备分配 MAC 地址或序列号后，可以派生密钥，并将其注入到设备，而无论制造商选择以何种方式来存储它。

请考虑下图，该图显示了一个设备密钥表，这些设备密钥通过以下方式在中心中生成：通过组注册密钥 (K) 对每个设备注册 ID 进行哈希处理。 

![从中心分配的设备密钥](./media/concepts-symmetric-key-attestation/key-diversification.png)

每个设备的标识由注册 ID 和在中心安装的派生设备密钥表示。 设备密钥不会复制到其他位置，且组密钥不会存储在设备上。

如果未在中心中安装设备密钥，应使用[硬件安全模块 HSM](concepts-security.md#hardware-security-module) 来安全存储设备标识。

## <a name="next-steps"></a>后续步骤

了解对称密钥证明后，请参阅以下文章以了解更多信息：

* [快速入门：使用对称密钥预配模拟设备](quick-create-simulated-device-symm-key.md)
* [了解自动预配中的概念](./concepts-auto-provisioning.md)
* [自动预配入门](./quick-setup-auto-provision.md) 
