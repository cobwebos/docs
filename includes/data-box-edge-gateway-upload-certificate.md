---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "67448638"
---
正确的 SSL 证书可确保向正确的服务器发送加密信息。 除了加密之外，证书还允许进行身份验证。 您可以通过设备的 PowerShell 接口上传您自己的可信 SSL 证书。

1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
2. 使用`Set-HcsCertificate`cmdlet 上载证书。 当出现提示时，提供以下参数：

   - `CertificateFilePath`- 以 *.pfx*格式包含证书文件的共享的路径。
   - `CertificatePassword`- 用于保护证书的密码。
   - `Credentials`- 访问包含证书的共享的用户名。 提示时向网络共享提供密码。

     下面的示例显示了此 cmdlet 的用法：

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

