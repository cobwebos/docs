---
title: 使用 B2B AD FS 设置直接联盟-Azure AD
description: 了解如何将 AD FS 设置为直接联合身份验证的标识提供者，以便来宾可以登录到 Azure AD 应用
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e350d6338b6ca589ab18d068ef6a314363fe205c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272829"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>示例：与 Active Directory 联合身份验证服务（AD FS）的直接联合（预览）
|     |
| --- |
| 直接联合是 Azure Active Directory 的公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

本文介绍如何设置使用 Active Directory 联合身份验证服务（AD FS）作为 SAML 2.0 或 WS-ADDRESSING 标识提供者的[直接联合](direct-federation.md)。 若要支持直接联合，必须在标识提供程序中配置某些属性和声明。 为了说明如何配置直接联合的标识提供程序，我们将使用 Active Directory 联合身份验证服务（AD FS）作为示例。 我们将演示如何将 AD FS 设置为 SAML 标识提供者和 WS-FEDERATION 标识提供者。

> [!NOTE]
> 本文介绍如何为 SAML 和 WS-FEDERATION 设置 AD FS 以便于说明目的。 对于 AD FS 标识提供者的直接联合身份验证集成，建议使用 WS 作为协议。 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>为 SAML 2.0 直接联合配置 AD FS
Azure AD B2B 可以配置为与使用 SAML 协议的标识提供者联合，该标识提供程序具有以下列出的特定要求。 为了说明 SAML 配置步骤，本部分说明如何设置 SAML 2.0 的 AD FS。 

若要设置直接联合身份验证，必须从标识提供者的 SAML 2.0 响应接收以下属性。 可以通过链接到联机 security token service XML 文件或手动输入这些属性来配置这些属性。 [创建测试 AD FS 实例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)中的步骤12说明了如何查找 AD FS 终结点或如何生成元数据 URL，例如 `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`。 

|属性  |值  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|目标受众     |`urn:federation:MicrosoftOnline`         |
|颁发者     |合作伙伴 IdP 的颁发者 URI，例如 `http://www.example.com/exk10l6w90DHM0yi...`         |

以下声明需要在由标识提供程序颁发的 SAML 2.0 令牌中进行配置：


|属性  |值  |
|---------|---------|
|NameID 格式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


下一节说明如何使用 AD FS 作为 SAML 2.0 标识提供者的示例来配置所需的属性和声明。

### <a name="before-you-begin"></a>开始之前

在开始此过程之前，必须已设置 AD FS 服务器并且工作正常。 有关设置 AD FS 服务器的帮助，请参阅[在 Azure 虚拟机上创建测试 AD FS 3.0 实例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)。

### <a name="add-the-claim-description"></a>添加声明说明

