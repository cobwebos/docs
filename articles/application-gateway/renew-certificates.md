---
title: 续订 Azure 应用程序网关证书
description: 了解如何续订与应用程序网关侦听器关联的证书。
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/17/2018
ms.author: victorh
ms.openlocfilehash: b125f707e8de17764701e981736a53492e5e756c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356931"
---
# <a name="renew-application-gateway-certificates"></a>续订应用程序网关证书

在某些时候，如果为 SSL 加密配置了应用程序网关，则需要续订证书。

可以使用 Azure 门户或 Azure PowerShell 续订与侦听器关联的证书：

## <a name="azure-portal"></a>Azure 门户

若要从门户续订侦听器证书，请导航到应用程序网关侦听器。 单击具有需要续订的证书的侦听器，然后单击“续订或编辑所选证书”。

![续订证书](media/renew-certificate/ssl-cert.png)

上传新的 PFX 证书，为其命名，输入密码，然后单击“保存”。

## <a name="azure-powershell"></a>Azure PowerShell

若要使用 Azure PowerShell 续订证书，请使用以下 cmdlet：

```PowerShell
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-azureRmApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password
```

## <a name="next-steps"></a>后续步骤

若要了解如何通过 Azure 应用程序网关来配置 SSL 卸载，请参阅[配置 SSL 卸载](application-gateway-ssl-portal.md)
