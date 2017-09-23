---
title: "使用 URL 路由规则创建应用程序网关 - Azure CLI 2.0 | Microsoft Docs"
description: "本页提供有关如何使用 URL 路由规则创建和配置应用程序网关的说明。"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: c617006bcb122cd3191f5da3ff08191e3c55b04b
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---
# <a name="create-an-application-gateway-by-using-path-based-routing-with-azure-cli-20"></a>使用 Azure CLI 2.0，通过基于路径的路由创建应用程序网关

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

借助基于 URL 路径的路由，可根据 HTTP 请求的 URL 路径来关联路由。 它会检查是否有路由连接到为应用程序网关中列出的 URL 配置的后端服务器池，并将网络流量发送到定义的池。 基于 URL 路径的路由的常见用法是将不同内容类型的请求负载均衡到不同的后端服务器池。

Azure 应用程序网关使用两种规则类型：基本规则和基于 URL 路径的规则。 基本规则类型为后端池提供轮循机制服务。 除了轮循机制分发之外，基于路径的规则还可以使用请求 URL 的路径模式选择适当的后端池。

## <a name="scenario"></a>方案

在以下示例中，应用程序网关使用两个后端服务器池为 contoso.com 提供流量：默认服务器池和映像服务器池。

http://contoso.com/image* 请求将被路由到映像服务器池 (imagesBackendPool)。 如果路径模式不匹配，应用程序网关将选择默认的服务器池 (appGatewayBackendPool)。

![URL 路由](./media/application-gateway-create-url-route-cli/scenario.png)

## <a name="sign-in-to-azure"></a>登录 Azure

打开 Microsoft Azure 命令提示符，并登录：

```azurecli
az login -u "username"
```

> [!NOTE]
> 还可以使用不带开关的 `az login` 进行设备登录，登录时需要在 aka.ms/devicelogin 输入代码。

输入前面的命令后，将收到一个代码。 在浏览器中转到 https://aka.ms/devicelogin，继续登录过程。

![显示设备登录信息的 Cmd][1]

在浏览器中，输入收到的代码。 它会将你重定向到登录页面。

![用于输入代码的浏览器][2]

输入代码进行登录，然后关闭浏览器以继续操作。

![已成功登录][3]

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>将基于路径的规则添加到现有应用程序网关

下列步骤演示了如何将基于路径的规则添加到现有应用程序网关。
### <a name="create-a-new-back-end-pool"></a>创建新后端池

配置应用程序网关设置“imagesBackendPool”以在后端池中实现负载均衡的网络流量。 本示例为新后端池配置不同的后端池设置。 每个后端池都可有自身的设置。 基于路径的规则使用后端 HTTP 设置将流量路由到正确的后端池成员。 将流量发送到后端池成员时，所用的协议和端口由此设置决定。 后端 HTTP 设置还决定了基于 Cookie 的会话。  在启用的情况下，基于 Cookie 的会话相关性会将流量发送到与每个数据包的前述请求相同的后端。

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port-for-an-application-gateway"></a>为应用程序网关创建新的前端端口

侦听器使用前端端口配置对象来定义应用程序网关所侦听的具体端口，以便确定侦听器上的流量。

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>创建新侦听器

此步骤针对用于接收传入网络流量的公共 IP 地址和连接端口配置侦听器。 以下示例使用以前配置的前端 IP 配置、前端端口配置以及协议（http 或 https，区分大小写）对侦听器进行配置。 在此示例中，侦听器侦听端口 82 上的 HTTP 流量，该端口位于此方案之前创建的公共 IP 地址上。

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>创建 URL 路径映射

此步骤配置应用程序网关用于定义路径间映射的相对 URL 路径，以及分配以用于处理传入流量的后端池。

> [!IMPORTANT]
> 每个路径必须以“/”开头，星号只允许放在末尾处。 有效示例包括 /xyz, /xyz* 或 /xyz/*。 发送到路径匹配器的字符串不会在第一个“?”或“#”之后包含任何文本，不允许使用这些字符。 

以下示例针对 /images/* 路径创建一个将流量路由到后端 imagesBackendPool 的规则。 此规则可确保将每个 URL 集的流量路由到后端。 例如，http://adatum.com/images/figure1.jpg 转到 imagesBackendPool。 如果路径不符合任何预定义的路径规则，规则路径映射配置也会配置默认的后端地址池。 例如，http://adatum.com/shoppingcart/test.html 转到 pool1，因为它被定义为非匹配流量的默认池。

```azurecli-interactive
az network application-gateway url-path-map create \
--gateway-name AdatumAppGateway \
--name imagespathmap \
--paths /images/* \
--resource-group myresourcegroup2 \
--address-pool imagesBackendPool \
--default-address-pool appGatewayBackendPool \
--default-http-settings appGatewayBackendHttpSettings \
--http-settings appGatewayBackendHttpSettings \
--rule-name images
```

## <a name="next-steps"></a>后续步骤

如果要了解安全套接字层 (SSL) 卸载，请参阅[配置应用程序网关以进行 SSL 卸载](application-gateway-ssl-cli.md)。


[scenario]: ./media/application-gateway-create-url-route-cli/scenario.png
[1]: ./media/application-gateway-create-url-route-cli/figure1.png
[2]: ./media/application-gateway-create-url-route-cli/figure2.png
[3]: ./media/application-gateway-create-url-route-cli/figure3.png

