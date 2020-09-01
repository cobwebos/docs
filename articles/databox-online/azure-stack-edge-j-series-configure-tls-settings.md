---
title: 在访问 Azure Stack Edge GPU 设备的 Windows 客户端上配置 TLS 1。2
description: 介绍如何在 Windows 客户端上配置 TLS 1.2，该客户端访问 Azure Stack 边缘 GPU 设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: bf6b591ef3158a5944b1ebeb37dd0ef5935f7215
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268496"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-device"></a>在访问 Azure Stack Edge 设备的 Windows 客户端上配置 TLS 1。2

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

如果使用 Windows 客户端来访问 Azure Stack Edge 设备，则需要在客户端上配置 TLS 1.2。 本文提供了有关在 Windows 客户端上配置 TLS 1.2 的资源和指南。 

此处提供的准则基于在运行 Windows Server 2016 的客户端上执行的测试。

## <a name="configure-tls-12-for-current-powershell-session"></a>为当前 PowerShell 会话配置 TLS 1。2

执行以下步骤以在客户端上配置 TLS 1.2。

1. 以管理员身份运行 PowerShell。
2. 若要为当前 PowerShell 会话设置 TLS 1.2，请键入：
  
    ```azurepowershell
    $TLS12Protocol = [System.Net.SecurityProtocolType] 'Ssl3 , Tls12'
    [System.Net.ServicePointManager]::SecurityProtocol = $TLS12Protocol
    ```
## <a name="configure-tls-12-on-client"></a>在客户端上配置 TLS 1。2

如果要为环境设置系统范围的 TLS 1.2，请遵循以下文档中的指南：

- [常规-如何启用 TLS 1。2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)
- [如何在客户端上启用 TLS 1.2](https://docs.microsoft.com/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [如何在站点服务器和远程站点系统上启用 TLS 1.2](https://docs.microsoft.com/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [TLS/SSL 中的协议 (Schannel SSP) ](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- [密码套件](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)：具体而言，要 [配置 TLS 密码套件顺序](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order) ，请确保列出当前密码套件，并预先列出以下列表中缺少的任何内容：

    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

    还可以通过直接编辑注册表设置来添加这些密码套件。

    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "Functions"  -PropertyType String -Value ("TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384")
    ```

- 如何设置椭圆曲线

    请确保列出当前椭圆曲线并预置以下列表中缺少的任何内容：

    - P-256 
    - P-384

    还可以通过直接编辑注册表设置来添加这些椭圆曲线。
    
    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "EccCurves" -PropertyType MultiString -Value @("NistP256", "NistP384")
    ```
    
    - [将最小 RSA 密钥交换大小设置为 2048](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes)。



## <a name="next-steps"></a>后续步骤

[连接到 Azure 资源管理器](azure-stack-edge-j-series-connect-resource-manager.md)