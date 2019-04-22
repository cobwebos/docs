---
title: 保护通信的 OPC 客户端和 OPC PLC 与 OPC 保管库-Azure |Microsoft Docs
description: 通过登录他们使用 OPC 保管库 CA 的证书保护的 OPC 客户端和 OPC PLC 的通信。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 30eedd982fa0536ce45506c159de6d04132e9a14
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59493979"
---
# <a name="secure-the-communication-of-opc-client-and-opc-plc"></a>保护 OPC 客户端和 OPC PLC 的通信

OPC 保管库是一个微服务，可以配置、 注册和管理证书生命周期的 OPC UA 服务器和客户端应用程序在云中。 本文介绍如何通过签名他们使用 OPC 保管库 CA 的证书保护的 OPC 客户端和 OPC PLC 的通信。

在以下设置中，OPC 客户端测试与 OPC PLC 的连接。 默认情况下，连接是不可能因为这两个组件未配有正确的证书。 如果使用证书的 OPC UA 组件未尚未设置，它将生成在启动时的自签名的证书。 但是，可以由证书颁发机构 (CA) 签署的证书，将其安装在 OPC UA 组件。 这是 OPC 客户端和 OPC PLC 后，启用连接。 下面的工作流描述的过程。 OPC UA 安全的一些背景信息可在[本文档](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf)白皮书。 可以在 OPC UA 规范中找到完整的信息。

测试床：以下环境已配置为用于测试。

OPC 保管库的脚本：
- 签名他们使用 OPC 保管库 CA 的证书保护的 OPC 客户端和 OPC PLC 的通信。

> [!NOTE]
> 有关详细信息，请参阅 GitHub[存储库](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds)。

## <a name="generate-a-self-signed-certificate-on-startup"></a>生成自签名的证书在启动时

**准备工作**

- 确保环境变量`$env:_PLC_OPT`并`$env:_CLIENT_OPT`是不确定，例如，`$env:_PLC_OPT=""`在 PowerShell 中。

- 设置环境变量`$env:_OPCVAULTID`为一个字符串，使你能够再次 OPC 保管库中找到你的数据。 允许仅字母数字字符。 本示例中，为"123456"作为值使用此变量。

- 确保没有 docker 卷`opcclient`或`opcplc`。 请咨询`docker volume ls`并将其与删除`docker volume rm <volumename>`。 可能需要删除还包含容器`docker rm <containerid>`如果卷仍由容器。

**快速入门**

在存储库的根目录中运行以下 PowerShell 命令：

```
docker-compose -f connecttest.yml up
```

**确认**

验证日志中有安装的第一个在没有证书。 此处 OPC PLC （类似显示 OPC 客户端注册） 的日志输出:...
```
opcplc-123456 | [20:51:32 INF] Trusted issuer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted issuer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Trusted peer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted peer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Rejected certificate store contains 0 certs
```
如果您看到报告的证书，请按照上述准备步骤并删除 docker 卷。

验证连接到 OPC PLC 已失败。 应看到以下输出的 OPC 客户端日志输出：

```
opcclient-123456 | [20:51:35 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:51:36 ERR] Session creation to endpoint 'opc.tcp://opcplc-123456:50000/' failed 1 time(s). Please verify if server is up and OpcClient configuration is correct.
opcclient-123456 | Opc.Ua.ServiceResultException: Certificate is not trusted.
```
失败的原因是该证书不受信任。 这意味着`opc-client`尝试连接到`opc-plc`，并收到响应，指示`opc-plc`不信任`opc-client`的结果`opc-plc`无法验证证书的`opc-client`已提供。 这是无需进一步的证书配置自签名的证书上`opc-plc`，因此连接失败。

## <a name="sign-and-install-certificates-in-opc-ua-components"></a>登录并在 OPC UA 组件中安装证书

