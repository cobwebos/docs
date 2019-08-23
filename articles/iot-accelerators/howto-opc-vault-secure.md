---
title: 保护 OPC UA 设备与 OPC 保管库的通信-Azure |Microsoft Docs
description: 如何注册 OPC UA 应用程序以及如何为 OPC UA 设备和 OPC 保管库颁发已签名的应用程序证书。
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: a819e0b5a0da92137451eedbf84fe06d22879568
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973774"
---
# <a name="how-to-use-the-opc-ua-certificate-management-service"></a>如何使用 OPC UA 证书管理服务

本文介绍如何注册应用程序, 以及如何为 OPC UA 设备颁发已签名的应用程序证书。

## <a name="prerequisites"></a>先决条件

### <a name="deploy-the-certificate-management-service"></a>部署证书管理服务

首先, 需要将该服务部署到 Azure 云。
请查看介绍如何[部署 OPC 保管库证书管理服务](howto-opc-vault-deploy.md)的文章。

### <a name="create-the-issuer-ca-certificate"></a>创建颁发者 CA 证书

如果尚未这样做, 请创建颁发者 CA 证书。

查找介绍如何[创建和管理 OPC 保管库的颁发者证书](howto-opc-vault-manage.md)的文章。

## <a name="secure-opc-ua-applications"></a>保护 OPC UA 应用程序

### <a name="step-1-register-your-opc-ua-application"></a>步骤 1：注册 OPC UA 应用程序 

> [!IMPORTANT]
> 注册应用程序需要 "Writer" 角色。

1. 在`https://myResourceGroup-app.azurewebsites.net`中打开证书服务, 并登录。
2. 导航到 `Register New` 页。
1. 对于应用程序注册, 用户需要至少分配 "Writer" 角色。
2. 项窗体遵循 OPC UA 世界中的命名约定。 作为示例, 请参阅 OPC UA 中[OPC Ua Reference Server](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference)示例的设置。显示 NetStandard 堆栈:

   ![UA 引用服务器注册](media/howto-opc-vault-secure/reference-server-registration.png "UA 引用服务器注册")

5. 按下`Register`该按钮在证书服务应用程序数据库中注册该应用程序。 工作流直接指导用户执行下一步, 以请求应用程序的签名证书。

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>步骤 2：使用 CA 签名应用程序证书保护应用程序

可以通过基于证书签名请求 (CSR) 颁发已签名的证书来保护 OPC UA 应用程序, 也可以通过请求新的密钥对 (它还包括一个采用 PFX 或 PEM 格式的新私钥) 来保护该应用程序。 按照您的应用程序支持的方法的 OPC UA 设备的文档进行操作。 通常情况下, 建议使用 CSR 方法, 因为它不需要通过线路传输私钥。

- 如果你的设备需要颁发新的密钥对, 请遵循[Step3a](##step-3a-request-a-new-certificate-with-a-new-keypair)。
- 如果设备支持颁发证书签名请求 (CSR), 请按[Step3b](#step-3b-request-a-new-certificate-with-a-csr)。

### <a name="step-3a-request-a-new-certificate-with-a-new-keypair"></a>步骤 3a：使用新的密钥对请求新证书

1. 导航到 `Applications` 页。
3. 通过选择`New Request`列出的应用程序来启动证书请求工作流。

   ![请求新证书](media/howto-opc-vault-secure/request-new-certificate.png "请求新证书")

3. 按 "请求新的密钥对和证书" 请求私钥, 并使用应用程序的公钥来请求新的签名证书。

   ![生成新的密钥对](media/howto-opc-vault-secure/generate-new-key-pair.png "生成新的密钥对")

4. 在表单中填写 "使用者" 和 "域名", 并选择 "PEM" 或 "PFX" 和 "密码" 作为私钥。 `Generate New Certificate`按按钮创建证书请求。

   ![批准证书](media/howto-opc-vault-secure/approve-reject.png "批准证书")

5. 审批步骤要求具有 "审批者" 角色并且 Azure Key Vault 中具有签名权限的用户。 在典型的工作流中, 应将审批者和请求者角色分配给不同的用户。
6. 批准或拒绝证书请求, 以启动或取消实际创建密钥对和签名操作。 创建新的密钥对, 并安全地存储在 Azure Key Vault, 直到证书请求方下载。 使用公钥生成的证书由 CA 进行签名。 这些操作可能需要几秒钟才能完成。

   ![查看密钥对](media/howto-opc-vault-secure/view-key-pair.png "查看密钥对")

7. 可以通过选择 "二进制文件下载" 格式从此处下载生成的私钥 (PFX 或 PEM) 和证书 (DER)。 例如, base64 编码版本也可用来将证书粘贴到命令行或文本输入。 
8. 安全下载并存储私钥后, 可以通过`Delete Private Key`按钮将其从服务中删除。 带有公钥的证书仍然可用, 供将来使用。
9. 由于使用的是 CA 签名证书, 因此还应在此处下载 CA 证书和 CRL。
10. 现在, 它依赖于 OPC UA 设备如何应用新的密钥对。 通常会将 CA 证书和 CRL 复制到`trusted`文件夹, 而应用程序证书的公钥和私钥会应用`own`于证书存储区中的文件夹。 某些设备可能已支持证书更新的 "服务器推送"。 请参阅 OPC UA 设备的文档。

### <a name="step-3b-request-a-new-certificate-with-a-csr"></a>步骤 3b:使用 CSR 请求新证书 

1. 导航到 `Applications` 页。
3. 通过选择`New Request`列出的应用程序来启动证书请求工作流。

   ![请求新证书](media/howto-opc-vault-secure/request-new-certificate.png "请求新证书")

3. 按 "使用签名请求请求新证书", 为你的应用程序请求新的签名证书。

   ![生成新证书](media/howto-opc-vault-secure/generate-new-certificate.png "生成新证书")

4. 通过选择本地文件或通过在表单中粘贴 base64 编码的 CSR 来上传 CSR。 `Generate New Certificate`按按钮创建证书请求。

   ![审批 CSR](media/howto-opc-vault-secure/approve-reject-csr.png "审批 CSR")

5. 审批步骤要求具有 "审批者" 角色并且 Azure Key Vault 中具有签名权限的用户。 批准或拒绝证书请求以启动或取消实际签名操作。 使用公钥生成的证书由 CA 进行签名。 此操作可能需要几秒钟才能完成。

   ![查看证书](media/howto-opc-vault-secure/view-cert-csr.png "查看证书")

6. 生成的证书 (DER) 可以作为二进制文件从此处下载。 例如, base64 编码版本也可用来将证书粘贴到命令行或文本输入。 
10. 安全下载并存储证书后, 可以通过`Delete Certificate`按钮从服务中删除该证书。
11. 由于使用的是 CA 签名证书, 因此还应在此处下载 CA 证书和 CRL。
12. 现在, 它依赖于 OPC UA 设备如何应用新证书。 通常会将 CA 证书和 CRL 复制到`trusted`文件夹, 同时将应用程序证书应用`own`于证书存储区中的文件夹。 某些设备可能已支持证书更新的 "服务器推送"。 请参阅 OPC UA 设备的文档。

### <a name="step-4-device-secured"></a>步骤 4：设备保护

OPC UA 设备现在可以与其他使用 CA 签名证书保护的 OPC UA 设备通信, 无需进一步配置。 请尽情享受其中的乐趣！


## <a name="next-steps"></a>后续步骤

现在, 你已了解如何保护 OPC UA 设备, 下面是建议的后续步骤:

> [!div class="nextstepaction"]
> [运行安全证书管理服务](howto-opc-vault-secure-ca.md)