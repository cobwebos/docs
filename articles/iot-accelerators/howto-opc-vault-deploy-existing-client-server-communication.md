---
title: 保护 OPC UA 客户端和 OPC UA 服务器应用程序使用 OPC 保管库-Azure |Microsoft Docs
description: 保护 OPC UA 客户端和 OPC UA 服务器使用新的密钥对和证书使用 OPC 保管库的应用程序。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 5ba2dba02585598b3797dd1b490976ebe34b489e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61450658"
---
# <a name="secure-opc-ua-client-and-opc-ua-server-application"></a>保护 OPC UA 客户端和 OPC UA 服务器应用程序 
OPC 保管库是可以配置、注册和管理云中 OPC UA 服务器与客户端应用程序的证书生命周期的微服务。 本文介绍如何保护 OPC UA 客户端和 OPC UA 服务器使用新的密钥对和证书使用 OPC 保管库的应用程序。

在以下设置中，OPC 客户端测试与 OPC PLC 连接性。 默认情况下，连接是不可能因为这两个组件具有尚未设置的正确证书。 在此工作流，我们不要使用 OPC UA 组件自签名的证书和签名通过 OPC 保管库。 请参阅前面[测试床](howto-opc-vault-deploy-existing-client-plc-communication.md)。 相反，此测试床预配都由 OPC 保管库生成的组件与新的证书以及新的私钥。 OPC UA 安全的一些背景信息可在这[白皮书](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf)。 可以在 OPC UA 规范中找到完整的信息。

测试床：以下环境已配置为用于测试。

OPC 保管库的脚本：
- 保护 OPC UA 客户端和 OPC UA 服务器使用新的密钥对和证书使用 OPC 保管库的应用程序。

> [!NOTE]
> 有关详细信息，请参阅 GitHub[存储库](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds)。

## <a name="generate-a-new-certificate-and-private-key"></a>生成新的证书和私钥 
**准备工作**
- 确保环境变量`$env:_PLC_OPT`和`$env:_CLIENT_OPT`是不确定。 例如，`$env:_PLC_OPT=""`中你的 PowerShell
- 设置环境变量`$env:_OPCVAULTID`为一个字符串，使你能够再次 OPC 保管库中找到你的数据。 我们建议将其设置为 6 位数字。 对于本示例中，"123456"已用作值的变量。
- 确保没有 docker 卷`opcclient`或`opcplc`。 请咨询`docker volume ls`并将其与删除`docker volume rm <volumename>`。 可能需要删除还包含容器`docker rm <containerid>`如果卷仍由容器。

