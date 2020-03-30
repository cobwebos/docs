---
title: 为 B2B - Azure AD 设置与 AD FS 的直接联合
description: 了解如何将 AD FS 设置为直接联合的标识提供程序，以便来宾可以登录到 Azure AD 应用
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272829"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>示例：与活动目录联合服务 （AD FS） 的直接联合（预览）
|     |
| --- |
| 直接联合是 Azure 活动目录的公共预览功能。 有关预览的详细信息，请参阅 Microsoft [Azure 预览的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

本文介绍如何使用 Active 目录联合服务 （AD FS） 作为 SAML 2.0 或 WS-Fed 标识提供程序设置[直接联合](direct-federation.md)。 要支持直接联合，必须在标识提供程序配置某些属性和声明。 为了说明如何为直接联合配置标识提供程序，我们将使用 Active 目录联合服务 （AD FS） 作为示例。 我们将演示如何将 AD FS 设置为 SAML 标识提供程序和 WS-Fed 标识提供程序。

> [!NOTE]
> 本文介绍如何为 SAML 和 WS-Fed 设置 AD FS 以进行说明。 对于标识提供程序为 AD FS 的直接联合集成，我们建议使用 WS-Fed 作为协议。 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>为 SAML 2.0 直接联合配置 AD FS
Azure AD B2B 可以配置为与使用 SAML 协议且具有下面列出的特定要求的标识提供程序联合。 为了说明 SAML 配置步骤，本节演示如何为 SAML 2.0 设置 AD FS。 

要设置直接联合，必须在标识提供程序的 SAML 2.0 响应中接收以下属性。 可以通过链接到在线安全令牌服务 XML 文件或手动输入它们来配置这些属性。 [在创建测试 AD FS 实例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)中的步骤 12 描述了如何查找 AD FS 终结点或如何生成元数据`https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`URL，例如 。 

|特性  |“值”  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|读者     |`urn:federation:MicrosoftOnline`         |
|颁发者     |合作伙伴 IdP 的颁发者 URI，例如`http://www.example.com/exk10l6w90DHM0yi...`         |

需要在标识提供程序颁发的 SAML 2.0 令牌中配置以下声明：


|特性  |“值”  |
|---------|---------|
|名称 ID 格式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


下一节将说明如何使用 AD FS 作为 SAML 2.0 标识提供程序的示例配置所需的属性和声明。

### <a name="before-you-begin"></a>开始之前

在开始此过程之前，必须设置 AD FS 服务器并正常运行。 有关设置 AD FS 服务器的帮助，请参阅[在 Azure 虚拟机上创建测试 AD FS 3.0 实例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)。

### <a name="add-the-claim-description"></a>添加声明说明

1. 在 AD FS 服务器上，选择 **"工具** > **AD FS"管理**。
2. 在导航窗格中，选择 **"服务** > **声明说明**"。
3. 在 **"操作"** 下，选择 **"添加索赔说明**"。
4. 在 **"添加声明描述"** 窗口中，指定以下值：

   - **显示名称**： 持久标识符
   - **声明标识符**：`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - 选择联合**元数据中"发布此声明说明"复选框，作为此联合身份验证服务可以接受的声明类型**。
   - 选择联合**元数据中"发布此声明说明"复选框，作为此联合身份验证服务可以发送的声明类型**。

5. 单击“确定”****。

### <a name="add-the-relying-party-trust-and-claim-rules"></a>添加依赖方信任和索赔规则

1. 在 AD FS 服务器上，转到**工具** > **AD FS 管理**。
2. 在导航窗格中，选择**信任关系** > **依赖方信任**。
3. 在 **"操作"** 下，选择 **"添加依赖方信任**"。 
4. 在 **"选择数据源**"的添加依赖方信任向导中，使用"**导入有关联机或本地网络上发布的依赖方的数据"** 选项。 指定此联合元数据 URL- https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml。 保留其他默认选择。 选择“关闭”。
5. 将打开 **"编辑声明规则"** 向导。
6. 在 **"编辑声明规则"** 向导中，选择 **"添加规则**"。 在**选择规则类型**中，选择 **"发送 LDAP 属性作为声明**"。 选择“下一步”。
7. 在 **"配置声明规则"中**，指定以下值： 

   - **声明规则名称**：电子邮件声明规则 
   - **属性存储**： 活动目录 
   - **LDAP 属性**： 电子邮件地址 
   - **传出声明类型**： 电子邮件地址

8. 选择“完成”****。
9. "**编辑声明规则"** 窗口将显示新规则。 单击 **“应用”**。 
10. 单击“确定”****。  

### <a name="create-an-email-transform-rule"></a>创建电子邮件转换规则
1. 转到 **"编辑索赔规则**"，然后单击"**添加规则**"。 在 **"选择规则类型**"中，选择 **"转换传入声明**"，然后单击"**下一步**"。 
2. 在 **"配置声明规则"中**，指定以下值： 

   - **声明规则名称**：电子邮件转换规则 
   - **传入声明类型**：电子邮件地址 
   - **传出声明类型**：名称 ID 
   - **传出名称 ID 格式**： 持久标识符 
   - 选择“传递所有声明值”****。

3. 单击“完成”****。 
4. "**编辑声明规则"** 窗口将显示新规则。 单击 **“应用”**。 
5. 单击“确定”。 AD FS 服务器现在配置为使用 SAML 2.0 协议进行直接联合。

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>为 WS-Fed 直接联合配置 AD FS 
Azure AD B2B 可以配置为与使用 WS-Fed 协议且具有下面列出的特定要求的标识提供程序联合。 目前，两个 WS-Fed 提供程序已测试与 Azure AD 的兼容性，包括 AD FS 和 Shibboleth。 在这里，我们将使用活动目录联合服务 （AD FS） 作为 WS-Fed 标识提供程序的示例。 有关使用 Azure AD 在符合 WS-Fed 的提供程序之间建立依赖方信任的详细信息，请下载 Azure AD 标识提供程序兼容性文档。

要设置直接联合，必须在来自标识提供程序的 WS-Fed 消息中接收以下属性。 可以通过链接到在线安全令牌服务 XML 文件或手动输入它们来配置这些属性。 [在创建测试 AD FS 实例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)中的步骤 12 描述了如何查找 AD FS 终结点或如何生成元数据`https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`URL，例如 。
 
|特性  |“值”  |
|---------|---------|
|被动请求者端点     |`https://login.microsoftonline.com/login.srf`         |
|读者     |`urn:federation:MicrosoftOnline`         |
|颁发者     |合作伙伴 IdP 的颁发者 URI，例如`http://www.example.com/exk10l6w90DHM0yi...`         |

IdP 颁发的 WS-Fed 令牌所需的声明：

|特性  |“值”  |
|---------|---------|
|不可改变ID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

下一节将说明如何使用 AD FS 作为 WS-Fed 标识提供程序的示例配置所需的属性和声明。

### <a name="before-you-begin"></a>开始之前
在开始此过程之前，必须设置 AD FS 服务器并正常运行。 有关设置 AD FS 服务器的帮助，请参阅[在 Azure 虚拟机上创建测试 AD FS 3.0 实例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)。


### <a name="add-the-relying-party-trust-and-claim-rules"></a>添加依赖方信任和索赔规则 
1. 在 AD FS 服务器上，转到**工具** > **AD FS 管理**。 
1. 在导航窗格中，选择**信任关系** > **依赖方信任**。 
1. 在 **"操作"** 下，选择 **"添加依赖方信任**"。  
1. 在添加依赖方信任向导中，对于**选择数据源**，请使用选项 **"导入有关联机或本地网络上发布的依赖方的数据**"。 指定此联合元数据 URL： `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`。  保留其他默认选择。 选择“关闭”。
1. 将打开 **"编辑声明规则"** 向导。 
1. 在 **"编辑声明规则"** 向导中，选择 **"添加规则**"。 在 **"选择规则类型**"中，选择**使用自定义规则发送声明**。 选择“下一步”。 
1. 在 **"配置声明规则"中**，指定以下值：

   - **声明规则名称**：发出不可改变的 ID  
   - **自定义规则**：`c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. 选择“完成”****。 
1. "**编辑声明规则"** 窗口将显示新规则。 单击 **“应用”**。  
1. 在同一 **"编辑声明规则"** 向导中，选择 **"添加规则**"。 在**Cohose 规则类型中**，选择 **"发送 LDAP 属性作为声明**"。 选择“下一步”。
1. 在 **"配置声明规则"中**，指定以下值： 

   - **声明规则名称**：电子邮件声明规则  
   - **属性存储**： 活动目录  
   - **LDAP 属性**： 电子邮件地址  
   - **传出声明类型**： 电子邮件地址 

1.  选择“完成”****。 
1.  "**编辑声明规则"** 窗口将显示新规则。 单击 **“应用”**。  
1.  单击“确定”。 AD FS 服务器现在配置为使用 WS-Fed 进行直接联合。

## <a name="next-steps"></a>后续步骤
接下来，您将在 Azure AD 门户中或使用 PowerShell[配置直接联合](direct-federation.md#step-2-configure-direct-federation-in-azure-ad)。 
