---
title: 在 Azure Cloud Shell 中使用 MSI | Microsoft Docs
description: 在 Azure Cloud Shell 中使用 MSI 对代码进行身份验证
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: juluk
ms.openlocfilehash: 99577faf7328dc773a9da5f7c1227aa63600aa0a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="use-msi-in-azure-cloud-shell"></a>在 Azure Cloud Shell 中使用 MSI

Azure Cloud Shell 支持使用托管服务标识 (MSI) 进行授权。 可以使用此功能来检索访问令牌以便安全地与 Azure 服务进行通信。

## <a name="about-managed-service-identity-msi"></a>关于托管服务标识 (MSI)
生成云应用程序时需要应对的常见挑战是，如何安全地管理为了通过云服务的身份验证而需要插入到代码中的凭据。 在 Cloud Shell 中，可能需要授权从 Key Vault 中检索脚本可能需要的凭据。

托管服务标识 (MSI) 为 Azure 服务提供了 Azure Active Directory (Azure AD) 中的自动托管标识，更巧妙地解决了这个问题。 此标识可用于通过支持 Azure AD 身份验证的任何服务（包括 Key Vault）的身份验证，这样就无需在代码中插入任何凭据了。

## <a name="acquire-access-token-in-cloud-shell"></a>在 Cloud Shell 中获取访问令牌

执行以下命令来将 MSI 访问令牌设置为环境变量 `access_token`。
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>处理令牌过期

本地 MSI 子系统会缓存令牌。 因此，可以根据所需的频率调用令牌，仅当存在以下情况时，才在线调用 Azure AD 结果：
- 由于缓存中没有令牌，发生了缓存未命中
- 令牌已过期

如果将令牌缓存在代码中，应该准备好处理资源指出令牌已过期的情况。

若要处理令牌错误，请访问[有关获取 MSI 访问令牌的 MSI 页](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling)。

## <a name="next-steps"></a>后续步骤
[详细了解 MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[从 MSI VM 获取访问令牌](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
