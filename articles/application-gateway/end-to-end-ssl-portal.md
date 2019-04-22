---
title: 快速入门-使用 Azure 应用程序网关-Azure 门户中配置端到端 SSL 加密 |Microsoft Docs
description: 了解如何使用 Azure 门户创建端到端 SSL 加密的 Azure 应用程序网关。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: e47a3e1231701f3339057e25ee4388aff0c9fbd7
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227540"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>使用门户中使用应用程序网关配置端到端 SSL

本文介绍如何使用 Azure 门户对应用程序网关配置端到端 SSL 加密 v1 SKU。  

> [!NOTE]
> 应用程序网关 v2 SKU 需要启用端到端配置的受信任的根证书。 门户支持添加受信任的根证书尚不可用。 因此，发生 V2 SKU 时，请参阅[配置使用 PowerShell 的端到端 SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>开始之前

若要对应用程序网关配置端到端 SSL，需要网关证书和后端服务器证书。 网关证书用于根据 SSL 协议规范派生对称密钥。 然后使用对称密钥进行加密和解密发送到网关的流量。 若要加密端到端 SSL，后端必须已加入应用程序网关的允许列表。 若要执行此操作，上载到应用程序网关后端服务器，也称为身份验证证书的公用证书。 添加证书后，可确保应用程序网关仅与已知后端实例通信。 从而进一步保护端到端通信。

若要了解详细信息，请参阅[SSL 终止和端到端 SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>使用端到端 SSL 创建新的应用程序网关

若要创建新的应用程序网关使用端到端 SSL 加密，将需要首先启用时创建新的应用程序网关的 SSL 终止。 这将使客户端和应用程序网关之间的通信的 SSL 加密。 然后，将需要列入允许列表作为后端 HTTP 设置中的服务器证书启用应用程序网关和后端服务器，从而完成端到端 SSL 加密之间的通信的 SSL 加密。

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>启用 SSL 终止时创建新的应用程序网关

请参阅此文章以了解如何[启用时创建新的应用程序网关的 SSL 终止](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)。

### <a name="whitelist-certificates-for-backend-servers"></a>允许列表后端服务器的证书

1. 选择“所有资源”，然后选择“myAppGateway”。

2. 选择**HTTP 设置**从左侧菜单中。 Azure 已自动创建默认 HTTP 设置， **appGatewayBackendHttpSettings**，当你创建应用程序网关。 

3. 选择**appGatewayBackendHttpSettings**。

4. 下**协议**，选择**HTTPS**。 为窗格**后端身份验证证书**将出现。 

5. 下**后端身份验证证书**，选择**新建**。

6. 输入合适**名称**。

7. 使用证书**上传 CER 证书**框。![addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > 此步骤中提供的证书应该是后端中存在的 .pfx 证书的公钥。 以索赔、证据和推理 (CER) 格式导出后端服务器上安装的证书（不是根证书），将其用在此步骤。 此步骤会将后端加入应用程序网关的允许列表。

8. 选择“保存”。

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>启用现有应用程序网关的端到端 SSL

若要使用的端到端 SSL 加密配置现有的应用程序网关，将需要在侦听器中的第一个启用 SSL 终止。 这将使客户端和应用程序网关之间的通信的 SSL 加密。 然后，将需要列入允许列表作为后端 HTTP 设置中的服务器证书启用应用程序网关和后端服务器，从而完成端到端 SSL 加密之间的通信的 SSL 加密。

需要使用 HTTPS 协议和证书用于启用 SSL 终止的侦听器。 不能更改现有侦听器的协议。 因此，您可以选择使用现有侦听器使用 HTTPS 协议和证书，或创建一个新的侦听器。 如果选择前者，则可以忽略下面所述的步骤**启用 SSL 终止现有应用程序网关中**并直接移至**后端服务器的允许列表证书**部分。 如果您选择后者，执行这些步骤。 

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>启用现有应用程序网关的 SSL 终止

1. 选择“所有资源”，然后选择“myAppGateway”。

2. 选择**侦听器**从左侧菜单中。

3. 之间进行选择**基本**并**多站点**具体请根据要求的侦听器。

4. 下**协议**，选择**HTTPS**。 为窗格**证书**将出现。

5. 想要使用客户端和应用程序网关之间的 SSL 终端的 PFX 证书上传。

   > [!NOTE]
   > 出于测试目的，可以使用自签名的证书。 不应为生产工作负荷使用自签名的证书。 了解如何[创建自签名的证书](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)。

6. 添加其他所需的设置**侦听器**根据你的要求。

7. 选择“确定”进行保存。

### <a name="whitelist-certificates-for-backend-servers"></a>允许列表后端服务器的证书

1. 选择“所有资源”，然后选择“myAppGateway”。

2. 选择**HTTP 设置**从左侧菜单中。 可以是允许列表证书中的现有后端 HTTP 设置或创建新的 HTTP 设置。 在下面步骤中，我们将默认 HTTP 设置的允许列表证书**appGatewayBackendHttpSettings**。

3. 选择**appGatewayBackendHttpSettings**。

4. 下**协议**，选择**HTTPS**。 为窗格**后端身份验证证书**将出现。 

5. 下**后端身份验证证书**，选择**新建**。

6. 输入合适**名称**。

7. 使用证书**上传 CER 证书**框。![addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > 此步骤中提供的证书应该是后端中存在的 .pfx 证书的公钥。 以索赔、证据和推理 (CER) 格式导出后端服务器上安装的证书（不是根证书），将其用在此步骤。 此步骤会将后端加入应用程序网关的允许列表。

8. 选择“保存”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过 Azure CLI 使用应用程序网关管理 Web 流量](./tutorial-manage-web-traffic-cli.md)