**准备工作**
1. 查看步骤 1 的日志输出，提取"CreateSigningRequest 信息"OPC PLC 和 OPC 客户端。 下面的输出仅显示 OPC PLC 的：

    ```
    opcplc-123456 | [20:51:32 INF] ----------------------- CreateSigningRequest information ------------------
    opcplc-123456 | [20:51:32 INF] ApplicationUri: urn:OpcPlc:opcplc-123456
    opcplc-123456 | [20:51:32 INF] ApplicationName: OpcPlc
    opcplc-123456 | [20:51:32 INF] ApplicationType: Server
    opcplc-123456 | [20:51:32 INF] ProductUri: https://github.com/azure-samples/iot-edge-opc-plc
    opcplc-123456 | [20:51:32 INF] DiscoveryUrl[0]: opc.tcp://opcplc-123456:50000
    opcplc-123456 | [20:51:32 INF] ServerCapabilities: DA
    opcplc-123456 | [20:51:32 INF] CSR (base64 encoded):
    opcplc-123456 | MIICmzCCAYMCAQAwETEPMA0GA1UEAwwGT3BjUGxjMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwTvlbinAPWPxR9Lw1ndGsrLGy8GiqVOxyGaDpPUcMchX0k0/ncg28h7xrB2H1PThdiZxUJuUwsNM74HrVgt
    ofmXhA4dLM1cTxZHyJVFjl2L3vK5M58NNf6UNdKcB0x3LyuoT6mAIMXmCioqymFCk1TMzIMzbAe7JVAdUaSRBP1vuqQ1rV/cfNAe35dKQW4aPYgl7pR5f1hqprcDu/oca67X8L4kTl4oN0/bCYTk+Ibcd9cG462oAN+bSwbHn8a2jNky8rGsofA
    o9DOT+0ALPhk6CApCYIP2yxoI/kT188eqUUxzAFF9nyU79AyCkpGPuY8DGMyf56pDofgtGpfY3wQIDAQABoEUwQwYJKoZIhvcNAQkOMTYwNDAyBgNVHREEKzAphhh1cm46T3BjUGxjOm9wY3BsYy0xMjM0NTaCDW9wY3BsYy0xMjM0NTYwDQYJK
    oZIhvcNAQELBQADggEBAAsZLoOLzS2VhDcQRu0QhRbG7CGAxX19l7fDCG2WjU7lTFnCvYVTWTYyaY61ljmrWc7IbCaQdMJM8GRnAnvAzUh/PBDxkOX7NqI2+8F1yQOHgs/AfKuppOd6DIP8EzFAHnc0H85jay6zFdmIDWoWwpy0ACqOVooOTKST
    7uty0mT87bj8Cdy1yf4mvBNQx+nsuTbKgxWCBxGYAyg9dIL2uKL0aeB/ROW5Gkelz5sCEzQ1fFDokUA4oC5QiATQBN3cY7EmvRbPgdToY7CpRN3iiO7J+7bC7BP9YKfuE34E8xOFpskHPHAPf3r002/L0S67HyuVSXLUj1+Jc0LeAAF9Bw0=
    opcplc-123456 | [20:51:32 INF] ---------------------------------------------------------------------------
    ```
    
