<properties 
	pageTitle="使用 Azure AD 通过首次运行体验设置新设备 | Microsoft Azure" 
	description="本主题说明用户在其首次运行体验过程中如何设置 Azure AD Join。" 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="swadhwa" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.date="08/02/2015" 
	wacn.date=""/>

# 通过 Microsoft Passport 在不使用密码的情况下进行身份验证

单纯使用密码进行身份验证的传统方法不足以保障用户的安全。使用这种方法时，用户需重复使用密码，有时还会忘记密码。密码可能会泄露、被骗取、易于破解且可猜出。密码也很难记住，并且易于遭受攻击，例如“传递哈希”。

## 什么是 Microsoft Passport
对于组织和消费者而言，Microsoft Passport 是基于私钥/公钥或证书的身份验证方法，它比密码身份验证更有优势。这种形式的身份验证依赖于这些密钥对凭据，而这些凭据可以取代密码且能抵御漏洞、盗窃和网络钓鱼。Microsoft Passport 使用户能够向 Microsoft 帐户、Active Directory 帐户、Microsoft Azure Active Directory (AD) 帐户或者支持 Fast ID Online (FIDO) 身份验证的非 Microsoft 服务进行身份验证。在 Microsoft Passport 注册期间完成最初的两步骤身份验证之后，将在用户的设备上设置 Microsoft Passport，并且该用户需设置一个手势（可能是 Windows Hello 或 PIN）。提供的手势用于验证身份；Windows 接着使用 Microsoft Passport 来对用户进行身份验证，并帮助他们访问受保护的资源和服务。

私钥可以单独以“用户手势”的形式提供，例如，PIN、生物特征、远程设备（如用户用于登录设备的智能卡），此信息将链接到证书或非对称密钥对。如果设备上装配了受信任平台模块 (TPM) 芯片，此私钥将会通过硬件认证。私钥永远不会离开设备。

公钥已注册到 Azure Active Directory 和 Windows Server Active Directory（以便在本地使用）。标识提供者 (IDP) 通过将用户的公钥映射到私钥来验证用户的身份，并通过一次性密码 (OTP)、Phonefactor 或不同的通知机制来提供登录信息。
## 企业为何应采用 Microsoft Passport
启用 Microsoft Passport 后，企业可通过以下措施使其资源变得更安全：

* 使用硬件偏好的选项来设置 Microsoft Passport，这意味着要在 TPM 1.2 或 TPM 2.0 可用时在其上生成密钥，或者在 TPM 不可用时通过软件生成密钥。 

* 定义 PIN 的复杂性和长度，以及是否要在组织中启用 Hello

* 配置 Microsoft Passport 可以使用基于证书的信任来支持类似于智能卡的方案。

## 工作原理
1. 密钥在硬件上生成。许多计算机都有内置的受信任平台模块 (TPM) 芯片，通过将加密密钥集成到设备来保护硬件。TPM 1.2 或 TPM 2.0 可用于生成密钥，或者与生成的密钥隔离的证书。

2. 这些硬件绑定的密钥将要通过 TPM 的认证，企业可以

3. 单个解锁手势将解除设备锁定，如果设备已加入域或已加入 Azure AD，则允许使用此手势获取对多个资源的访问权限。

## Microsoft Passport 生命周期
Microsoft Passport 身份验证生命周期 
![](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png) 
上图演示了私钥-公钥对，以及标识提供者所提供的验证。下面将详细解释其中的每个步骤：

1. 用户通过多个内置验证方法（手势、物理智能卡、Multi-Factor Authentication）证明其身份，并将此信息发送到 Azure Active Directory 或 Active Directory 等标识提供者 (IDP)。

2.  然后，设备将创建密钥、认证密钥、接收此密钥的公开部分、为其附加站点声明、登录并将它发送到 IDP 以注册此密钥。

3. 在 IDP 中注册密钥的公开部分之后，IDP 将会质询设备，以使用密钥的私密部分进行签名。然后，IDP 将执行验证并颁发可让用户访问受保护资源的身份验证令牌。

4. 在 IDP 中注册密钥的公开部分之后，IDP 将在设备上质询，以使用密钥的私密部分进行签名。

5. 然后，IDP 将执行验证并颁发可让用户和设备访问受保护资源的身份验证令牌。IDP 可以编写跨平台应用，或者通过 JS/Webcrypto API 使用浏览器支持为其用户创建和使用 Microsoft Passport 凭据。

## 部署要求
在企业级别
---------------------------
* Azure 订阅

在用户级别
-------------------------------------------------------------
* 计算机必须运行 Windows 10 Professional 或 Enterprise SKU

## 其他信息

* [通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-user-upgrade)
* [设置 Azure AD Join](active-directory-azureadjoin-setup)

<!---HONumber=79-->