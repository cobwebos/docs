---
title: 使用 OPC 保管库保护 OPC UA 设备的通信 - Azure |微软文档
description: 如何注册 OPC UA 应用程序，以及如何向 OPC 保管库颁发 OPC UA 设备的已签名应用程序证书。
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: af5e511cbf273bc4e4fa0a08d089a955426fe75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75454195"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>使用 OPC 保管库证书管理服务

本文介绍如何注册应用程序，以及如何为 OPC UA 设备颁发已签名的应用程序证书。

## <a name="prerequisites"></a>先决条件

### <a name="deploy-the-certificate-management-service"></a>部署证书管理服务

首先，将服务部署到 Azure 云。 有关详细信息，请参阅[部署 OPC 保管库证书管理服务](howto-opc-vault-deploy.md)。

### <a name="create-the-issuer-ca-certificate"></a>创建颁发者 CA 证书

如果尚未这样做，请创建颁发者 CA 证书。 有关详细信息，请参阅[为 OPC 保管库创建和管理颁发者证书](howto-opc-vault-manage.md)。

## <a name="secure-opc-ua-applications"></a>安全 OPC UA 应用程序

### <a name="step-1-register-your-opc-ua-application"></a>第 1 步：注册 OPC UA 应用程序 

> [!IMPORTANT]
> 注册应用程序需要写入器角色。

1. 在 打开证书服务`https://myResourceGroup-app.azurewebsites.net`，然后登录。
2. 转到**注册新**。 对于应用程序注册，用户至少需要分配 Writer 角色。
2. 条目窗体遵循 OPC UA 中的命名约定。 例如，在以下屏幕截图中，将显示 OPC UA .NET 标准堆栈中[OPC UA 参考服务器](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference)示例的设置：

   ![UA 参考服务器注册屏幕截图](media/howto-opc-vault-secure/reference-server-registration.png "UA 参考服务器注册")

5. 选择 **"注册"** 以在证书服务应用程序数据库中注册应用程序。 工作流直接引导用户执行下一步，以请求应用程序的已签名证书。

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>第 2 步：使用 CA 签名的应用程序证书保护应用程序

通过基于证书签名请求 （CSR） 颁发签名证书来保护您的 OPC UA 应用程序。 或者，您可以请求一个新的密钥对，其中包括一个新的私钥在 PFX 或 PEM 格式。 有关应用程序支持哪种方法的信息，请参阅 OPC UA 设备的文档。 通常，建议使用 CSR 方法，因为它不需要通过导线传输私钥。

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>使用新密钥对请求新证书

1. 转到**应用程序**。
3. 为列出的应用程序选择 **"新请求**"。

   ![请求新证书的屏幕截图](media/howto-opc-vault-secure/request-new-certificate.png "请求新证书")

3. 选择 **"请求新的密钥组合和证书**"以请求私钥和带有应用程序公钥的新签名证书。

   ![生成新密钥对和证书的屏幕截图](media/howto-opc-vault-secure/generate-new-key-pair.png "生成新密钥对")

4. 填写表格中，填写主题和域名。 对于私钥，选择带有密码的 PEM 或 PFX。 选择 **"生成新密钥组合**"以创建证书请求。

   ![查看证书请求详细信息的屏幕截图](media/howto-opc-vault-secure/approve-reject.png "批准证书")

5. 审批需要具有审批者角色的用户，并在 Azure 密钥保管库中具有签名权限。 在典型的工作流中，应将审批者和请求者角色分配给不同的用户。 选择 **"批准**"或 **"拒绝**"以启动或取消密钥对的实际创建和签名操作。 新的密钥对将安全地创建并存储在 Azure 密钥保管库中，直到证书请求者下载。 使用公钥生成的证书由 CA 签名。 这些操作可能需要几秒钟才能完成。

   ![查看证书请求详细信息的屏幕截图，底部有批准消息](media/howto-opc-vault-secure/view-key-pair.png "查看密钥对")

7. 生成的私钥 （PFX 或 PEM） 和证书 （DER） 可以在此处以选择为二进制文件下载的格式下载。 base64 编码版本也可用于复制证书并将其粘贴到命令行或文本条目。 
8. 在安全下载并存储私钥后，您可以选择 **"删除私钥**"。 带有公钥的证书仍可供将来使用。
9. 由于使用 CA 签名证书，CA 证书和证书吊销列表 （CRL） 也应在此处下载。

现在，它取决于 OPC UA 设备如何应用新的密钥对。 通常，CA 证书和 CRL 复制到`trusted`文件夹，而应用程序证书的公共密钥和私钥将应用于证书存储中的`own`文件夹。 某些设备可能已支持服务器推送证书更新。 请参阅 OPC UA 设备的文档。

#### <a name="request-a-new-certificate-with-a-csr"></a>使用 CSR 请求新证书 

1. 转到**应用程序**。
3. 为列出的应用程序选择 **"新请求**"。

   ![请求新证书的屏幕截图](media/howto-opc-vault-secure/request-new-certificate.png "请求新证书")

3. 选择 **"使用签名请求请求新证书**"，以请求应用程序的新签名证书。

   ![生成新证书的屏幕截图](media/howto-opc-vault-secure/generate-new-certificate.png "生成新证书")

4. 通过选择本地文件或在窗体中粘贴 base64 编码 CSR 来上载 CSR。 选择 **"生成新证书**"。

   ![查看证书请求详细信息的屏幕截图](media/howto-opc-vault-secure/approve-reject-csr.png "批准 CSR")

5. 审批需要具有审批者角色的用户，并在 Azure 密钥保管库中具有签名权限。 选择 **"批准**"或 **"拒绝**"以启动或取消实际的签名操作。 使用公钥生成的证书由 CA 签名。 此操作可能需要几秒钟才能完成。

   ![查看证书请求详细信息的屏幕截图，底部有批准消息](media/howto-opc-vault-secure/view-cert-csr.png "查看证书")

6. 生成的证书 （DER） 可以在此处作为二进制文件下载。 base64 编码版本也可用于复制证书并将其粘贴到命令行或文本条目。 
10. 安全下载并存储证书后，您可以选择 **"删除证书**"。
11. 由于使用 CA 签名证书，CA 证书和 CRL 也应在此处下载。

现在，它取决于 OPC UA 设备如何应用新证书。 通常，CA 证书和 CRL 复制到`trusted`文件夹，而应用程序证书将应用于证书存储中的`own`文件夹。 某些设备可能已支持服务器推送证书更新。 请参阅 OPC UA 设备的文档。

### <a name="step-3-device-secured"></a>第 3 步：设备安全

OPC UA 设备现在已准备好与受 CA 签名证书保护的其他 OPC UA 设备进行通信，无需进一步配置。

## <a name="next-steps"></a>后续步骤

现在，您已经学会了如何保护 OPC UA 设备，您可以：

> [!div class="nextstepaction"]
> [运行安全证书管理服务](howto-opc-vault-secure-ca.md)