**快速入门**
1. 转到[OPC 保管库网站](https://opcvault.azurewebsites.net/)

1. 选择`Register New`

1. 输入 OPC PLC 信息，因为它已显示在上一个测试床日志输出`CreateSigningRequest information`上相应的输入字段中的区域`Register New OPC UA Application`页上，选择`Server`为 ApplicationType。

1. 选择`Register`

1. 在下一页上，`Request New Certificate for OPC UA Application`选择 `Request new KeyPair and Certificate`

1. 在下一页上，`Generate a new Certificate with a Signing Request`粘贴`CSR (base64 encoded)`中的日志输出到字符串`CreateRequest`输入的字段。 请确保复制的完整字符串。

1. 在下一页上，`Request New Certificate for OPC UA Application`选择 `Request new Certificate with Signing Request`

1. 在下一页上`Generate a new KeyPair and for an OPC UA Application`输入`CN=OpcPlc`SubjectName，作为`opcplc-<_OPCVAULTID>`(替换`<_OPCVAULTID>`自己) 作为 DomainName，选择`PEM`PrivateKeyFormat 作为，并输入密码 (我们之所以将其作为更高版本`<certpassword-string>`)

1. 选择`Generate New KeyPair`

1. 您现在向前移动到`View Certificate Request Details`。 在此页上，你可以下载所需的所有信息来预配证书存储区的`opc-plc`。

1. 在此页上：  
    - 选择`Certificate`中`Download as Base64`中显示的文本字符串的副本和`EncodedBase64`字段并将其存储以供将来使用。 我们之所以将其作为`<applicationcertbase64-string>`更高版本上。 选择 `Back`。
    - 选择`PrivateKey`中`Download as Base64`中显示的文本字符串的副本和`EncodedBase64`字段并将其存储以供将来使用。 我们之所以将其作为`<privatekeybase64-string>`更高版本上。 选择 `Back`。
    - 选择`Issuer`中`Download as Base64`中显示的文本字符串的副本和`EncodedBase64`字段并将其存储以供将来使用。 我们之所以将其作为`<addissuercertbase64-string>`更高版本上。 选择 `Back`。
    - 选择`Crl`中`Download as Base64`中显示的文本字符串的副本和`EncodedBase64`字段并将其存储以供将来使用。 我们之所以将其作为`<updatecrlbase64-string>`更高版本上。 选择 `Back`。

1. 现在你的 PowerShell 中设置一个名为变量`$env:_PLC_OPT`:

   `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --privatekeybase64 <privatekeybase64-string> --certpassword <certpassword-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  

    将为来自网站提取选项值 Base64 字符串形式传递的字符串。  

1. 重复开头的完整过程`Register New`OPC 客户端。 有只需要注意以下差异：
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

**确认**

验证两个组件未发生的现有应用程序证书。 检查日志输出。 以下是 OPC PLC 的输出和 OPC 客户端有类似的日志输出。

```
opcplc-123456 | [13:40:08 INF] There is no existing application certificate.
```
如果应用程序证书，删除 docker 卷，在准备步骤中所述。

在日志中验证颁发者证书存储以及如下所示的受信任的对等证书存储区中已安装 OPC 保管库 CA 证书。 下面是 OPC PLC 的日志输出和 OPC 客户端有类似的日志输出。 

```
opcplc-123456 | [13:40:09 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Trusted peer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Rejected certificate store contains 0 certs
```
现在，OPC PLC does 与签名由 OPC 保管库的证书信任所有 OPC UA 客户端。

验证日志中的私钥格式被识别为 PEM 和安装了新的应用程序证书。 下面是 OPC PLC 的日志输出和 OPC 客户端有类似的日志输出。 

```
opcplc-123456 | [13:40:09 INF] The private key for the new certificate was passed in using PEM format.
opcplc-123456 | [13:40:09 INF] Remove the existing application certificate.
opcplc-123456 | [13:40:09 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C' was added to the application certificate store.
opcplc-123456 | [13:40:09 INF] Activating the new application certificate with thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C'.
```

应用程序证书和私钥现在安装应用程序证书存储中并由 OPC UA 应用程序。

验证可以成功建立连接 OPC 客户端与 OPC PLC 和 OPC 客户端可以成功地从 OPC PLC 读取数据。 应看到以下输出的 OPC 客户端日志输出：
```
opcclient-123456 | [13:40:12 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [13:40:12 INF] Session successfully created with Id ns=3;i=941910499.
opcclient-123456 | [13:40:12 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [13:40:12 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [13:40:12 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [13:40:12 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [13:40:12 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [13:40:12 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [13:40:12 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [13:40:12 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [13:40:12 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/21/2018 13:40:12
```
如果看到此输出，然后 OPC PLC 现在信任 OPC 客户端，反之亦然，因为都有现在由 CA 和这两个信任证书所签名的此 CA 签名的证书。

### <a name="a-testbed-for-opc-publisher"></a>OPC 发布服务器的测试床 ###

**快速入门**

在存储库的根目录中运行以下 PowerShell 命令：
```
docker-compose -f testbed.yml up
```

**确认**
- 验证数据将发送到 iot 中心，通过设置来配置`_HUB_CS`使用[Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)或[iothub explorer](https://github.com/Azure/iothub-explorer)。
- OPC 测试客户端将要使用 iot 中心直接方法调用和 OPC 方法调用来配置 OPC 发布服务器以发布/取消发布从 OPC 测试服务器中的节点。
- 监视输出的错误消息。

## <a name="next-steps"></a>后续步骤

现在，已了解如何将 OPC 保管库部署到现有项目，下面是建议的下一步：

> [!div class="nextstepaction"]
> [将 OPC 孪生部署到现有项目](howto-opc-twin-deploy-existing.md)