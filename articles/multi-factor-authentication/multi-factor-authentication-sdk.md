<properties 
	pageTitle="将本地标识与 Azure Active Directory 集成" 
	description="本页介绍 Azure AD Connect 是什么，以及为何要使用它。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.date="07/21/2015" 
	wacn.date=""/>

# 将多重身份验证构建到自定义应用程序中 (SDK)

使用 Azure Multi-Factor Authentication 软件开发工具包 (SDK)，你可将电话呼叫和短信验证直接内置于 Azure AD 租户的应用程序的登录或事务处理流程中。

Multi-Factor Authentication SDK 可用于 C#、Visual Basic (.NET)、Java、Perl、PHP 和 Ruby。该 SDK 围绕 Multi-Factor Authentication 提供一个精简包装。它提供你编写代码所需的全部内容，包括带注释的源代码文件、示例文件和详细自述文件。每个 SDK 还包括证书和私钥，用于加密你的 Multi-Factor Authentication 提供程序独有的事务处理。只要你有提供程序，即可根据需要下载任意多种语言和格式的 SDK。

Multi-Factor Authentication SDK 的 API 结构相当简单。你可使用 Multi-Factor Authentication 选项参数（如验证模式）和用户数据（如要呼叫的电话号码或要验证的 PIN 号码）对 API 进行单函数调用。API 将函数调用转换成对基于云的 Azure Multi-Factor Authentication 服务的 Web 服务请求。所有调用都必须包括对每个 SDK 中包含的私有证书的引用。

由于这些 API 无法访问 Azure Active Directory 中注册的用户，你必须在文件或数据库中提供用户信息，例如电话号码和 PIN 码。另外，这些 API 也未提供注册或用户管理功能，因此你需要将这些流程内置于你的应用程序中。






## 下载 Azure Multi-Factor Authentication SDK 

可以使用两种不同的方法下载 Azure Multi-Factor Authentication SDK。两种方法都是通过 Azure 门户进行的。第一种方法是直接管理 Multi-Factor Auth 提供程序。第二种方法是通过服务设置。第二个选项需要 Multi-Factor Auth 提供程序或 Azure AD Premium 许可证。


### 从 Azure 门户下载 Azure Multi-Factor Authentication SDK


1. 以管理员身份登录到 Azure 门户。
2. 在左侧选择“Active Directory”。
3. 在“Active Directory”页的顶部，单击“Multi-Factor Auth 提供程序”
4. 在底部单击“管理”
5. 此时将打开一个新页面。在左下方单击“SDK”。
<center>![Download](./media/multi-factor-authentication-sdk/download.png)</center>
6. 选择所需的语言，然后单击一个关联的下载链接。
7. 保存下载的内容。



### 通过服务设置下载 Azure Multi-Factor Authentication SDK


1. 以管理员身份登录到 Azure 门户。
2. 在左侧选择“Active Directory”。
3. 双击 Azure AD 的实例。
4. 在顶部单击“配置”
5. 在“Multi-Factor Authentication”下选择“管理服务设置” ![下载](./media/multi-factor-authentication-sdk/download2.png)
6. 在“服务设置”页上的屏幕底部单击“转到门户”。![下载](./media/multi-factor-authentication-sdk/download3.png)
7. 此时将打开一个新页面。在左下方单击“SDK”。
8. 选择所需的语言，然后单击一个关联的下载链接。
9. 保存下载的内容。

## Azure Multi-Factor Authentication SDK 的内容
在 SDK 中，你将会看到以下项：

- **自述文件**。解释如何在新应用程序或现有应用程序中使用 Multi-Factor Authentication API。
- Multi-Factor Authentication 的**源文件**
- 用来与 Multi-Factor Authentication 服务通信的**客户端证书**
- 证书的**私钥**
- **调用结果**。 调用结果代码列表。若要打开此文件，请使用支持文本格式的应用程序，例如 WordPad。使用调用结果代码可测试 Multi-Factor Authentication 在应用程序中的实现及进行故障排除。它们不是身份验证状态代码。
- **示例**。 Multi-Factor Authentication 的基本工作实现的示例代码。


>[AZURE.WARNING]客户端证书是专门为你生成的唯一私有证书。请不要共享或丢失此文件。它是确保你与 Multi-Factor Authentication 服务的通信安全性的关键所在。

## 代码示例：标准模式电话验证

此代码示例演示如何使用 Azure Multi-Factor Authentication SDK 中的 API，为你的应用程序添加标准模式语音呼叫验证。标准模式是用户通过按 # 键响应的电话呼叫。

