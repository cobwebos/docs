---
title: 加密 - Microsoft 威胁建模工具 - Azure | Microsoft 文档
description: 针对威胁建模工具中暴露的威胁采取的缓解措施
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 5e5d487c4c793a49ce1d4ac17f6fcd672e09bb90
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
ms.locfileid: "30911652"
---
# <a name="security-frame-cryptography--mitigations"></a>安全框架：加密 | 缓解措施 
| 产品/服务 | 文章 |
| --------------- | ------- |
| **Web 应用程序** | <ul><li>[只使用批准的对称块加密法和密钥长度](#cipher-length)</li><li>[为对称加密法使用批准的块加密法模式和初始化向量](#vector-ciphers)</li><li>[使用批准的非对称算法、密钥长度和填充](#padding)</li><li>[使用批准的随机数生成器](#numgen)</li><li>[不要使用对称流加密法](#stream-ciphers)</li><li>[使用批准的 MAC/HMAC/键控哈希算法](#mac-hash)</li><li>[只使用批准的加密哈希函数](#hash-functions)</li></ul> |
| **数据库** | <ul><li>[使用强加密算法来加密数据库中的数据](#strong-db)</li><li>[应该对 SSIS 包加密和数字签名](#ssis-signed)</li><li>[将数字签名添加到关键的数据库安全对象](#securables-db)</li><li>[使用 SQL Server EKM 保护加密密钥](#ekm-keys)</li><li>[如果不应向数据库引擎透露加密密钥，请使用 AlwaysEncrypted 功能](#keys-engine)</li></ul> |
| **IoT 设备** | <ul><li>[在 IoT 设备上安全存储加密密钥](#keys-iot)</li></ul> | 
| **IoT 云网关** | <ul><li>[生成足够长度的随机对称密钥用于向 IoT 中心进行身份验证](#random-hub)</li></ul> | 
| **Dynamics CRM 移动客户端** | <ul><li>[确保实施要求使用 PIN 并允许远程擦除的设备管理策略](#pin-remote)</li></ul> | 
| **Dynamics CRM Outlook 客户端** | <ul><li>[确保实施要求 PIN/密码/自动锁定并加密所有数据的设备管理策略（例如 Bitlocker）](#bitlocker)</li></ul> | 
| **标识服务器** | <ul><li>[使用标识服务器时确保滚动更新签名密钥](#rolled-server)</li><li>[确保在标识服务器中使用强加密型客户端 ID 和客户端密码](#client-server)</li></ul> | 

## <a id="cipher-length"></a>只使用批准的对称块加密法和密钥长度

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | <p>产品必须只使用已由组织中加密顾问明确批准的对称块加密法和关联的密钥长度。 Microsoft 批准的对称算法包括以下块加密法：</p><ul><li>对于新代码，可接受 AES-128、AES-192 和 AES-256</li><li>为了与现有代码向后兼容，可接受三重密钥 3DES</li><li>使用对称块加密法的产品：<ul><li>新代码需要使用高级加密标准 (AES)</li><li>为了向后兼容，允许在现有代码中使用三重数据加密标准 (3DES)</li><li>其他所有块加密法，包括 RC2、DES、双重密钥 3DES、DESX 和 Skipjack，只能用于解密旧数据，用于加密时必须改用其他算法</li></ul></li><li>对于对称块加密算法，必须至少指定 128 位的密钥长度。 针对新代码建议的唯一块加密算法为 AES（AES-128、AES-192 和 AES-256 都可接受）</li><li>如果已在现有代码中使用三重密钥 3DES，则可接受该算法；建议过渡到 AES。 行业不再认为 DES、DESX、RC2 和 Skipjack 是安全的。 只能出于向后兼容的目的将这些算法用于解密现有数据，应使用建议的块加密法重新加密数据</li></ul><p>请注意，必须以批准的加密法模式使用所有对称块加密法，这要求使用适当的初始化向量 (IV)。 适当的 IV 通常是一个随机数，绝对不会是常量值</p><p>经过组织的加密委员会评审后，允许使用传统的或者未批准的加密算法和更短的密钥长度来读取现有数据（但不能写入新数据）。 但是，必须针对此要求申请例外处理。 此外，在企业部署中，如果使用弱加密来读取数据，应考虑在产品中向管理员发出警告。 此类警告应是自释性的并指明可采取的措施。 在某些情况下，可能适合使用组策略来控制弱加密的使用</p><p>为使加密灵活可控而允许的 .NET 算法（按优先顺序列出）</p><ul><li>AesCng（符合 FIPS）</li><li>AuthenticatedAesCng（符合 FIPS）</li><li>AESCryptoServiceProvider（符合 FIPS）</li><li>AESManaged（不符合 FIPS）</li></ul><p>请注意，在未对 machine.config 文件做出更改的情况下，不能通过 `SymmetricAlgorithm.Create` 或 `CryptoConfig.CreateFromName` 方法指定其中的任何算法。 另请注意，在低于 .NET 3.5 的 .NET 版本中，AES 名为 `RijndaelManaged`，`AesCng` 和 `AuthenticatedAesCng` 可通过 CodePlex 获取，在底层 OS 中要求使用 CNG</p>

## <a id="vector-ciphers"></a>为对称加密法使用批准的块加密法模式和初始化向量

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | 必须以批准的对称加密法模式使用所有对称块加密法。 批准的模式只包括 CBC 和 CTS。 具体而言，应当避免电子源码书 (ECB) 操作模式；使用 ECB 必须经得组织加密委员会的评审。 使用 OFB、CFB、CTR、CCM 和 GCM 或其他任何加密模式都经常经得组织加密委员会的评审。 对采用“流加密法模式”的块加密法（例如 CTR）重复使用相同的初始化向量 (IV) 可能会导致透露已加密的数据。 所有对称块加密法也必须结合适当的初始化向量 (IV) 使用。 适当的 IV 通常是一个强加密型随机数，绝对不会是常量值 |

## <a id="padding"></a>使用批准的非对称算法、密钥长度和填充

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | <p>使用禁止的加密算法会给产品安全性带来重大风险，因此必须避免使用。 产品必须只使用经过组织加密委员会明确批准的加密算法和关联的密钥长度和填充。</p><ul><li>**RSA-** 可用于加密、密钥交换和签名。 RSA 加密必须只使用 OAEP 或 RSA-KEM 填充模式。 现有代码只能出于兼容目的使用 PKCS #1 v1.5 填充模式。 null 填充已被明确禁止使用。 对于新代码，要求使用 >= 2048 位的密钥。 现有代码只能出于兼容目的，在经得组织加密委员会的评审后，支持 < 2048 位的密钥。 < 1024 位的密钥只能用于解密/验证旧数据，对于加密或签名操作，必须改用其他密钥。</li><li>**ECDSA-** 只能用于签名。 对于新代码，要求使用密钥 >= 256 位的 ECDSA。 基于 ECDSA 的签名必须使用 NIST 批准的三种曲线算法之一（P-256、P-384 或 P521）。 经过全面分析的曲线算法只能在经得组织加密委员会的评审后使用。</li><li>**ECDH-** 只能用于密钥交换。 对于新代码，要求使用密钥 >= 256 位的 ECDH。 基于 ECDH 的密钥交换必须使用 NIST 批准的三种曲线算法之一（P-256、P-384 或 P521）。 经过全面分析的曲线算法只能在经得组织加密委员会的评审后使用。</li><li>在经得组织加密委员会的评审和批准后，可接受 **DSA-**。 请与安全顾问联系，安排组织加密委员会的评审。 如果使用 DSA 已获得批准，请注意，需要禁止使用长度小于 2048 位的密钥。 从 Windows 8 和更高版本开始，CNG 支持 2048 位和更长的密钥。</li><li>**Diffie-Hellman-** 只能用于会话密钥管理。 对于新代码，要求密钥长度 >= 2048 位。 现有代码只能出于向后兼容目的，在经得组织加密委员会的评审后，支持密钥长度 < 2048 位。 不可使用 < 1024 位的密钥。</li><ul>

## <a id="numgen"></a>使用批准的随机数生成器

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | <p>产品必须使用批准的随机数生成器。 因此，在此类代码中不得使用伪随机函数，例如 C 运行时函数 rand、.NET Framework 类 System.Random，或 GetTickCount 等系统函数。 禁止使用双重椭圆曲线随机数生成器 (DUAL_EC_DRBG) 算法</p><ul><li>**CNG-** BCryptGenRandom（除非调用方能够以大于 0 [即 PASSIVE_LEVEL] 的任何 IRQL 运行，否则建议使用 BCRYPT_USE_SYSTEM_PREFERRED_RNG 标志）</li><li>**CAPI-** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom（新的实现应使用 BCryptGenRandom 或 CryptGenRandom）* rand_s * SystemPrng（适用于内核模式）</li><li>**.NET-** RNGCryptoServiceProvider 或 RNGCng</li><li>**Windows 应用商店应用-** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom 或 .GenerateRandomNumber</li><li>**Apple OS X (10.7+)/iOS(2.0+)-** int SecRandomCopyBytes (SecRandomRef random, size_t count, uint8_t \*bytes )</li><li>**Apple OS X (<10.7)-** 使用 /dev/random 来检索随机数</li><li>**Java（包括 Google Android Java 代码）-** java.security.SecureRandom 类。 请注意，对于 Android 4.3 (Jelly Bean)，开发人员必须遵循 Android 建议的解决方法并更新其应用程序，使用 /dev/urandom 或 /dev/random 中的熵初始化 PRNG</li></ul>|

## <a id="stream-ciphers"></a>不要使用对称流加密法

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | 不得使用对称流加密法，例如 RC4。 产品不应使用对称流加密法，应使用块加密法，具体而言，密钥长度至少为 128 位的 AES。 |

## <a id="mac-hash"></a>使用批准的 MAC/HMAC/键控哈希算法

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | <p>产品必须只使用批准的消息身份验证代码 (MAC) 或基于哈希的消息身份验证代码 (HMAC) 算法。</p><p>消息身份验证代码 (MAC) 是附加到消息的信息片段，可让其接收方使用机密密钥来验证发送方的真实性和消息的完整性。 允许使用基于哈希的 MAC ([HMAC](http://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) 或[基于块加密法的 MAC](http://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf)，前提是所有底层哈希或对称加密算法的使用也已获批准；目前，这些算法包括 HMAC SHA2 函数（HMAC-SHA256、HMAC SHA384 和 HMAC SHA512），以及 CMAC/OMAC1 和 OMAC2 基于块加密法的 MAC（基于 AES）。</p><p>出于平台兼容性目的，允许使用 HMAC-SHA1，但需要对此过程申请例外处理，并接受组织加密委员会的评审。 不允许截断小于 128 位的 HMAC。 使用客户方法来哈希处理密钥和数据的措施未获批准，在使用之前必须接受组织加密委员会的评审。</p>|

## <a id="hash-functions"></a>只使用批准的加密哈希函数

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | <p>产品必须使用 SHA-2 系列的哈希算法（SHA256、SHA384 和 SHA512）。 如果需要更短的哈希（例如 128 位输出长度）来适应出于缩短 MD5 哈希目的而设计的数据结构，产品团队可以截断 SHA2 哈希之一（通常为 SHA256）。 请注意，SHA384 是 SHA512 的截断版本。 不允许出于安全目的将加密哈希截断至 128 位以下。 新代码必须使用 MD2、MD4、MD5、SHA-0、 SHA-1 或 RIPEMD 哈希算法。 对于这些算法，哈希冲突在计算上是可行的，这会有效地破解这些算法。</p><p>为使加密灵活可控而允许的 .NET 哈希算法（按优先顺序列出）：</p><ul><li>SHA512Cng（符合 FIPS）</li><li>SHA384Cng（符合 FIPS）</li><li>SHA256Cng（符合 FIPS）</li><li>SHA512Managed（不符合 FIPS）（在 HashAlgorithm.Create 或 CryptoConfig.CreateFromName 调用中使用 SHA512 作为算法名称）</li><li>SHA384Managed（不符合 FIPS）（在 HashAlgorithm.Create 或 CryptoConfig.CreateFromName 调用中使用 SHA384 作为算法名称）</li><li>SHA256Managed（不符合 FIPS）（在 HashAlgorithm.Create 或 CryptoConfig.CreateFromName 调用中使用 SHA256 作为算法名称）</li><li>SHA512CryptoServiceProvider（符合 FIPS）</li><li>SHA256CryptoServiceProvider（符合 FIPS）</li><li>SHA384CryptoServiceProvider（符合 FIPS）</li></ul>| 

## <a id="strong-db"></a>使用强加密算法加密数据库中的数据

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 数据库 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [选择加密算法](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **步骤** | 加密算法定义了未经授权的用户无法轻松逆转的数据转换。 SQL Server 允许管理员和开发人员从多种算法中选择，包括 DES、三重 DES、TRIPLE_DES_3KEY、RC2、RC4、128 位 RC4、DESX、128 位 AES、192 位 AES 和 256 位 AES |

## <a id="ssis-signed"></a>应该对 SSIS 包进行加密和数字签名

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 数据库 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [使用数字签名标识包源](https://msdn.microsoft.com/library/ms141174.aspx)、[威胁和漏洞缓解措施（集成服务）](https://msdn.microsoft.com/library/bb522559.aspx) |
| **步骤** | 包源是指创建包的个人或组织。 从未知或不受信任的源运行包可能有风险。 为了防止有人未经授权篡改 SSIS 包，应使用数字签名。 此外，为确保在存储/传输过程中包的机密性，必须将 SSIS 包加密 |

## <a id="securables-db"></a>将数字签名添加到关键的数据库安全对象

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 数据库 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [ADD SIGNATURE (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **步骤** | 如果必须验证关键数据库安全对象的完整性，应使用数字签名。 可将数据库安全对象（例如存储过程、函数、程序集或触发器）数字签名。 下面是数字签名的一个用例：假设某家 ISV（独立软件供应商）需要向其一个客户提供软件支持。 在提供支持之前，该 ISV 想要确保软件中的数据库安全对象不会因为人为失误或恶意入侵而遭到篡改。 如果将安全对象数字签名，该 ISV 就可以验证其数字签名和完整性。| 

## <a id="ekm-keys"></a>使用 SQL Server EKM 保护加密密钥

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 数据库 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [SQL Server 可扩展密钥管理 (EKM)](https://msdn.microsoft.com/library/bb895340)、[使用 Azure Key Vault (SQL Server) 可扩展密钥管理](https://msdn.microsoft.com/library/dn198405) |
| **步骤** | 使用 SQL Server 可扩展密钥管理可将保护数据库文件的加密密钥存储在智能卡、USB 设备或 EKM/HSM 模块等外部设备中。 数据库管理员（sysadmin 组的成员除外）还可以使用它启用数据保护。 可以使用外部 EKM/HSM 模块中只能由数据库用户访问的加密密钥来加密数据。 |

## <a id="keys-engine"></a>如果不应向数据库引擎透露加密密钥，请使用 AlwaysEncrypted 功能

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 数据库 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | SQL Azure、OnPrem |
| **属性**              | SQL 版本 - V12、MsSQL2016 |
| **参考**              | [Always Encrypted（数据库引擎）](https://msdn.microsoft.com/library/mt163865) |
| **步骤** | Always Encrypted 功能旨在保护 Azure SQL 数据库或 SQL Server 数据库中存储的敏感数据，例如信用卡号或国民身份证号（如美国社会安全号码）。 通过 Always Encrypted，客户可在客户端应用程序中加密敏感数据，永远不会向数据库引擎（SQL 数据库或 SQL Server）透露加密密钥。 因此，Always Encrypted 将数据所有者与数据管理者区分开来，前者可查看数据，而后者无权访问数据。 |

## <a id="keys-iot"></a>在 IoT 设备上安全存储加密密钥

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | IoT 设备 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 设备 OS - Windows IoT Core、设备连接、Azure IoT 设备 SDK |
| **参考**              | [Windows IoT Core 上的 TPM](https://developer.microsoft.com/windows/iot/docs/tpm)、[设置 Windows IoT Core 上的 TPM](https://developer.microsoft.com/windows/iot/win10/setuptpm)、[Azure IoT 设备 SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **步骤** | 对称或证书私钥安全存储在受硬件保护的存储（如 TPM 或智能卡芯片）中。 Windows 10 IoT Core 支持 TPM 用户，另外还可以使用多个兼容的 TPM：https://developer.microsoft.com/windows/iot/win10/tpm。 建议使用固件或离散 TPM。 软件 TPM 只应该用于开发和测试目的。 获取 TPM 并在其中预配密钥后，应该编写生成令牌的代码，但不要对令牌中的任何敏感信息进行硬编码。 | 

### <a name="example"></a>示例
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
可以看到，设备主密钥未出现在代码中， 而是存储在 TPM 的槽 0 中。 TPM 设备生成一个用于连接到 IoT 中心的短期生存的 SAS 令牌。 

## <a id="random-hub"></a>生成足够长的随机对称密钥用于向 IoT 中心进行身份验证

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | IoT 云网关 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 网关选项 - Azure IoT 中心 |
| **参考**              | 不适用  |
| **步骤** | IoT 中心包含设备标识注册表，在预配设备时，会自动生成随机对称密钥。 建议使用此项 Azure IoT 中心标识注册表功能来生成用于身份验证的密钥。 IoT 中心还允许在创建设备时指定密钥。 如果在设备预配期间在 IoT 中心外部生成密钥，建议创建随机对称密钥或至少 256 位的密钥。 |

## <a id="pin-remote"></a>确保实施要求使用 PIN 并允许远程擦除的设备管理策略

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Dynamics CRM 移动客户端 | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | 确保实施要求使用 PIN 并允许远程擦除的设备管理策略 |

## <a id="bitlocker"></a>确保实施要求 PIN/密码/自动锁定并加密所有数据的设备管理策略（例如 Bitlocker）

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Dynamics CRM Outlook 客户端 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | 确保实施要求 PIN/密码/自动锁定并加密所有数据的设备管理策略（例如 Bitlocker） |

## <a id="rolled-server"></a>使用标识服务器时确保滚动更新签名密钥

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 标识服务器 | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [标识服务器 - 密钥、签名和加密](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **步骤** | 使用标识服务器时确保滚动更新签名密钥。 参考部分中的链接说明了如何在不中断依赖于标识服务器的应用程序的情况下规划密钥滚动更新。 |

## <a id="client-server"></a>确保在标识服务器中使用强加密型客户端 ID 和客户端密码

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 标识服务器 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | <p>确保在标识服务器中使用强加密型客户端 ID 和客户端密码。 生成客户端 ID 和机密时，应遵循以下指导原则：</p><ul><li>以客户端 ID 形式生成随机 GUID</li><li>以机密形式生成加密的随机 256 位密钥</li></ul>|
