---
title: 通过带 GPU Azure Stack Edge Pro 的证书疑难解答 |Microsoft Docs
description: 介绍如何排查 Azure Stack Edge Pro GPU 设备的证书错误。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: f0f9dfa6e3d6ae02f66ac71f62586953cb21517e
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568346"
---
# <a name="troubleshooting-certificate-errors"></a>证书错误疑难解答

本文提供在将证书安装到 Azure Stack Edge Pro 设备时常见证书错误的疑难解答。

## <a name="common-certificate-errors"></a>常见证书错误

下表列出了常见的证书错误以及有关这些错误和可能的解决方案的详细信息：

> [!NOTE]
> 出现 &#8220;{0} ， {1} ，...，{n} &#8221; 指示位置参数。 位置参数将采用值，具体取决于你使用的证书。

| 错误代码 | 说明 |
|---|---|
| CertificateManagement_UntrustedCertificate | 使用者名称的证书 {0} 的证书链已断开。 上传签名链证书，然后上载此证书。|
| CertificateManagement_DeviceNotRegistered| 你的设备未激活。 只能在激活后上传支持证书。|
| CertificateManagement_ExpiredCertificate | 类型的证书 {0} 已过期或即将过期。 检查证书过期时间，并在需要时引入新证书。|
| CertificateManagement_FormatMismatch | 不支持证书格式。 检查证书格式，并根据需要引入新证书。  应为 {0} ，找到 {1} 。 |
| CertificateManagement_GenericError | 无法执行证书管理操作。 请在几分钟后重试此操作。 如果问题持续出现，请联系 Microsoft 支持。 |
| CertificateManagement_HttpsBindingFailure | 使用者名称的证书 {0} 无法创建安全的 https 通道。 检查已上传的证书，如果需要，请引入新证书。 设备节点证书发生此错误。|
| CertificateManagement_IncorrectKeyCertSignKeyUsage | 使用者名称的证书 {0} 不应具有密钥使用证书签名。 检查证书的密钥用法，并在需要时引入新证书。 签名链证书发生此错误。 |
| CertificateManagement_IncorrectKeyNumber | 使用者名称的证书 {0} 具有错误的密钥号 {1} 。 检查证书的密钥号，并在需要时引入新证书。|
| CertificateManagement_InvalidP7b | 上传的证书文件无效。  检查证书格式，并根据需要引入新证书。|
| CertificateManagement_KeyAlgorithmNotRSA | 此证书不使用 RSA 密钥算法。 仅支持 RSA 证书。 |
| CertificateManagement_KeySizeNotSufficient | 使用者名称的证书 {0} 没有足够的密钥大小 {1} 。 最小密钥大小为4096。|
| CertificateManagement_MissingClientOid | 使用者名称的证书没有 {0} 客户端身份验证 OID。 检查证书属性，如果需要，请引入新证书。|
| CertificateManagement_MissingDigitalSignatureKeyUsage | 使用者名称的证书 {0} 在密钥使用中没有数字签名。 检查证书属性，如果需要，请引入新证书。 |
| CertificateManagement_MissingKeyCertSignKeyUsage | 使用者名称的证书 {0} 在密钥使用中没有证书签名。 检查证书属性，如果需要，请引入新证书。|
| CertificateManagement_MissingKeyEnciphermentKeyUsage | 使用者名称的证书 {0} 在密钥使用中没有密钥加密。 检查证书属性，如果需要，请引入新证书。 |
| CertificateManagement_MissingServerOid | 使用者名称的证书没有 {0} 服务器身份验证 OID。 检查证书属性，如果需要，请引入新证书。|
| CertificateManagement_NameMismatch | 证书类型不匹配。 应为范围： {0} ，找到 {1} 。 上传适当的证书。|
| CertificateManagement_NoPrivateKeyPresent | 使用者名称的证书 {0} 没有私钥。 上载包含私钥的 .pfx 证书。|
| CertificateManagement_NotSelfSignedCertificate | 使用者名称的证书 {0} 不是自签名证书。 根证书应自签名 |
| CertificateManagement_NotSupportedOnVirtualAppliance | 虚拟设备上不支持此操作。 此错误表示仅在战术性云设备中运行 Data Box Gateway 进行签名。 通过 Windows PowerShell 管理设备时出现此错误。|
| CertificateManagement_SelfSignedCertificate | 使用者名称的证书 {0} 是自签名证书。 上传正确签名的证书。|
| CertificateManagement_SignatureAlgorithmSha1 | 使用者名称的证书 {0} 具有不受支持的签名算法。 SHA1-不支持 RSA。 |
| CertificateManagement_SubjectNamesInvalid | 使用者名称的证书没有 {0} 证书的正确使用者名称或使用者备用名称 {1} 。 检查已上传的证书，如果需要，请引入新证书。 还应检查 DNS 名称以匹配 SAN 名称。|
| CertificateManagement_UnreadableCertificate | 无法读取类型的证书 {0} 。 如果证书不可读或已损坏，则会出现此错误。 引入新证书。|
| CertificateSubjectNotFound | 找不到使用者名称的证书 {0} 。 引入新证书。|
| CertificateRotationGenericFailure | 一个或多个证书轮换失败。 几分钟后重试。 如果问题持续出现，请联系 Microsoft 支持。|
| CertificateImportFailure | {0}节点上未导入具有指纹的证书 {1} 。 如果问题持续出现，请联系 Microsoft 支持。 |
| CertificateApplyFailure | {0}节点上未应用具有指纹的证书 {1} 。 如果问题持续出现，请联系 Microsoft 支持。|
| NodeNotReachable | 无法在上验证证书 {0} 。 检查系统硬件和软件运行状况。|

## <a name="next-steps"></a>后续步骤

[证书要求](azure-stack-edge-j-series-certificate-requirements.md)