1. 在 AD FS 服务器上，选择 "**工具**" > **AD FS 管理**"。
2. 在导航窗格中，选择 "**服务** > **声明说明**"。
3. 在 "**操作**" 下，选择 "**添加声明说明**"。
4. 在 "**添加声明说明**" 窗口中，指定以下值：

   - **显示名称**：永久性标识符
   - **声明标识符**： `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - 选中 "**在联合元数据中发布此声明说明" 复选框作为此联合身份验证服务可以接受的声明类型**。
   - 选中 "**在联合元数据中发布此声明说明" 复选框作为此联合身份验证服务可以发送的声明类型**。

5. 单击“确定”。

### <a name="add-the-relying-party-trust-and-claim-rules"></a>添加信赖方信任和声明规则

1. 在 AD FS 服务器上， > **AD FS 管理**"中转到"**工具**"。
2. 在导航窗格中，选择 "**信任关系**" > **信赖方信任**"。
3. 在 "**操作**" 下，选择 "**添加信赖方信任**"。 
4. 在 "添加信赖方信任向导" 的 "**选择数据源**" 中，使用选项 "**导入有关联机或本地网络上发布的信赖方的数据**"。 指定此联合元数据 URL- https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml。 保留其他默认选项。 选择“关闭”。
5. 此时将打开 "**编辑声明规则**向导"。
6. 在 "**编辑声明规则**" 向导中，选择 "**添加规则**"。 在 "**选择规则类型**" 中，选择 "**以声明方式发送 LDAP 属性**"。 选择“**下一步**”。
7. 在 "**配置声明规则**" 中，指定以下值： 

   - **声明规则名称**：电子邮件声明规则 
   - **属性存储**： Active Directory 
   - **LDAP 属性**：电子邮件地址 
   - **传出声明类型**：电子邮件地址

8. 选择“完成”。
9. "**编辑声明规则**" 窗口将显示新规则。 单击“应用”。 
10. 单击“确定”。  

### <a name="create-an-email-transform-rule"></a>创建电子邮件转换规则
1. 单击 "**编辑声明规则**"，然后单击 "**添加规则**"。 在 "**选择规则类型**" 中，选择 "**转换传入声明**" 并单击 "**下一步**"。 
2. 在 "**配置声明规则**" 中，指定以下值： 

   - **声明规则名称**：电子邮件转换规则 
   - **传入声明类型**：电子邮件地址 
   - **传出声明类型**：名称 ID 
   - **传出名称 ID 格式**：永久性标识符 
   - 选择“传递所有声明值”。

3. 单击“**完成**”。 
4. "**编辑声明规则**" 窗口将显示新规则。 单击“应用”。 
5. 单击“确定”。 现在，使用 SAML 2.0 协议为直接联盟配置 AD FS 服务器。

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>为 WS-FEDERATION 直接联合身份验证配置 AD FS 
Azure AD B2B 可配置为与使用 WS 送单协议的标识提供者联合，并使用下面列出的特定要求。 目前，两个 WS-ADDRESSING 提供程序已测试兼容性 Azure AD 包括 AD FS 和 Shibboleth。 在这里，我们将使用 Active Directory 联合身份验证服务（AD FS）作为 WS-ADDRESSING 标识提供者的示例。 若要详细了解如何在与 Azure AD 的 WS 兼容的提供程序之间建立信赖方信任，请下载 Azure AD 标识提供程序兼容性文档。

若要设置直接联合身份验证，必须在来自标识提供程序的 WS 送送消息中接收下列属性。 可以通过链接到联机 security token service XML 文件或手动输入这些属性来配置这些属性。 [创建测试 AD FS 实例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)中的步骤12说明了如何查找 AD FS 终结点或如何生成元数据 URL，例如 `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`。
 
|属性  |值  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|目标受众     |`urn:federation:MicrosoftOnline`         |
|颁发者     |合作伙伴 IdP 的颁发者 URI，例如 `http://www.example.com/exk10l6w90DHM0yi...`         |

IdP 颁发的 WS-AT 令牌所需的声明：

|属性  |值  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

下一节说明如何使用 AD FS 作为 WS-ADDRESSING 标识提供程序的示例来配置所需的属性和声明。

### <a name="before-you-begin"></a>开始之前
在开始此过程之前，必须已设置 AD FS 服务器并且工作正常。 有关设置 AD FS 服务器的帮助，请参阅[在 Azure 虚拟机上创建测试 AD FS 3.0 实例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)。


### <a name="add-the-relying-party-trust-and-claim-rules"></a>添加信赖方信任和声明规则 
1. 在 AD FS 服务器上， > **AD FS 管理**"中转到"**工具**"。 
1. 在导航窗格中，选择 "**信任关系**" > **信赖方信任**"。 
1. 在 "**操作**" 下，选择 "**添加信赖方信任**"。  
1. 在 "添加信赖方信任向导" 中的 "**选择数据源**" 中，使用选项 "**导入有关联机或本地网络的信赖方的数据**"。 指定此联合元数据 URL： `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`。  保留其他默认选项。 选择“关闭”。
1. 此时将打开 "**编辑声明规则**向导"。 
1. 在 "**编辑声明规则**" 向导中，选择 "**添加规则**"。 在 "**选择规则类型**" 中，选择 "**使用自定义规则发送声明**"。 选择“*下一步*”。 
1. 在 "**配置声明规则**" 中，指定以下值：

   - **声明规则名称**：发出不可变的 Id  
   - **自定义规则**： `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. 选择“完成”。 
1. "**编辑声明规则**" 窗口将显示新规则。 单击“应用”。  
1. 在相同的**编辑声明规则**向导中，选择 "**添加规则**"。 在 " **Cohose 规则类型**" 中，选择 "**以声明方式发送 LDAP 属性**"。 选择“**下一步**”。
1. 在 "**配置声明规则**" 中，指定以下值： 

   - **声明规则名称**：电子邮件声明规则  
   - **属性存储**： Active Directory  
   - **LDAP 属性**：电子邮件地址  
   - **传出声明类型**：电子邮件地址 

1.  选择“完成”。 
1.  "**编辑声明规则**" 窗口将显示新规则。 单击“应用”。  
1.  单击“确定”。 现在，AD FS 服务器已配置为使用 WS-FEDERATION 进行直接联合。

## <a name="next-steps"></a>后续步骤
接下来，你将在 Azure AD 门户中或通过使用 PowerShell 在[Azure AD 中配置直接联合](direct-federation.md#step-2-configure-direct-federation-in-azure-ad)。 
