---
title: include 文件
description: 机密客户端方案登陆页的 include 文件（守护程序、Web 应用、Web API）
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "76773381"
---
## <a name="register-secrets-or-certificates"></a>注册机密或证书

与任何机密客户端应用程序一样，你需要注册机密或证书。 可以通过 [Azure 门户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)中的交互体验或使用命令行工具（如 PowerShell）注册应用程序机密。

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>使用应用程序注册门户注册客户端机密

在应用程序的“证书和机密”  页上管理客户端凭据：

![“证书和机密”页](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- 应用程序机密（也称为客户端密码）由 Azure AD 在机密客户端应用程序注册期间生成。 选择“新建客户端密码”  时，将发生此生成。 此时，必须将机密字符串复制到剪贴板中以便在应用中使用，然后再选择“保存”  。 此字符串将不再显示。
- 在应用程序注册过程中，请使用“上传证书”  按钮来上传证书。 Azure AD 仅支持在应用程序上直接注册的证书，而不遵循证书链。

有关详细信息，请参阅[快速入门：将客户端应用程序配置为访问 Web API | 将凭据添加到应用程序](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)。



### <a name="register-client-secrets-by-using-powershell"></a>使用 PowerShell 注册客户端机密

或者，可以使用命令行工具向 Azure AD 注册应用程序。 [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) 示例显示如何向 Azure AD 应用程序注册应用程序机密或证书：

- 有关如何注册应用程序机密的详细信息，请参阅 [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)。
- 有关如何向应用程序注册证书的详细信息，请参阅 [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)。
