---
title: include 文件
description: 机密客户端方案登陆页的 include 文件（守护程序、Web 应用、Web API）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/14/2020
ms.author: jmprieur
ms.openlocfilehash: 42102f38959911388cefcc141d949e59f24a2c31
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89436455"
---
## <a name="add-a-client-secret-or-certificate"></a>添加客户端密码或证书

对于任何机密客户端应用程序，你需要添加一个机密或证书作为该应用程序的 *凭据* ，因此它可以自行进行身份验证，而无需用户交互。

可以通过使用 [Azure 门户](#add-client-credentials-by-using-the-azure-portal) 或使用 [PowerShell](#add-client-credentials-by-using-powershell)之类的命令行工具，将凭据添加到客户端应用的注册。

### <a name="add-client-credentials-by-using-the-azure-portal"></a>使用 Azure 门户添加客户端凭据

若要将凭据添加到机密客户端应用程序的应用注册，请按照 [快速入门：向 Microsoft 标识平台注册应用程序以使用](../articles/active-directory/develop/quickstart-register-app.md) 要添加的凭据类型的步骤：

* [添加客户端密码](../articles/active-directory/develop/quickstart-register-app.md#add-a-client-secret)
* [添加证书](../articles/active-directory/develop/quickstart-register-app.md#add-a-certificate)

### <a name="add-client-credentials-by-using-powershell"></a>使用 PowerShell 添加客户端凭据

或者，在使用 PowerShell 将应用程序注册到 Microsoft 标识平台时，可以添加凭据。

GitHub 上的 [dotnetcore](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) 代码示例介绍了如何在注册应用程序时添加应用程序机密或证书：

- 有关如何使用 PowerShell 添加 **客户端机密** 的详细信息，请参阅 [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)。
- 有关如何使用 PowerShell 添加 **证书** 的详细信息，请参阅 [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)。
