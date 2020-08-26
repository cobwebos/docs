---
title: Azure Key Vault 的新增功能 | Microsoft Docs
description: Azure Key Vault 的最新更新
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.date: 07/27/2020
ms.author: mbaldwin
ms.openlocfilehash: b469ea0d0a91cc4ca7f0602dffbcc63b31c60855
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588358"
---
# <a name="whats-new-for-azure-key-vault"></a>Azure Key Vault 的新增功能

> [!WARNING]
> 2020 年 7 月：对 Key Vault 进行了两个更新，它们可能会影响服务的实现：[Key Vault 软删除在默认情况下开启](#soft-delete-on-by-default)和 [Azure TLS 证书更改](#azure-tls-certificate-changes)。 有关详细信息，请参阅下文。

下面是 Azure Key Vault 的新增功能。 新增功能和改进也在 [Azure 更新 Key Vault 频道](https://azure.microsoft.com/updates/?category=security&query=Key%20vault)上进行了公布。

## <a name="soft-delete-on-by-default"></a>Key Vault 软删除在默认情况下开启

到 2020 年年底，软删除在默认情况下将对所有 Key Vault（新的和预先存在的）处于开启状态。 有关此可能的中断性变更的完整详细信息，以及查找受影响的 Key Vault 并预先更新它们的步骤，请参阅[对所有 Key Vault 启用软删除](soft-delete-change.md)。 

## <a name="azure-tls-certificate-changes"></a>Azure TLS 证书更改  

Microsoft 在将 Azure 服务更新为使用来自一组不同的根证书颁发机构 (CA) 的 TLS 证书。 此更改正在进行中，因为当前 CA 证书不符合某个 CA/浏览器论坛基线要求。

### <a name="when-will-this-change-happen"></a>此更改将在何时进行？

- Azure Active Directory (Azure AD) 服务在 2020 年 7 月 7 日开始此过渡。
- 所有新创建的 Azure TLS/SSL 终结点都包含链接到新根 CA 的已更新证书。 
- 现有 Azure 终结点将以分阶段的方法从 2020 年 8 月 13 日开始过渡，到 2020 年 10 月 26 日完成。

> [!IMPORTANT]
> 进行此更改之后，客户可能需要更新其应用程序，以防止在尝试连接到 Azure 服务时出现连接故障。 

### <a name="what-is-changing"></a>有什么变化？

如今，Azure 服务使用的大多数 TLS 证书都链接到以下根 CA：

| CA 的公用名 | 指纹 (SHA1) |
|--|--|
| [Baltimore CyberTrust 根](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

Azure 服务使用的 TLS 证书都链接到以下根 CA 之一：

| CA 的公用名 | 指纹 (SHA1) |
|--|--|
| [DigiCert 全局根 G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert 全局根 CA](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust 根](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D-TRUST 根类 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA 根证书颁发机构 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft EV ECC 根证书颁发机构 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20EV%20ECC%20Root%20Certificate%20Authority%202017.crt) | 6b1937abfd64e1e40daf2262a27857c015d6228d |

### <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>何时可以停用旧的中间指纹？

在 2021 年 2 月 15 日 之前，不会撤销当前 CA 证书。 在该日期后，可以从代码中删除旧指纹。

如果此日期发生更改，则会收到新的吊销日期通知。

### <a name="will-this-affect-me"></a>这是否会影响我？

我们预计**大多数 Azure 客户不会**受到影响。  但是，如果应用程序显式指定可接受 CA 的列表，则可能会受到影响。 这种做法称为证书固定。

下面是检测应用程序是否受影响的一些方式：

- 在源代码中搜索在[此处](https://www.microsoft.com/pki/mscorp/cps/default.htm)找到的任何 Microsoft IT TLS CAs 的指纹、公用名和其他证书属性。 如果存在匹配，则应用程序会受到影响。 若要解决此问题，请更新源代码，包括新的 CA。 作为最佳做法，请确保在简短通知时可以添加或编辑 CA。 行业法规要求在 7 天内替换 CA 证书，因此依赖于固定的客户需要迅速做出反应。

- 如果你的应用程序与 Azure API 或其他 Azure 务集成，并且你不确定它是否使用证书固定，请向应用程序供应商核实。

- 与 Azure 服务进行通信的不同操作系统和语言运行时可能需要额外步骤，才能正确利用这些新的根来构建证书链： 
    - Linux：许多分发版要求将上面列出的 CA 添加到 /etc/ssl/certs。 有关特定说明，请参阅分发的文档。
    - **Java**：确保 Java 密钥存储包含上面列出的 CA。
    - 在断开连接的环境中运行的 Windows：在断开连接的环境中运行的系统需要将上面列出的根添加到受信任的根证书颁发机构存储，并将中间证书添加到中间证书颁发机构存储。
    - Android：查看适用于你设备和 Android 版本的文档。
    - 其他硬件设备（尤其是 IoT）：联系设备制造商。 

- 如果环境中的防火墙规则设置为仅允许对特定证书吊销列表 (CRL) 下载和/或在线证书状态协议 (OCSP) 验证位置进行出站呼叫。 需要允许以下 CRL 和 OCSP URL：

    - http://crl3&#46;digicert&#46;com
    - http://crl4&#46;digicert&#46;com
    - http://ocsp&#46;digicert&#46;com
    - http://www&#46;d-trust&#46;net
    - http://root-c3-ca2-2009&#46;ocsp&#46;d-trust&#46;net
    - http://crl&#46;microsoft&#46;com
    - http://oneocsp&#46;microsoft&#46;com
    - http://ocsp&#46;msocsp&#46;com

## <a name="june-2020"></a>2020 年 6 月

Azure Monitor for Key Vault 现在为预览版。  Azure Monitor 提供 Key Vault 请求、性能、失败和延迟的统一视图，在其中可以全面监视密钥保管库。 有关详细信息，请参阅 [Azure Monitor for Key Vault（预览版）](../../azure-monitor/insights/key-vault-insights-overview.md)。

## <a name="may-2020"></a>2020 年 5 月

Key Vault“创建自己的密钥”(BYOK) 现已正式发布。 请参阅 [Azure Key Vault BYOK 规范](../keys/byok-specification.md)，并了解如何[将 HSM 保护的密钥导入 Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md)。

## <a name="march-2020"></a>2020 年 3 月

专用终结点现在提供预览版。 使用 Azure 专用链接服务，可以通过虚拟网络中的专用终结点访问 Azure Key Vault 和 Azure 托管的客户服务/合作伙伴服务。  了解如何[将 Key Vault 与 Azure 专用链接集成](private-link-service.md)。

## <a name="2019"></a>2019

- 发布下一代 Azure Key Vault SDK。 有关其用法的示例，请参阅适用于 [Python](../secrets/quick-create-python.md)、[.NET](../secrets/quick-create-net.md)、[Java](../secrets/quick-create-java.md) 和 [Node.js](../secrets/quick-create-node.md) 的 Azure Key Vault 机密快速入门
- 用于管理 Key Vault 证书的新 Azure 策略。 请参阅 [Key Vault 的 Azure Policy 内置定义](../policy-samples.md)。
- Azure Key Vault 虚拟机扩展现已正式发布。  请参阅[适用于 Linux 的 Key Vault 虚拟机扩展](../../virtual-machines/extensions/key-vault-linux.md)和[适用于 Windows 的 Key Vault 虚拟机扩展](../../virtual-machines/extensions/key-vault-windows.md)。
- Azure 事件网格中现在提供适用于 Azure Key Vault 的事件驱动机密管理。 有关详细信息，请参阅 [Azure Key Vault 中用于事件的事件网格架构](../../event-grid/event-schema-key-vault.md]，并了解如何[通过 Azure 事件网格接收和响应 Key Vault 通知](event-grid-tutorial.md)。

## <a name="2018"></a>2018

本年度发布的新功能和集成：

- 与 Azure Functions 集成。 有关利用 [Azure Functions](../../azure-functions/index.yml) 进行 Key Vault 操作的示例方案，请参阅[自动轮换机密](../secrets/tutorial-rotation.md)。 
- [与 Azure Databricks 集成](/azure/databricks/scenarios/store-secrets-azure-key-vault)。 借助此功能，Azure Databricks 现在支持两种类型的机密范围：Azure Key Vault 支持和 Databricks 支持。 有关详细信息，请参阅[创建 Azure Key Vault 支持的机密范围](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Azure Key Vault 的虚拟网络服务终结点](overview-vnet-service-endpoints.md)。

## <a name="2016"></a>2016

本年度发布的新功能：

- 托管存储帐户密钥。 新增了存储帐户密钥功能，可更轻松地与 Azure 存储集成。 参阅概述主题[托管存储帐户密钥概述](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys)，了解详细信息。
- 软删除。 软删除功能增强了 Key Vault 和 Key Vault 对象的数据保护。 参阅概述主题[软删除概述](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)，了解详细信息。

## <a name="2015"></a>2015

本年度发布的新功能：
- 证书管理。 2016 年 9 月 26 日，作为一种功能添加到 2015-06-01 正式版中。

2015 年 6 月 24 日发布了 2015-06-01 正式版。 此版本进行了以下更改： 
- 删除密钥 - 删除了“使用”字段。
- 获取有关密钥的信息 - 删除了“使用”字段。
- 将密钥导入保管库 - 删除了“使用”字段。
- 还原密钥 - 删除了“使用”字段。     
- 将“RSA_OAEP”改为 RSA 算法的“RSA-OAEP”。 请参阅[关于密钥、机密和证书](about-keys-secrets-certificates.md)。    
 
2015 年 4 月 20 日发布了第二版预览版（版本 2015-02-01-preview）。 有关详细信息，请参阅 [REST API 更新](https://docs.microsoft.com/archive/blogs/kv/rest-api-update) 博客文章。 更新了以下任务：
 
- 列出保管库中的密钥 - 向操作添加了分页支持。
- 列出密钥版本 - 添加了列出密钥版本的操作。  
- 列出保管库中的机密 - 添加了分页支持。
- 列出机密版本 - 添加了列出机密版本的操作。  
- 所有操作 - 添加了属性创建/更新时间戳。  
- 创建机密 - 向机密添加了 Content-Type。
- 创建密钥 - 添加了标记作为可选信息。
- 创建机密 - 添加了标记作为可选信息。
- 更新密钥 - 添加了标记作为可选信息。
- 更新机密 - 添加了标记作为可选信息。
- 将机密的最大大小从 10 KB 更改为 25 KB。 请参阅[关于密钥、机密和证书](about-keys-secrets-certificates.md)。    

## <a name="2014"></a>2014
 
2015 年 1 月 8 日发布了第一版预览版（版本 2014-12-08-preview）。  

## <a name="next-steps"></a>后续步骤

如果你有其他问题，请通过[支持](https://azure.microsoft.com/support/options/)联系我们。  
