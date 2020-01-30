---
title: include 文件
description: 机密客户端方案登录页面的包含文件（守护程序、web 应用、web API）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a5d34ac7eea50b67bd679d8cb8ddecf7ca277abd
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773381"
---
## <a name="register-secrets-or-certificates"></a>注册机密或证书

对于任何机密客户端应用程序，都需要注册机密或证书。 你可以通过[Azure 门户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)中的交互式体验或使用命令行工具（如 PowerShell）来注册你的应用程序机密。

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>使用应用程序注册门户注册客户端密码

客户端凭据的管理发生在应用程序的 "**证书 & 机密**" 页上：

![证书 & 机密页](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- 应用程序密钥（也称为客户端密码）是在注册机密客户端应用程序的过程中 Azure AD 生成的。 当你选择 "**新建客户端密钥**" 时，将发生这种情况。 此时，你必须将机密字符串复制到剪贴板，以便在应用中使用，然后再选择 "**保存**"。 此字符串将不会再出现。
- 在应用程序注册过程中，可以使用 "**上传证书**" 按钮上载证书。 Azure AD 仅支持直接在应用程序上注册的证书，但不支持证书链。

有关详细信息，请参阅[快速入门：配置客户端应用程序以访问 Web api |向应用程序添加凭据](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)。



### <a name="register-client-secrets-by-using-powershell"></a>使用 PowerShell 注册客户端密码

或者，你可以使用命令行工具将应用程序注册到 Azure AD。 [Dotnetcore](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)示例演示如何将应用程序机密或证书注册到 Azure AD 应用程序：

- 有关如何注册应用程序机密的详细信息，请参阅[AppCreationScripts/Configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)。
- 有关如何向应用程序注册证书的详细信息，请参阅[AppCreationScripts-withCert/Configure。](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)
