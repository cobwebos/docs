---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448638"
---
正确的 SSL 证书可以确保，要将加密的信息发送给正确的服务器。 除了加密，该证书还允许进行身份验证。 你可以上载您自己受信任的 SSL 证书通过设备的 PowerShell 接口。

1. [连接到 PowerShell 界面](#connect-to-the-powershell-interface)。
2. 使用`Set-HcsCertificate`cmdlet 来上传的证书。 出现提示时，提供以下参数：

   - `CertificateFilePath` -包含中的证书文件的共享路径 *.pfx*格式。
   - `CertificatePassword` -一个用于保护证书的密码。
   - `Credentials` -用于访问包含证书的共享的用户名。 提供对出现提示时的网络共享密码。

     下面的示例显示了此 cmdlet 的用法：

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

