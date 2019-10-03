---
title: 续订 Azure 应用程序网关证书
description: 了解如何续订与应用程序网关侦听器关联的证书。
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: 90200f7be6c71346441922365fc4439111dd8701
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66133698"
---
# <a name="renew-application-gateway-certificates"></a>续订应用程序网关证书

在某些时候，如果为 SSL 加密配置了应用程序网关，则需要续订证书。

可以使用 Azure 门户、Azure PowerShell 或 Azure CLI 续订与侦听器关联的证书：

## <a name="azure-portal"></a>Azure 门户

若要从门户续订侦听器证书，请导航到应用程序网关侦听器。 单击具有需要续订的证书的侦听器，然后单击“续订或编辑所选证书”  。

![续订证书](media/renew-certificate/ssl-cert.png)

上传新的 PFX 证书，为其命名，输入密码，然后单击“保存”  。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 续订证书，请使用以下脚本：

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>后续步骤

若要了解如何通过 Azure 应用程序网关来配置 SSL 卸载，请参阅[配置 SSL 卸载](application-gateway-ssl-portal.md)
