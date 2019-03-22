---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 216380cf7069468d13c4e533fc90b2596aa211c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58115914"
---
正确的 SSL 证书可以确保，要将加密的信息发送给正确的服务器。 除了加密，该证书还允许进行身份验证。 你可以上载您自己受信任的 SSL 证书通过设备的 PowerShell 接口。

1. [连接到 PowerShell 界面](#connect-to-the-powershell-interface)。
2. 使用`Set-HcsCertificate`cmdlet 来上传的证书。 出现提示时，提供以下参数：

   - `CertificateFilePath` -包含中的证书文件的共享路径 *.pfx*格式。
   - `CertificatePassword` -一个用于保护证书的密码。
   - `Credentials` -用户名和密码来访问包含证书的共享。

     下面的示例显示了此 cmdlet 的用法：

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credentials "Username/Password"
     ```

