<properties 
	pageTitle="Azure Multi-Factor Authentication 服务器入门" 
	description="这是与 Azure Multi-Factor Authentication 相关的页面，介绍如何开始使用 Azure MFA 服务器。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.date="08/24/2015" 
	wacn.date=""/>

# Azure Multi-Factor Authentication 服务器入门




<center>![Cloud](./media/multi-factor-authentication-get-started-server/server2.png)</center>

既然我们已经确定了是否使用本地 Multi-Factor Authentication，那么我们就开始下一步吧。本页介绍如何全新安装服务器，以及在本地 Active Directory 上对它进行设置。如果已安装 Phonefactor 服务器，并正在寻求实现此目标的方法，请参阅“升级到 Azure Mult-Factor Authentication 服务器”；如果你正在寻找有关只安装 Web 服务的信息，请参阅“部署 Azure Multi-Factor Authentication 服务器移动应用 Web 服务”。



## 下载 Azure Multi-Factor Authentication 服务器



可以使用两种不同的方法下载 Azure Multi-Factor Authentication 服务器。两种方法都是通过 Azure 门户进行的。第一种方法是直接管理 Multi-Factor Auth 提供程序。第二种方法是通过服务设置。第二个选项需要 Multi-Factor Auth 提供程序或 Azure AD Premium 许可证。


### 从 Azure 门户下载 Azure Multi-Factor Authentication 服务器
--------------------------------------------------------------------------------

1. 以管理员身份登录到 Azure 门户。
2. 在左侧选择“Active Directory”。
3. 在“Active Directory”页的顶部，单击“Multi-Factor Auth 提供程序”
4. 在底部单击“管理”
5. 此时将打开一个新页面。单击“下载”。![下载](./media/multi-factor-authentication-sdk/download.png)
6. 在“生成激活凭据”上方，单击“下载”。![下载](./media/multi-factor-authentication-get-started-server/download4.png)
7. 保存下载的内容。



### 通过服务设置下载 Azure Multi-Factor Authentication 服务器


1. 以管理员身份登录到 Azure 门户。
2. 在左侧选择“Active Directory”。
3. 双击 Azure AD 的实例。
4. 在顶部单击“配置” ![下载](./media/multi-factor-authentication-sdk/download2.png)
5. 在“Multi-Factor Authentication”下选择“管理服务设置”
6. 在“服务设置”页上的屏幕底部单击“转到门户”。![下载](./media/multi-factor-authentication-sdk/download3.png)
7. 此时将打开一个新页面。单击“下载”。
8. 在“生成激活凭据”上方，单击“下载”。
9. 保存下载的内容。




## 安装和配置 Azure Multi-Factor Authentication 服务器
下载服务器后，你可以进行安装和配置。请确保用于安装的服务器符合以下要求：



Azure Multi-Factor Authentication 服务器要求|说明|
:------------- | :------------- | 
硬件|<li>200 MB 硬盘空间</li><li>x32 或 x64 处理器</li><li>1 GB 或更大的 RAM</li>
软件|<li>Windows Server 2003 或更高版本（如果主机是服务器操作系统）</li><li>Windows Vista 或更高版本（如果主机是客户端操作系统）</li><li>Microsoft.NET 2.0 Framework</li><li>IIS 6.0 或更高版本（如果要安装用户门户或 Web 服务 SDK）</li>

### Azure Multi-Factor Authentication 服务器防火墙要求
--------------------------------------------------------------------------------
每个 MFA 服务器都必须能够通过端口 443 与以下地址进行出站通信：

- https://pfd.phonefactor.net
- https://pfd2.phonefactor.net
- https://css.phonefactor.net

如果端口 443 上限制了出站防火墙，则需要打开以下 IP 地址范围：

IP 子网|网络掩码|IP 范围
:------------- | :------------- | :------------- |
134\.170.116.0/25|255\.255.255.128|134\.170.116.1 – 134.170.116.126
134\.170.165.0/25|255\.255.255.128|134\.170.165.1 – 134.170.165.126
70\.37.154.128/25|255\.255.255.128|70\.37.154.129 – 70.37.154.254

