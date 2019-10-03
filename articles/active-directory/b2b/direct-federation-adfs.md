---
title: 设置针对 B2B 的 Azure Active Directory 与 AD FS 直接联合 |Microsoft Docs
description: 了解如何设置 AD FS 直接联合身份验证的标识提供程序作为以便来宾可以登录到 Azure AD 应用
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
ms.openlocfilehash: a8f709186f0ef17e037c4203118be07ea2d4f511
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544317"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>示例：直接联合身份验证与 Active Directory 联合身份验证服务 (AD FS) （预览版）
|     |
| --- |
| 直接联合身份验证是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

本文介绍如何设置[直接联合身份验证](direct-federation.md)使用 Active Directory 联合身份验证服务 (AD FS) 作为 SAML 2.0 或 WS 联合身份验证标识提供程序。 若要支持直接联合身份验证，某些属性和声明必须在配置标识提供程序。 若要说明如何配置直接联合身份验证的标识提供程序，我们将使用 Active Directory 联合身份验证服务 (AD FS) 作为示例。 我们将介绍如何设置 AD FS 作为 SAML 标识提供者和 WS 联合身份验证标识提供程序。

> [!NOTE]
> 本文介绍如何设置了 AD FS SAML 和 Ws-fed，为了便于说明。 其中标识提供者是 AD FS 直接联合身份验证集成，建议使用 WS 联合身份验证作为协议。 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>配置 AD FS 进行直接联合身份验证的 SAML 2.0
可以配置 azure AD B2B 进行联合身份验证使用与下面列出的特定要求使用 SAML 协议的标识提供者。 为了说明 SAML 配置步骤，本部分演示如何设置对于 SAML 2.0,azure AD FS。 

若要设置直接联合身份验证，必须从标识提供程序的 SAML 2.0 响应中收到以下属性。 通过将链接到在线安全令牌服务的 XML 文件或通过手动输入，可以配置这些属性。 在步骤 12[创建测试 AD FS 实例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)介绍如何查找 AD FS 终结点或如何生成你的元数据 URL，例如`https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`。 

|特性  |值  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|目标受众     |`urn:federation:MicrosoftOnline`         |
|颁发者     |颁发者 URI 的示例中的 IdP，合作伙伴 `http://www.example.com/exk10l6w90DHM0yi...`         |

需要在标识提供者颁发的 SAML 2.0 标记中配置以下声明：


|特性  |值  |
|---------|---------|
|NameID 格式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


下一节说明了如何配置必需的属性和作为 SAML 2.0 标识提供程序的示例使用 AD FS 的声明。

### <a name="before-you-begin"></a>开始之前

必须已设置的 AD FS 服务器上移和在开始此过程之前正常运行。 设置 AD FS 服务器的帮助，请参阅[创建 Azure 虚拟机上测试 AD FS 3.0 实例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)。

### <a name="add-the-claim-description"></a>添加声明说明

1. 在 AD FS 服务器上，选择**工具** > **AD FS 管理**。
2. 在导航窗格中，选择**服务** > **声明说明**。
3. 下**操作**，选择**添加声明说明**。
4. 在中**添加声明说明**窗口中，指定以下值：

   - **显示名称**:永久标识符
   - **声明标识符**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - 选中的复选框**作为此联合身份验证服务可以接受的声明类型在联合身份验证元数据中发布此声明说明**。
   - 选中的复选框**作为此联合身份验证服务可以发送的声明类型在联合身份验证元数据中发布此声明说明**。

5. 单击“确定”  。

### <a name="add-the-relying-party-trust-and-claim-rules"></a>添加信赖方信任和声明规则

1. 在 AD FS 服务器上，转到**工具** > **AD FS 管理**。
2. 在导航窗格中，选择**信任关系** > **信赖方信任**。
3. 下**操作**，选择**添加信赖方信任**。 
4. 添加信赖方信任向导**选择数据源**，使用选项**导入数据有关信赖方联机或本地网络上发布**。 指定此联合身份验证的元数据 URL- https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml 。 保留其他默认选择。 选择“关闭”。 
5. **编辑声明规则**向导随即打开。
6. 在中**编辑声明规则**向导中，选择**添加规则**。 在中**选择规则类型**，选择**以声明方式发送 LDAP 属性**。 选择“**下一步**”。
7. 在中**配置声明规则**，指定以下值： 

   - **声明规则名称**:电子邮件声明规则 
   - **属性存储**:Active Directory 
   - **LDAP 属性**:E-Mail-Addresses 
   - **传出声明类型**:电子邮件地址

