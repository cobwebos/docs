---
title: 快速入门 - 使用 Azure 应用程序网关配置端到端 SSL 加密 - Azure 门户 | Microsoft Docs
description: 了解如何使用 Azure 门户创建启用端到端 SSL 加密的 Azure 应用程序网关。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a37b313bd808ee0441d84ac92050b087eba7ac9d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097193"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>在门户中使用应用程序网关配置端到端 SSL

本文介绍如何在 Azure 门户中使用应用程序网关 v1 SKU 配置端到端 SSL 加密。  

> [!NOTE]
> 应用程序网关 v2 SKU 需要受信任的根证书才能启用端到端配置。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>开始之前

若要使用应用程序网关配置端到端 SSL，需要为网关使用证书，并要求向后端服务器提供证书。 网关证书用于根据 SSL 协议规范派生对称密钥。 然后，可以使用该对称密钥来加密和解密发送到网关的流量。 对于端到端 SSL 加密，应用程序网关上必须允许使用正确的后端服务器。 为此，请将后端服务器的公共证书（也称为身份验证证书（v1）或受信任的根证书（v2））上传到应用程序网关。 添加证书可确保应用程序网关仅与已知的后端实例通信。 从而进一步保护端到端通信。

有关详细信息，请参阅 [SSL 终止和端到端 SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>创建启用端到端 SSL 的新应用程序网关

若要创建启用端到端 SSL 加密的新应用程序网关，首先需要在创建新应用程序网关时启用 SSL 终止。 这会针对客户端与应用程序网关之间的通信启用 SSL 加密。 然后，需要将 HTTP 设置中后端服务器的证书加入白名单，以针对应用程序网关与后端服务器之间的通信启用 SSL 加密，实现端到端的 SSL 加密。

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>创建新应用程序网关时启用 SSL 终止

请参阅有关如何[在创建新应用程序网关时启用 SSL 终止](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)的文章。

### <a name="add-authenticationroot-certificate-of-back-end-servers"></a>添加后端服务器的身份验证/根证书

1. 选择“所有资源”，然后选择“myAppGateway”。

2. 在左侧菜单中选择“HTTP 设置”。 当你创建应用程序网关时，Azure 自动创建了默认 HTTP 设置 **appGatewayBackendHttpSettings**。 

3. 选择“appGatewayBackendHttpSettings”。

4. 在“协议”下，选择“HTTPS”。 将显示**后端身份验证证书或受信任的根证书**的窗格。 

5. 选择 "**新建**"。

6. 输入合适的**名称**。

7. 使用 "**上载 CER 证书**" 框选择证书文件。

   对于标准版和 WAF （v1）应用程序网关，应将后端服务器证书的公钥上传到 .cer 格式。

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   对于 Standard_v2 和 WAF_v2 应用程序网关，应以 .cer 格式上传后端服务器证书的**根证书**。 如果后端证书由已知 CA 颁发，则可以选中 "使用众所周知的 CA 证书" 框，并且不需要上传证书。

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![rootcert](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. 选择**保存**。

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>为现有的应用程序网关启用端到端 SSL

若要为现有的应用程序网关配置端到端 SSL 加密，首先需要在侦听器中启用 SSL 终止。 这会针对客户端与应用程序网关之间的通信启用 SSL 加密。 然后，需要将 HTTP 设置中后端服务器的证书加入白名单，以针对应用程序网关与后端服务器之间的通信启用 SSL 加密，实现端到端的 SSL 加密。

需要使用一个具有 HTTPS 协议和证书的侦听器来启用 SSL 终止。 因此，可以选择使用具有 HTTPS 协议和证书的现有侦听器，或创建新的侦听器。 如果选择以前的，则可以忽略下述步骤以**在现有应用程序网关上启用 SSL 终止**，并直接移到 "为**后端服务器添加身份验证/受信任的根证书**" 部分。 如果选择后一种做法，请执行这些步骤。

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>在现有应用程序网关中启用 SSL 终止

1. 选择“所有资源”，然后选择“myAppGateway”。

2. 在左侧菜单中选择“侦听器”。

3. 根据要求选择“基本”侦听器或“多站点”侦听器。

4. 在“协议”下，选择“HTTPS”。 随后会显示“证书”窗格。

5. 上传 PFX 证书用于在客户端与应用程序网关之间实现 SSL 终止。

   > [!NOTE]
   > 对于测试，可以使用自签名的证书。 但对于生产工作负荷，不建议这样做，因为这些工作负荷更难以管理，并且不是完全安全的。 了解如何[创建自签名证书](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)。

6. 根据要求添加“侦听器”的其他所需设置。

7. 选择“确定”进行保存。

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>添加后端服务器的身份验证/受信任的根证书

1. 选择“所有资源”，然后选择“myAppGateway”。

2. 在左侧菜单中选择“HTTP 设置”。 可将现有后端 HTTP 设置中的证书加入白名单，或创建新的 HTTP 设置。 以下步骤将默认 HTTP 设置 **appGatewayBackendHttpSettings** 的证书加入白名单。

3. 选择“appGatewayBackendHttpSettings”。

4. 在“协议”下，选择“HTTPS”。 将显示**后端身份验证证书或受信任的根证书**的窗格。 

5. 选择 "**新建**"。

6. 输入适当的**名称**。

7. 使用 "**上载 CER 证书**" 框选择证书文件。

   对于标准版和 WAF （v1）应用程序网关，应将后端服务器证书的公钥上传到 .cer 格式。

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   对于 Standard_v2 和 WAF_v2 应用程序网关，应以 .cer 格式上传后端服务器证书的**根证书**。 如果后端证书由已知 CA 颁发，则可以选中 "使用众所周知的 CA 证书" 框，并且不需要上传证书。

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. 选择**保存**。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过 Azure CLI 使用应用程序网关管理 Web 流量](./tutorial-manage-web-traffic-cli.md)
