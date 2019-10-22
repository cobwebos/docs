---
title: 保护 OPC UA 设备与 OPC 保管库的通信-Azure |Microsoft Docs
description: 如何注册 OPC UA 应用程序，以及如何为 OPC UA 设备和 OPC 保管库颁发已签名的应用程序证书。
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b5c886625c944e2f5501859e78506ca89ec3d765
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "71203692"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>使用 OPC 保管库证书管理服务

本文介绍如何注册应用程序，以及如何为 OPC UA 设备颁发已签名的应用程序证书。

## <a name="prerequisites"></a>必备组件

### <a name="deploy-the-certificate-management-service"></a>部署证书管理服务

首先，将该服务部署到 Azure 云。 有关详细信息，请参阅[部署 OPC 保管库证书管理服务](howto-opc-vault-deploy.md)。

### <a name="create-the-issuer-ca-certificate"></a>创建颁发者 CA 证书

如果尚未这样做，请创建颁发者 CA 证书。 有关详细信息，请参阅[创建和管理 OPC 保管库的颁发者证书](howto-opc-vault-manage.md)。

## <a name="secure-opc-ua-applications"></a>保护 OPC UA 应用程序

### <a name="step-1-register-your-opc-ua-application"></a>步骤1：注册 OPC UA 应用程序 

> [!IMPORTANT]
> 注册应用程序需要编写者角色。

1. 在 `https://myResourceGroup-app.azurewebsites.net` 打开证书服务，并登录。
2. 请参阅**注册新**。 对于应用程序注册，用户需要至少分配编写者角色。
2. 条目窗体遵循 OPC UA 中的命名约定。 例如，在以下屏幕截图中，会显示 OPC UA .NET Standard 堆栈中的[OPC Ua 引用服务器](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference)示例的设置：

   ![UA 引用服务器注册的屏幕截图](media/howto-opc-vault-secure/reference-server-registration.png "UA 引用服务器注册")

5. 选择 "**注册**"，在证书服务应用程序数据库中注册该应用程序。 工作流直接指导用户执行下一步，以请求应用程序的签名证书。

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>步骤2：使用 CA 签名应用程序证书保护应用程序

通过基于证书签名请求（CSR）颁发签名证书来保护 OPC UA 应用程序。 或者，你可以请求新的密钥对，这包括采用 PFX 或 PEM 格式的新私钥。 有关应用程序支持哪种方法的信息，请参阅 OPC UA 设备的文档。 通常情况下，建议使用 CSR 方法，因为它不需要通过线路传输私钥。

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>使用新的密钥对请求新证书

1. 请参阅**应用程序**。
3. 为列出的应用程序选择 "**新建请求**"。

   ![请求新证书的屏幕截图](media/howto-opc-vault-secure/request-new-certificate.png "请求新证书")

3. 选择 "**请求新的密钥对和证书**" 请求私钥，并使用应用程序的公钥来请求新的签名证书。

   ![生成新的密钥对和证书的屏幕截图](media/howto-opc-vault-secure/generate-new-key-pair.png "生成新的密钥对")

4. 使用主题和域名填写表单。 对于私钥，选择 PEM 或具有密码的 PFX。 选择 "**生成新的密钥**对" 以创建证书申请。

   ![查看证书请求详细信息的屏幕截图](media/howto-opc-vault-secure/approve-reject.png "批准证书")

5. 审批要求具有审批者角色的用户和 Azure Key Vault 中的签名权限。 在典型的工作流中，应将审批者和请求者角色分配给不同的用户。 选择 "**批准**" 或 "**拒绝**" 以启动或取消实际创建密钥对和签名操作。 新的密钥对创建并安全地存储在 Azure Key Vault 中，直到证书请求方下载。 使用公钥生成的证书由 CA 进行签名。 这些操作可能需要几秒钟才能完成。

   ![查看证书请求详细信息的屏幕截图，最底部的审批消息](media/howto-opc-vault-secure/view-key-pair.png "查看密钥对")

7. 可以通过选择 "二进制文件下载" 格式从此处下载生成的私钥（PFX 或 PEM）和证书（DER）。 例如，可将证书复制并粘贴到命令行或文本输入中，这也提供了 base64 编码版本。 
8. 安全下载并存储私钥后，可以选择 "**删除私钥**"。 带有公钥的证书仍然可用，供将来使用。
9. 由于使用的是 CA 签名证书，因此还应在此处下载 CA 证书和证书吊销列表（CRL）。

现在，它依赖于 OPC UA 设备如何应用新的密钥对。 通常会将 CA 证书和 CRL 复制到 `trusted` 文件夹，同时应用程序证书的公钥和私钥会应用于证书存储中的 `own` 文件夹。 某些设备可能已支持服务器推送以实现证书更新。 请参阅 OPC UA 设备的文档。

#### <a name="request-a-new-certificate-with-a-csr"></a>使用 CSR 请求新证书 

1. 请参阅**应用程序**。
3. 为列出的应用程序选择 "**新建请求**"。

   ![请求新证书的屏幕截图](media/howto-opc-vault-secure/request-new-certificate.png "请求新证书")

3. 选择 "**请求带有签名请求的新证书**" 以请求应用程序的新签名证书。

   ![生成新证书的屏幕截图](media/howto-opc-vault-secure/generate-new-certificate.png "生成新证书")

4. 通过选择本地文件或通过在表单中粘贴 base64 编码的 CSR 来上传 CSR。 选择 "**生成新证书**"。

   ![查看证书请求详细信息的屏幕截图](media/howto-opc-vault-secure/approve-reject-csr.png "审批 CSR")

5. 审批要求具有审批者角色的用户和 Azure Key Vault 中的签名权限。 选择 "**批准**" 或 "**拒绝**" 以启动或取消实际签名操作。 使用公钥生成的证书由 CA 进行签名。 此操作可能需要几秒钟才能完成。

   ![查看证书请求详细信息的屏幕截图，最底部的审批消息](media/howto-opc-vault-secure/view-cert-csr.png "查看证书")

6. 生成的证书（DER）可以作为二进制文件从此处下载。 例如，可将证书复制并粘贴到命令行或文本输入中，这也提供了 base64 编码版本。 
10. 安全下载并存储证书后，可以选择 "**删除证书**"。
11. 由于使用的是 CA 签名证书，因此还应在此处下载 CA 证书和 CRL。

现在，它依赖于 OPC UA 设备如何应用新证书。 通常会将 CA 证书和 CRL 复制到 `trusted` 文件夹中，同时应用程序证书应用于证书存储中的 `own` 文件夹。 某些设备可能已支持服务器推送以实现证书更新。 请参阅 OPC UA 设备的文档。

### <a name="step-4-device-secured"></a>步骤4：设备安全

OPC UA 设备现在已准备好与受 CA 签名证书保护的其他 OPC UA 设备通信，无需进一步配置。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何保护 OPC UA 设备，你可以：

> [!div class="nextstepaction"]
> [运行安全证书管理服务](howto-opc-vault-secure-ca.md)