如果你不使用 Azure Multi-Factor Authentication 事件确认功能，并且用户不在公司网络中使用设备上的 Multi-Factor Auth 移动应用进行身份验证，则可以将 IP 范围缩小为：


IP 子网|网络掩码|IP 范围
:------------- | :------------- | :------------- |
134\.170.116.72/29|255\.255.255.248|134\.170.116.72 – 134.170.116.79
134\.170.165.72/29|255\.255.255.248|134\.170.165.72 – 134.170.165.79
70\.37.154.200/29|255\.255.255.248|70\.37.154.201 – 70.37.154.206


### 安装和配置 Azure Multi-Factor Authentication 服务器
--------------------------------------------------------------------------------


1. 双击可执行文件。随即将会开始安装。
2. 在“选择安装文件夹”屏幕中，确保文件夹正确，然后单击“下一步”。
3. 安装完成后，单击“完成”。随即会启动配置向导。
4. 在配置向导欢迎屏幕上，选中“跳过使用身份验证配置向导”，然后单击“下一步”。这将会关闭向导并启动服务器。
![云](./media/multi-factor-authentication-get-started-server/skip2.png)

5. 返回下载服务器的页面，单击“生成激活凭据”按钮。在提供的框中，将此信息复制到“Azure MFA 服务器”，然后单击“激活”。


上述步骤说明了使用配置向导快速进行设置的过程。你可以通过从服务器上的“工具”菜单选择身份验证向导来重新运行该向导。



## 从 Active Directory 导入用户

安装并配置服务器后，你可以快速地将用户导入 Azure MFA 服务器。

### 从 Active Directory 导入用户
--------------------------------------------------------------------------------


1. 在“Azure MFA 服务器”的左侧选择“用户”。
2. 在底部选择“从 Active Directory 导入”。
3. 现在，你可以搜索单个用户，或在 AD 中搜索包含用户的 OU（组织单位）。在本例中，我们将指定用户 OU。
4. 突出显示右侧的所有用户，然后单击“导入”。此时应会显示一个弹出窗口，指出操作已成功。关闭导入窗口。

![云](./media/multi-factor-authentication-get-started-server/import2.png)

## 向用户发送电子邮件
将用户导入到 Azure Multi-Factor Authentication 服务器后，建议你向用户发送一封电子邮件，告知他们已注册到 Multi-Factor Authentication 中。

使用 Azure Multi-Factor Authentication 服务器，可以通过多种途径来配置用户使用 Multi-Factor Authentication 的方式。例如，如果你知道用户的电话号码，或能够从用户公司的目录将电话号码导入到 Azure Multi-Factor Authentication，则该电子邮件将让用户了解已将他们配置为使用 Azure Multi-Factor Authentication，同时提供一些 Azure Multi-Factor Authentication 使用说明，并通知用户他们会在哪个电话号码收到身份验证。

电子邮件的内容根据你为用户设置的身份验证方法（如电话呼叫、短信、移动应用）的不同而异。例如，如果要求用户在身份验证时使用 PIN 码，则该电子邮件将告诉用户其初始 PIN 码的设置。通常要求用户在首次身份验证期间更改其 PIN 码。

如果用户的电话号码尚未配置或尚未导入到 Azure Multi-Factor Authentication 服务器，或者用户已预配置为使用移动应用进行身份验证，则用户将收到一封告知他们已配置为使用 Azure Multi-Factor Authentication 的电子邮件，该邮件将引导他们通过 Azure Multi-Factor Authentication 用户门户完成其帐户注册。邮件中包含一个超链接，用户单击该超链接即可访问用户门户。当用户单击该超链接时，Web 浏览器将会打开，并带用户前往其公司的 Azure Multi-Factor Authentication 用户门户。


