<properties 
	pageTitle="将 Azure Multi-Factor Authentication 与 Azure AD 配合使用来保护云资源" 
	description="这是与 Azure Multi-Factor Authentication 相关的页面，介绍如何将 Azure MFA 与云中的 AD FS 配合使用。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.date="06/02/2015" 
	wacn.date=""/>

# 将 Azure Multi-Factor Authentication 与 Azure AD 配合使用来保护云资源

如果你的组织已与 Azure Active Directory 联合，并且你有可供 Azure Active Directory 访问的资源，则可以使用 Azure Multi-Factor Authentication 或 Active Directory 联合身份验证服务保护这些资源。使用以下过程可通过 Azure Multi-Factor Authentication 或 Active Directory 联合身份验证服务保护 Azure Active Directory 资源。

## 若要使用 AD FS 保护 Azure AD 资源，请执行以下操作： 



1. 使用[启用 Multi-Factor Authentication](/documentation/articles/multi-factor-authentication-get-started-cloud#turn-on-multi-factor-authentication-for-users) 中所述的步骤来为用户启用帐户。
2. 使用以下过程设置声明规则：

![云](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)

- 	启动 AD FS 管理控制台。
- 	导航到“依赖方信任”，然后右键单击“依赖方信任”。选择“编辑声明规则...”
- 	单击“添加规则...”
- 	从下拉列表中选择“使用自定义规则发送声明”，然后单击“下一步”。
- 	输入声明规则的名称。
- 	在“自定义规则”下：添加以下内容：


		=> issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");

	相应的声明：

		<saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
		<saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
		</saml:Attribute>
- 单击“确定”。单击“完成”。关闭 AD FS 管理控制台。

然后，用户可以使用本地方法（如智能卡）完成登录。

## 联合用户的受信任 IP
受信任的 IP 可让管理员针对特定的 IP 地址或针对从他们自己的 Intranet 发出请求的联合用户，跳过 Multi-Factor Authentication。以下部分将介绍当请求是来自联合用户的 Intranet 时，如何配置联合用户的 Azure Multi-Factor Authentication 受信任 IP，以及跳过 Multi-Factor Authentication。这是通过将 AD FS 配置为使用“传递或筛选传入声明”模板与“公司网络内部”声明类别来实现的。此示例使用 Office 365 作为信赖方信任。

### 配置 AD FS 声明规则

我们要做的第一件事是配置 AD FS 声明。我们将创建两个声明规则，一个用于“公司网络内部”声明类型，另一个用于保持用户登录。

1. 打开“AD FS 管理”。
2. 在左侧选择“信赖方信任”。
3. 在中间，右键单击“Microsoft Office 365 标识平台”，然后选择“编辑声明规则...”![云](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. 在“颁发转换规则”上，单击“添加规则”。![云](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. 在“添加转换声明规则向导”上，从下拉列表中选择“传递或筛选传入声明”，然后单击“下一步”。![云](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. 在“声明规则名称”旁边的框中，为规则指定名称。例如：InsideCorpNet。
7. 从“传入声明类型”旁边的下拉列表中，选择“公司网络内部”。![云](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. 单击“完成”。
9. 在“颁发转换规则”上，单击“添加规则”。
10. 在“添加转换声明规则向导”上，从下拉列表中选择“使用自定义规则发送声明”，然后单击“下一步”。
11. 在“声明规则名称”下的框中：输入“保持用户登录状态”。
12. 在“自定义规则”框中，输入：
	    
		c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
			=> issue(claim = c);
![云](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. 单击“完成”。
14. 单击“应用”。
15. 单击“确定”。
16. 关闭“AD FS 管理”。



### 配置联合用户的 Azure Multi-Factor Authentication 受信任 IP
创建声明后，我们可以开始配置受信任的 IP。

1. 登录到 Azure 管理门户。
2. 在左侧单击“Active Directory”。
3. 在“目录”下，单击要设置受信任 IP 的目录。
4. 在选择的目录上，单击“配置”。
5. 在“Multi-Factor Authentication”部分中，单击“管理服务设置”。
6. 在“服务设置”页的“受信任 IP”下，选择“用于联合用户从我的 Intranet 发起的请求”。![云](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. 单击“保存”。
8. 应用更新后，单击“关闭”。


就这么简单！ 现在，仅当声明来自公司 Intranet 外部时，Office 365 联合用户才需要使用 MFA。

<!---HONumber=69-->