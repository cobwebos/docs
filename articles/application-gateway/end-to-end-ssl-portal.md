---
title: 快速入门 - 使用 Azure 应用程序网关配置端到端 SSL 加密 - Azure 门户 | Microsoft Docs
description: 了解如何使用 Azure 门户创建具有端到端 SSL 加密的应用程序网关。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: ba31b5ebf83edcd08060a2acc3b5639a521e2729
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243668"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>在门户中使用应用程序网关配置端到端 SSL

本文介绍如何使用 Azure 门户通过 Azure 应用程序网关 v1 SKU 来配置端到端安全套接字层（SSL）加密。

> [!NOTE]
> 应用程序网关 v2 SKU 需要受信任的根证书才能启用端到端配置。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>开始之前

若要使用应用程序网关配置端到端 SSL，需要网关的证书。 后端服务器也需要证书。 网关证书用于派生对称密钥，符合 SSL 协议规范。 然后，可以使用该对称密钥来加密和解密发送到网关的流量。 

对于端到端 SSL 加密，应用程序网关上必须允许使用正确的后端服务器。 若要允许此访问，请将后端服务器的公共证书（也称为身份验证证书（v1）或受信任的根证书（v2））上传到应用程序网关。 添加证书可确保应用程序网关仅与已知的后端实例通信。 此配置进一步保护端到端通信。

有关详细信息，请参阅 [SSL 终止和端到端 SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>创建启用端到端 SSL 的新应用程序网关

若要创建启用端到端 SSL 加密的新应用程序网关，首先需要在创建新应用程序网关时启用 SSL 终止。 此操作为客户端和应用程序网关之间的通信启用 SSL 加密。 然后，需要将后端服务器的证书放在 "安全收件人" 列表中。 此配置支持对应用程序网关和后端服务器之间的通信进行 SSL 加密。 完成端到端 SSL 加密。

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>创建新应用程序网关时启用 SSL 终止

若要了解详细信息，请参阅[在创建新的应用程序网关时启用 SSL 终止](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)。

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>添加后端服务器的身份验证/根证书

1. 选择“所有资源”，然后选择“myAppGateway”。

2. 从左侧菜单中选择 " **HTTP 设置**"。 当你创建应用程序网关时，Azure 自动创建了默认 HTTP 设置 **appGatewayBackendHttpSettings**。 

3. 选择“appGatewayBackendHttpSettings”。

4. 在“协议”下，选择“HTTPS”。 随即出现 "**后端身份验证证书" 或 "受信任的根证书**" 窗格。

5. 选择“新建”。

6. 在 "**名称**" 字段中，输入合适的名称。

7. 在 "**上载 CER 证书**" 框中选择证书文件。

   对于标准版和 WAF （v1）应用程序网关，应将后端服务器证书的公钥上传到 .cer 格式。

   ![添加证书](./media/end-to-end-ssl-portal/addcert.png)

   对于 Standard_v2 和 WAF_v2 应用程序网关，应以 .cer 格式上传后端服务器证书的根证书。 如果后端证书由知名证书颁发机构（CA）颁发，则可以选中 "使用众所周知的**CA 证书**" 复选框，然后无需上载证书。

   ![添加受信任的根证书](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![根证书](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. 选择“保存”。

## <a name="enable-end-to-end-ssl-for-an-existing-application-gateway"></a>为现有应用程序网关启用端到端 SSL

若要配置具有端到端 SSL 加密的现有应用程序网关，必须先在侦听器中启用 SSL 终止。 此操作为客户端和应用程序网关之间的通信启用 SSL 加密。 然后，将这些证书放置在 "安全收件人" 列表中的 "HTTP 设置" 中。 此配置支持对应用程序网关和后端服务器之间的通信进行 SSL 加密。 完成端到端 SSL 加密。

需要使用带有 HTTPS 协议的侦听器和用于启用 SSL 终止的证书。 您可以使用满足这些条件的现有侦听器，也可以创建新的侦听器。 如果选择以前的选项，则可以忽略以下 "在现有应用程序网关中启用 SSL 终止" 部分，并直接转到 "为后端服务器添加身份验证/受信任的根证书" 部分。

如果选择后一个选项，请应用以下过程中的步骤。
### <a name="enable-ssl-termination-in-an-existing-application-gateway"></a>在现有应用程序网关上启用 SSL 终止

1. 选择“所有资源”，然后选择“myAppGateway”。

2. 从左侧菜单中选择 "**侦听器**"。

3. 根据要求选择 "**基本**" 或 "**多站点**" 侦听器。

4. 在“协议”下，选择“HTTPS”。 此时将显示**证书**窗格。

5. 上传要用于客户端和应用程序网关之间的 SSL 终止的 PFX 证书。

   > [!NOTE]
   > 对于测试，可以使用自签名的证书。 不过，对于生产工作负荷，不建议这样做，因为这不是完全安全的。 有关详细信息，请参阅[创建自签名证书](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)。

6. 根据你的要求，为**侦听器**添加其他所需的设置。

7. 选择“确定”进行保存。

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>添加后端服务器的身份验证/受信任的根证书

1. 选择“所有资源”，然后选择“myAppGateway”。

2. 从左侧菜单中选择 " **HTTP 设置**"。 可以在 "安全收件人" 列表中的现有后端 HTTP 设置中放置证书，或创建新的 HTTP 设置。 （在下一步中，默认 HTTP 设置**appGatewayBackendHttpSettings**的证书将添加到 "安全收件人" 列表中。）

3. 选择“appGatewayBackendHttpSettings”。

4. 在“协议”下，选择“HTTPS”。 随即出现 "**后端身份验证证书" 或 "受信任的根证书**" 窗格。 

5. 选择“新建”。

6. 在 "**名称**" 字段中，输入合适的名称。

7. 在 "**上载 CER 证书**" 框中选择证书文件。

   对于标准版和 WAF （v1）应用程序网关，应将后端服务器证书的公钥上传到 .cer 格式。

   ![添加证书](./media/end-to-end-ssl-portal/addcert.png)

   对于 Standard_v2 和 WAF_v2 应用程序网关，应以 .cer 格式上传后端服务器证书的根证书。 如果后端证书由已知 CA 颁发，则可以选中 "使用众所周知的**CA 证书**" 复选框，然后无需上载证书。

   ![添加受信任的根证书](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. 选择“保存”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过 Azure CLI 使用应用程序网关管理 Web 流量](./tutorial-manage-web-traffic-cli.md)
