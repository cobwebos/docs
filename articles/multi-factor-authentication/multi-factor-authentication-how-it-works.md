<properties 
	pageTitle="Azure Multi-Factor Authentication - 工作原理" 
	description="Azure Multi-Factor Authentication 可帮助保护对数据和应用程序的访问，同时可以满足用户对简单登录过程的需求。它通过要求第二种形式的身份验证提供额外的安全性，并通过一系列简单的身份验证选项提供增强式身份验证。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.date="08/04/2016"
	wacn.date=""/>

#Azure Multi-Factor Authentication 的工作原理

多因素身份验证的安全性在于它的分层方法。破坏多因素身份验证系统对于攻击者来说是巨大的挑战。即使攻击者设法得到用户的密码，如果没有同时占有可信设备也没有用处。如果用户丢失了设备，捡到该设备的人也无法使用它，除非他（她）也知道该用户的密码。

![验证](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure Multi-Factor Authentication 可帮助保护对数据和应用程序的访问，同时可以满足用户对简单登录过程的需求。它通过要求第二种形式的身份验证提供额外的安全性，并通过一系列简单的身份验证选项提供增强式身份验证：

- 电话呼叫
- 短信
- 移动应用通知 - 允许用户选择偏好的方法
- 移动应用验证码
- 第三方 OATH 令牌



##可用于 Multi-Factor Authentication 的方法
当用户登录时，系统会将额外的身份验证发送给该用户。以下是可用于这种二次身份验证的方法列表。

验证方法 | 说明 
------------- | ------------- |
电话呼叫 | 向用户的智能手机拨打电话，要求他们按 # 号来验证其登录。这样就会完成验证过程。此选项可配置，并可以更改为你指定的代码。
短信 | 将包含 6 位数代码的短信发送到用户的智能手机。输入此代码即可完成验证过程。
移动应用通知 | 将验证请求发送到用户的智能手机，要求他们通过在移动应用中选择“验证”来完成验证。如果你将应用通知选作主要验证方法，则会发生这种情况。如果用户在未登录时收到通知，他们可以选择将通知举报为欺诈。
移动应用的验证码 | 将验证码发送到用户智能手机中运行的移动应用。如果将验证码选作主要验证方法，则会发生这种情况。


##可用的 Azure Multi-Factor Authentication 版本
Azure Multi-Factor Authentication 有三个不同的版本。下表较详细地描述了每个版本。

版本 | 说明 
------------- | ------------- |
适用于 Office 365 的 Multi-Factor Authentication | 此版本专门与 Office 365 应用程序配合使用，可以从 Office 365 门户进行管理。因此，管理员现在可以借助 Multi-Factor Authentication 来保护其 Office 365 资源。Office 365 订阅随附了此版本。
面向 Azure 管理员的 Multi-Factor Authentication | 适用于 Office 365 的 Multi-Factor Authentication 功能的相同子集将免费提供给所有 Azure 管理员使用。Azure 订阅的每个管理员帐户现在可以通过启用这项核心 Multi-Factor Authentication 功能来获得更多的保护。因此，如果某个管理员想要访问 Azure 门户以创建 VM 和网站以及管理存储、移动服务或任何其他 Azure 服务，则可在其管理员帐户中添加多因素身份验证。
Azure Multi-Factor Authentication | Multi-Factor Authentication 提供最丰富的功能集。<br><br>它通过 Azure 管理门户、高级报告及支持一系列本地和云应用程序来提供其他配置选项。Azure Multi-Factor Authentication 可以通过单独的许可证购买，也可能与 Azure Active Directory Premium 和 Enterprise Mobility Suite 捆绑在一起。<br><br>还可以通过在 Azure 订阅中创建 Azure 多重身份验证提供程序，根据使用量购买。
##版本功能比较
下表提供了 Azure Multi-Factor Authentication 各版本中可用的功能列表。


功能 | 适用于 Office 365 的 Multi-Factor Authentication（随附在 Office 365 SKU 中）|面向 Azure 管理员的 Multi-Factor Authentication（随附在 Azure 订阅中） | Azure Multi-Factor Authentication（随附在 Azure AD Premium 和企业移动套件中） 
------------- | :-------------: |:-------------: |:-------------: |
管理员可以使用 MFA 保护帐户| * | *（仅适用于 Azure 管理员帐户）|*
将移动应用用作第二个因素|* | * | *
将电话呼叫用作第二个因素|* | * | *
将短信用作第二个因素|* | * | *
不支持 MFA 的客户端的应用密码|* | * | *
管理员控制身份验证方法| *| *| *
PIN 模式| | | *
欺诈警报| | | *
MFA 报告| | | *
一次性跳过| | | *
通话的自定义问候语| | | *
可自定义来电者 ID| | | *
事件确认| | | *
受信任的 IP| | | *
暂停对已记住的设备进行 MFA（公共预览版）| | | *
MFA SDK| | | *
对使用 MFA 服务器的本地应用程序进行 MFA| | | *


##如何获取 Azure Multi-Factor Authentication

如果你想要获取 Azure Multi-Factor Authentication 提供的完整功能，而不只是针对 Office 365 用户和 Azure 管理员提供的功能，可以使用多个选项：

1.	购买 Azure Multi-Factor Authentication 许可证并将其分配给用户。
2.	购买随附了 Azure Multi-Factor Authentication 的许可证（例如 Azure Active Directory Premium 或 Enterprise Mobility Suite）并将其分配给用户。
3.	在 Azure 订阅中创建 Azure Multi-Factor Authentication 提供程序。如果还没有 Azure 订阅，可以注册 Azure 试用版订阅。在试用过期之前，需要将试用订阅转换为常规订阅。

使用 Azure Multi-Factor Authentication 提供程序时，有两种使用模式（通过 Azure 订阅计费）可用：

- **按用户**。通常适用于想要为固定数目的、需要定期进行身份验证的员工启用 Multi-Factor Authentication 的企业。
- **按身份验证**。通常适用于想要为大量的、不定期需要身份验证的外部用户启用 Multi-Factor Authentication 的企业。

有关价格详细信息，请参阅 [Azure MFA 定价](/pricing/details/multi-factor-authentication/)。

选择最适合你的组织的按用户或按使用量模式。接下来，若要开始使用，请参阅[入门](/documentation/articles/multi-factor-authentication-get-started/)



 

<!---HONumber=Mooncake_0919_2016-->