8. 选择“完成”。 
9. **编辑声明规则**窗口将显示新规则。 单击“应用”  。 
10. 单击“确定”  。  

### <a name="create-an-email-transform-rule"></a>创建电子邮件转换规则
1. 转到**编辑声明规则**然后单击**添加规则**。 在中**选择规则类型**，选择**转换传入声明**然后单击**下一步**。 
2. 在中**配置声明规则**，指定以下值： 

   - **声明规则名称**:电子邮件转换规则 
   - **传入声明类型**:电子邮件地址 
   - **传出声明类型**:名称 ID 
   - **传出名称 ID 格式**:永久标识符 
   - 选择“传递所有声明值”  。

3. 单击 **“完成”** 。 
4. **编辑声明规则**窗口将显示新规则。 单击“应用”  。 
5. 单击“确定”。  AD FS 服务器现在已配置为直接使用 SAML 2.0 协议的联合身份验证。

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>配置 AD FS 的 WS 联合身份验证直接联合身份验证 
可以配置 azure AD B2B 进行与标识提供者与下面列出的特定要求使用 WS 联合身份验证协议的联合身份验证。 目前，Azure AD 与兼容性包括 AD FS 和 Shibboleth 两个 WS 联合身份验证提供程序已经过测试。 在这里，我们将使用 Active Directory 联合身份验证服务 (AD FS) 作为 WS 联合身份验证标识提供程序的示例。 有关建立符合 WS 联合身份验证提供程序与 Azure AD 之间的信赖方信任的详细信息，请下载 Azure AD 标识提供程序兼容性文档。

若要设置直接联合身份验证，必须从标识提供程序的 WS 联合身份验证消息中收到以下属性。 通过将链接到在线安全令牌服务的 XML 文件或通过手动输入，可以配置这些属性。 在步骤 12[创建测试 AD FS 实例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)介绍如何查找 AD FS 终结点或如何生成你的元数据 URL，例如`https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`。
 
|特性  |值  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|目标受众     |`urn:federation:MicrosoftOnline`         |
|颁发者     |颁发者 URI 的示例中的 IdP，合作伙伴 `http://www.example.com/exk10l6w90DHM0yi...`         |

必需的 IdP 颁发的 WS 联合身份验证令牌的声明：

|特性  |值  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

下一节说明如何配置所需的属性和声明作为 WS 联合身份验证标识提供程序的示例使用 AD FS。

### <a name="before-you-begin"></a>开始之前
必须已设置的 AD FS 服务器上移和在开始此过程之前正常运行。 设置 AD FS 服务器的帮助，请参阅[创建 Azure 虚拟机上测试 AD FS 3.0 实例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)。


### <a name="add-the-relying-party-trust-and-claim-rules"></a>添加信赖方信任和声明规则 
1. 在 AD FS 服务器上，转到**工具** > **AD FS 管理**。 
1. 在导航窗格中，选择**信任关系** > **信赖方信任**。 
1. 下**操作**，选择**添加信赖方信任**。  
1. 在为信赖方信任向导中，添加**选择数据源**，使用选项**导入数据有关信赖方联机或本地网络上发布**。 指定此联合身份验证元数据 URL: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`。  保留其他默认选择。 选择“关闭”。 
1. **编辑声明规则**向导随即打开。 
1. 在中**编辑声明规则**向导中，选择**添加规则**。 在中**选择规则类型**，选择**使用自定义规则发送声明**。 选择“*下一步*”。 
1. 在中**配置声明规则**，指定以下值：

   - **声明规则名称**:问题不可变 Id  
   - **自定义规则**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. 选择“完成”。  
1. **编辑声明规则**窗口将显示新规则。 单击“应用”  。  
1. 在同一**编辑声明规则**向导中，选择**添加规则**。 在中**Cohose 规则类型**，选择**以声明方式发送 LDAP 属性**。 选择“**下一步**”。
1. 在中**配置声明规则**，指定以下值： 

   - **声明规则名称**:电子邮件声明规则  
   - **属性存储**:Active Directory  
   - **LDAP 属性**:E-Mail-Addresses  
   - **传出声明类型**:电子邮件地址 

1.  选择“完成”。  
1.  **编辑声明规则**窗口将显示新规则。 单击“应用”  。  
1.  单击“确定”。  AD FS 服务器现在已配置为直接使用 WS 联合身份验证的联合身份验证。

## <a name="next-steps"></a>后续步骤
接下来，将[在 Azure AD 中配置直接联合身份验证](direct-federation.md#step-2-configure-direct-federation-in-azure-ad)在 Azure AD 门户或使用 PowerShell。 
