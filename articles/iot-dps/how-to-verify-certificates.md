---
title: 如何使用 Azure IoT 中心设备预配服务执行 X.509 CA 证书的所有权证明 | Microsoft Docs
description: 如何使用 DPS 服务验证 X.509 CA 证书
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 709a53c0fc0814262cd7ce8bb0398be82ef81479
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526841"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>如何使用设备预配服务执行 X.509 CA 证书的所有权证明

验证的 X.509 证书颁发机构 (CA) 证书是已上传并注册到预配服务，且已在该服务中完成所有权证明的 CA 证书。 

所有权证明涉及以下步骤：
1. 获取预配服务针对 X.509 CA 证书生成的唯一验证码。 可以从 Azure 门户获取此信息。
2. 使用验证码作为使用者创建 X.509 验证证书，使用与 X.509 CA 证书关联的私钥对该证书进行签名。
3. 将签名的验证证书上传到服务。 服务将使用要验证的 CA 证书的公共部分来验证验证证书，从而证明你拥有 CA 证书的私钥。

使用登记组时，验证的证书将发挥重要作用。 验证证书所有权可以确保证书上传者拥有该证书的私钥，从而提供一个附加的安全层。 验证可以防止恶意行动者提取中间证书，并使用该证书在其自己的预配服务中创建登记组，从而有效地劫持你的设备，因此，可防止他们探查你的流量。 通过证明证书链中根证书或中间证书的所有权，可以证明你有权针对需要注册为登记组一部分的设备生成叶证书。 出于此原因，在登记组中配置的根证书或中间证书必须是验证的证书，或者在服务中进行身份验证时，必须滚动更新为设备所代表的证书链中的某个已验证证书。 若要详细了解登记组，请参阅 [X.509 证书](concepts-security.md#x509-certificates)和[使用 X.509 证书控制设备对预配服务的访问](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates)。

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>注册 X.509 证书的公共部分并获取验证码

若要将 CA 证书注册到预配服务并获取可在所有权证明期间使用的验证码，请遵循以下步骤。 

1. 在 Azure 门户中导航到预配服务，从左侧菜单打开“证书”。 
2. 单击“添加”以添加新证书。
3. 输入证书的友好显示名称。 浏览到表示 X.509 证书公共部分的 .cer 或 .pem 文件。 单击“上传” 。
4. 收到已成功上传证书的通知后，单击“保存”。

    ![上传证书](./media/how-to-verify-certificates/add-new-cert.png)  

   该证书将显示在“证书资源管理器”列表中。 请注意，此证书的“状态”为“未验证”。

5. 单击在上一步骤中添加的证书。

6. 在“证书详细信息”中，单击“生成验证码”。

7. 预配服务会创建一个可用于验证证书所有权的**验证码**。 将此代码复制到剪贴板。 

   ![验证证书](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>对验证代码进行数字签名，以创建验证证书

现在，需要使用与 X.509 CA 证书关联的、可生成签名的私钥来为*验证码*签名。 此过程称为[所有权证明](https://tools.ietf.org/html/rfc5280#section-3.1)，最终会生成一个签名的验证证书。

Microsoft 提供了工具和示例来帮助你创建签名的验证证书： 

- **Azure IoT 中心 C SDK** 提供了 PowerShell (Windows) 和 (Linux) Bash 脚本来帮助你创建用于开发的 CA 证书和叶证书，以及使用验证码执行所有权证明。 可将系统相关的[文件](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates)下载到工作文件夹，并遵照[管理 CA 证书自述文件](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)中的说明执行 CA 证书的所有权证明。 
- **Azure IoT 中心 C# SDK** 包含[组证书验证示例](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples/GroupCertificateVerificationSample)，可用于执行所有权证明。
 
> [!IMPORTANT]
> 除了执行所有权证明以外，使用上述 PowerShell 和 Bash 脚本还可创建根证书、中间证书和叶证书用于验证和预配设备。 只能在开发中使用这些证书。 切勿将它们用于生产环境。 

文档和 SDK 中提供的 PowerShell 与 Bash 脚本依赖于 [OpenSSL](https://www.openssl.org/)。 还可以借助 OpenSSL 或其他第三方工具执行所有权证明。 有关 SDK 随附的工具的详细信息，请参阅[如何使用 SDK 随附的工具](how-to-use-sdk-tools.md)。 


## <a name="upload-the-signed-verification-certificate"></a>上传已签名的验证证书

1. 在门户中将生成的签名作为验证证书上传到预配服务。 在 Azure 门户上的“证书详细信息”中，使用“验证证书 .pem 或 .cer 文件”字段旁边的“文件资源管理器”图标从系统上传已签名的验证证书。

2. 成功上传证书后，单击“验证”。 在“证书资源管理器”列表中，证书的“状态”会更改为“已验证”。 如果状态未自动更新，请单击“刷新”。

   ![上传证书验证](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>后续步骤

- 若要了解如何使用门户创建登记组，请参阅[使用 Azure 门户管理设备登记](how-to-manage-enrollments.md)。
- 若要了解如何使用服务 SDK 创建登记组，请参阅[使用服务 SDK 管理设备登记](how-to-manage-enrollments-sdks.md)。










