<properties 
	pageTitle="安装期间使用 Azure AD 设置新设备 | Microsoft Azure" 
	description="本主题说明用户在其首次运行体验过程中如何设置 Azure AD Join。" 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.date="08/02/2015" 
	wacn.date=""/>

# 安装期间使用 Azure AD 设置新设备

在 Windows 10 中，用户可以在首次运行体验 (FRX) 期间将其设备添加到 Azure AD。这样，组织就可以将拆装的设备分发给员工或学生，或者让他们选择自己的设备 (CYOD)。如果你安装 Windows 10 的 Professional 或 Enterprise SKU，则体验将默认为安装公司拥有的设备。

将设备加入 Azure AD
-----------------------------------------------------------------------

1. 在完成**准备**阶段之后，安装向导中会显示提示。
2. 首先，请自定义设备的区域和语言，接受 EULA，然后联机。
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png) </center>
3. 选择用于连接 Internet 的网络。
4. 选择设备是个人设备还是公司拥有的设备：
5. 单击“此设备属于我的组织”。随后将启动 Azure AD Join 体验。下面是 Windows 10 Professional SKU 中出现的屏幕。 
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png) </center>

6.	输入组织提供给你的凭据。
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
7.	输入用户名后，Azure AD 中会出现匹配的租户。如果你在联合域中，系统会将你重定向到本地安全令牌服务 (STS) 服务器（例如 AD FS）。如果你是非联合域中的用户，则必须直接在 Azure AD 托管页上输入你的凭据。你还会看到你的组织徽标和支持文本（如果已配置“公司品牌”）。
8.	然后，系统会提示你完成 Multi-Factor Authentication 质询。IT 部门可以配置质询。
9.	然后，Azure AD 检查将此用户/设备是否需要移动设备管理 (MDM) 注册。 
10.	然后，Windows 将在 Azure AD 上的组织目录中注册该设备，并在 MDM 中进行注册。
11.	完成此操作后，如果你是托管用户，Windows 将完成安装程序，并通过自动登录将用户定向到桌面。
12.	如果你是联合用户，将会进入 Windows 登录屏幕，你必须输入凭据才能登录。

> [AZURE.NOTE]不支持在 Windows 全新体验中加入本地 Active Directory 域。因此，如果你打算将电脑加入域，则应选择链接“改为使用本地帐户设置 Windows”。然后，可以像往常一样从“电脑设置”加入域。

## 其他信息
* [了解 Azure AD Join 的使用方案](active-directory-azureadjoin-deployment-aadjoindirect)
* [设置 Azure AD Join](active-directory-azureadjoin-setup)

<!---HONumber=79-->