本例在包含 C# 服务器端逻辑的基本 ASP.NET 应用程序中使用了 C# .NET 2.0 Multi-Factor Authentication SDK，但此过程与使用其他语言的简单实现非常相似。由于该 SDK 包括源文件而非可执行文件，因此你可以生成并引用文件，或将文件直接包括在你的应用程序中。

>[AZURE.NOTE]实现 Multi-Factor Authentication 时，将使用附加因素作为第二因素或第三因素验证来补充主要身份验证方法。根据设计，这些方法不用作主要身份验证方法。

### 代码示例概述
这段示例代码用于非常简单的 Web 演示应用程序，它使用以 # 键响应的电话呼叫来完成用户的身份验证。此电话呼叫因素在 Multi-Factor Authentication 中称为标准模式。

客户端代码不包括任何专用于 Multi-Factor Authentication 的元素。由于其他身份验证因素是独立于主身份验证的，因此你可在不更改现有登录接口的情况下添加这些因素。通过 Multi-Factor Authentication SDK 中的 API，你可以自定义用户体验，但也可能根本不需要进行任何更改。

服务器端代码在步骤 2 中添加了标准模式身份验证。它创建一个 PfAuthParams 对象，带有标准模式验证必需的参数：用户名、电话号码、模式及客户端证书路径 (CertFilePath)，这是每个调用所必需的。有关 PfAuthParams 中的所有参数的演示，请参阅 SDK 中的示例文件。

接下来，代码将 PfAuthParams 对象传递给 pf_authenticate() 函数。返回值指示身份验证成功或失败。out 参数 callStatus 和 errorID 包含其他调用结果信息。调用结果代码记录在 SDK 中的调用结果文件中。

最小实现只需编写几行代码。但在生产代码中，你会包括更加复杂的错误处理、其他数据库代码和增强型用户体验。

### Web 客户端代码

以下是演示页的 Web 客户端代码。

	
	<%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="_Default" %>
	
	<!DOCTYPE html>
	
	<html xmlns="http://www.w3.org/1999/xhtml">
	<head runat="server">
	<title>Multi-Factor Authentication Demo</title>
	</head>
	<body>
	<h1>Azure Multi-Factor Authentication Demo</h1>
	<form id="form1" runat="server">
	
	<div style="width:auto; float:left">
	Username:&nbsp;<br />
	Password:&nbsp;<br />
	</div>
	
	<div">
	<asp:TextBox id="username" runat="server" width="100px"/><br />
	<asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
	</div>
	
	<asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>
	
	<p><asp:Label ID="lblResult" runat="server"></asp:Label></p>
	
	</form>
	</body>
	</html>


### 服务器端代码

在以下服务器端代码中，Multi-Factor Authentication 在步骤 2 中配置和运行。标准模式 (MODE_STANDARD) 是用户通过按 # 键响应的电话呼叫。

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.UI;
	using System.Web.UI.WebControls;
	
	public partial class _Default : System.Web.UI.Page
	{
	    protected void Page_Load(object sender, EventArgs e)
	    {
	    }
	
	    protected void btnSubmit_Click(object sender, EventArgs e)
	    {
	        // Step 1: Validate the username and password
	        if (username.Text != "Contoso" || password.Text != "password")
	        {
	            lblResult.ForeColor = System.Drawing.Color.Red;
	            lblResult.Text = "Username or password incorrect.";
	        }
	        else
	        {
	            // Step 2: Perform multi-factor authentication
	
	            // Add call details from the user database.
	            PfAuthParams pfAuthParams = new PfAuthParams();
	            pfAuthParams.Username = username.Text;
	            pfAuthParams.Phone = "9134884271";
	            pfAuthParams.Mode = pf_auth.MODE_STANDARD;
	            
	            // Specify a client certificate 
	            // NOTE: This file contains the private key for the client
	            // certificate. It must be stored with appropriate file 
	            // permissions.
	            pfAuthParams.CertFilePath = "c:\cert_key.p12";
	
	            // Perform phone-based authentication
	            int callStatus;
	            int errorId;
	
	            if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
	            {
	                lblResult.ForeColor = System.Drawing.Color.Green;
	                lblResult.Text = "Multi-Factor Authentication succeeded.";
	            }
	            else
	            {
	                lblResult.ForeColor = System.Drawing.Color.Red;
	                lblResult.Text = " Multi-Factor Authentication failed.";
	            }
	        }
	
	    }
	}

<!---HONumber=69-->