1. 转到[OPC 保管库网站](https://opcvault.azurewebsites.net/)。

1. 选择 `Register New`

1. 从日志输出输入 OPC PLC 信息`CreateSigningRequest information`上相应的输入字段中的区域`Register New OPC UA Application`页上，选择`Server`为 ApplicationType。

1. 选择 `Register`。

1. 在下一页上，`Request New Certificate for OPC UA Application`选择`Request new Certificate with Signing Request`。

1. 在下一页上，`Generate a new Certificate with a Signing Request`粘贴`CSR (base64 encoded)`中的日志输出到字符串`CreateRequest`输入的字段。 请确保复制的完整字符串。

1. 选择 `Generate New Certificate`。

1. 您现在向前移动到`View Certificate Request Details`。 在此页上，你可以下载所需的所有信息来预配证书存储区的`opc-plc`。

1. 在此页上：  
    - 选择`Certificate`中`Download as Base64`中显示的文本字符串的副本和`EncodedBase64`字段并将其存储以供将来使用。 我们之所以将其作为`<applicationcertbase64-string>`更高版本上。 选择 `Back`。

    - 选择`Issuer`中`Download as Base64`中显示的文本字符串的副本和`EncodedBase64`字段并将其存储以供将来使用。 我们之所以将其作为`<addissuercertbase64-string>`更高版本上。 选择 `Back`。

    - 选择`Crl`中`Download as Base64`中显示的文本字符串的副本和`EncodedBase64`字段并将其存储以供将来使用。 我们之所以将其作为`<updatecrlbase64-string>`更高版本上。 选择 `Back`。

1. 现在你的 PowerShell 中设置一个名为变量`$env:_PLC_OPT`:

    ```
    `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  
    ```
    > [!NOTE] 
    > 将为来自网站提取选项值 Base64 字符串形式传递的字符串。

重复开头的完整过程`Register New`（上面的步骤 3） 对于 OPC 客户端。 有只需要注意以下差异：

- 使用的日志输出`opcclient`。
- 选择`Client`为 ApplicationType 在注册过程。
- 使用`$env:_CLIENT_OPT`作为 PowerShell 变量的名称。

> [!NOTE]
> 同时使用这种情况下，你可能已识别出`<addissuercertbase64-string>`并`<updatecrlbase64-string>`是相同的值`opcplc`和`opcclient`。 这是针对我们的用例，则返回 true，可以节省一些时间时执行的步骤。

**快速入门**

在存储库的根目录中运行以下 PowerShell 命令：

```
docker-compose -f connecttest.yml up
```

**验证**验证两个组件现在具有签名应用程序证书。 检查以下输出的日志输出：

```
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Starting to update the current CRL.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted peer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted issuer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Start updating the current application certificate.
opcplc-123456 | [20:54:38 INF] The current application certificate has SubjectName 'CN=OpcPlc' and thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] Remove the existing application certificate with thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' was added to the application certificate store.
opcplc-123456 | [20:54:39 INF] Activating the new application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586'.
opcplc-123456 | [20:54:39 INF] Application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' found in the application certificate store.
opcplc-123456 | [20:54:39 INF] Application certificate is for ApplicationUri 'urn:OpcPlc:opcplc-123456', ApplicationName 'OpcPlc' and Subject is 'OpcPlc'
 ```
应用程序证书存在，由 CA 签名。

验证日志中已安装的证书。 下面是 OPC PLC 的日志输出和 OPC 客户端有类似的输出。
```
opcplc-123456 | [20:54:39 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Trusted peer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Rejected certificate store contains 0 certs
```
应用程序证书的颁发者是 CA`CN=Azure IoT OPC Vault CA, O=Microsoft Corp.`和 OPC PLC 信任此 CA 的签名还所有的证书。


验证已成功创建连接到 OPC PLC 和 OPC 客户端可以从 OPC PLC 读取数据。 应看到以下输出的 OPC 客户端日志输出：
```
opcclient-123456 | [20:54:42 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:54:42 INF] Session successfully created with Id ns=3;i=1085867946.
opcclient-123456 | [20:54:42 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [20:54:42 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [20:54:42 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [20:54:42 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [20:54:42 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [20:54:42 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [20:54:42 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [20:54:42 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [20:54:42 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/20/2018 20:54:42
```
如果看到以下输出，则 OPC PLC 是现在由于都有现在由 CA 签名的证书和信任证书的位置信任 OPC 客户端，反之亦然，由该 CA 签名。

> [!NOTE] 
> 尽管我们仅为 OPC PLC 介绍了第一个的两个验证步骤，但那些需要为 OPC 客户端还验证。


## <a name="next-steps"></a>后续步骤

现在，已了解如何将 OPC 保管库部署到现有项目，下面是建议的下一步：

> [!div class="nextstepaction"]
> [将 OPC 孪生部署到现有项目](howto-opc-twin-deploy-existing.md)