### 配置电子邮件和电子邮件模板

单击左侧的电子邮件图标可以设置用于发送这些电子邮件的设置。你可以在此处输入邮件服务器的 SMTP 信息，并通过选中“向用户发送邮件”复选框来给全体用户发送电子邮件。

![电子邮件设置](./media/multi-factor-authentication-get-started-server/email1.png)

在“电子邮件内容”选项卡中，你将会看到所有可供选择的各种电子邮件模板。根据用户的配置方式使用 Multi-Factor Authentication，可以选择最合适的模板。

![电子邮件模板](./media/multi-factor-authentication-get-started-server/email2.png)

## Azure Multi-Factor Authentication 服务器如何处理用户数据

如果你在本地使用 Multi-Factor Authentication (MFA) 服务器，用户的数据将存储在本地服务器中。云中不会持久存储任何用户数据。当用户执行双因素身份验证时，MFA 服务器会将数据发送到 Azure MFA 云服务，以执行身份验证。将这些身份验证请求发送到云服务时，会在请求和日志中发送以下字段，以便在客户的身份验证/使用情况报告中使用。某些字段是可选的，可以在 Multi-Factor Authentication 服务器中启用或禁用。从 MFA 服务器到 MFA 云服务的通信使用基于出站端口 443 的 SSL/TLS。这些字段是：

- 唯一 ID - 用户名或内部 MFA 服务器 ID
- 名字和姓氏 - 可选
- 电子邮件地址 - 可选
- 电话号码 - 用于语音通话或短信身份验证
- 设备令牌 - 用于移动应用身份验证
- 身份验证模式 
- 身份验证结果 
- MFA 服务器名称 
- MFA 服务器 IP 
- 客户端 IP – 如果可用



除了上述字段，身份验证结果（成功/拒绝）和任何拒绝的原因还与身份验证数据一起存储，可通过身份验证/使用情况报告获取。


## Azure Multi-Factor Authentication 服务器高级配置
有关高级设置的详细信息和配置信息，请参考下表。

方法|说明
:------------- | :------------- | 
[用户门户](/documentation/articles/multi-factor-authentication-get-started-portal)| 有关设置和配置用户门户的信息，包括部署和用户自助服务。
[Active Directory 联合身份验证服务](/documentation/articles/multi-factor-authentication-get-started-adfs)|有关设置使用 AD FS 的 Azure Multi-Factor Authentication 的信息。
[RADIUS 身份验证](/documentation/articles/multi-factor-authentication-get-started-server-radius)| 有关设置和配置使用 RADIUS 的 Azure MFA 服务器的信息。
[IIS 身份验证](/documentation/articles/multi-factor-authentication-get-started-server-iis)|有关设置和配置使用 IIS 的 Azure MFA 服务器的信息。
[Windows 身份验证](/documentation/articles/multi-factor-authentication-get-started-server-windows)| 有关设置和配置使用 Windows 身份验证的 Azure MFA 服务器的信息。
[LDAP 身份验证](/documentation/articles/multi-factor-authentication-get-started-server-ldap)|有关设置和配置使用 LDAP 身份验证的 Azure MFA 服务器的信息。
[使用 RADIUS 的远程桌面网关和 Azure Multi-Factor Authentication 服务器](/documentation/articles/multi-factor-authentication-get-started-server-rdg)| 有关设置和配置使用 RADIUS 的、具有远程桌面网关的 Azure MFA 服务器的信息。
[与 Windows Server Active Directory 同步](/documentation/articles/multi-factor-authentication-get-started-server-dirint)|有关在 Active Directory 与 Azure MFA 服务器之间设置和配置同步的信息。
[部署 Azure Multi-Factor Authentication 服务器移动应用 Web 服务](/documentation/articles/multi-factor-authentication-get-started-server-webservice)|有关设置和配置 Azure MFA 服务器 Web 服务的信息。

<!---HONumber=Mooncake_1207_2015-->