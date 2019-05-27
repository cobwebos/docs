---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: d7a9923d5bd9e357bcd75fae6e0a7d1bcd437a53
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161207"
---
正确的 SSL 证书可以确保，要将加密的信息发送给正确的服务器。 除了加密，该证书还允许进行身份验证。 你可以上载您自己受信任的 SSL 证书通过设备的 PowerShell 接口。

1. [连接到 PowerShell 界面](#connect-to-the-powershell-interface)。
2. 使用`Set-HcsCertificate`cmdlet 来上传的证书。 出现提示时，提供以下参数：

   - `CertificateFilePath` -包含中的证书文件的共享路径 *.pfx*格式。
   - `CertificatePassword` -一个用于保护证书的密码。
   - `Credentials` -用户名和密码来访问包含证书的共享。

     下面的示例显示了此 cmdlet 的用法：

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username/Password"
     ```

