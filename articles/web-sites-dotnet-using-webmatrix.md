<properties linkid="develop-dotnet- Website-with-webmatrix" urlDisplayName=" 包含 WebMatrix 的网站" pageTitle="包含 WebMatrix 的 .NET 网站 - Azure 教程" metaKeywords="WebMatrix Azure, WebMatrix Azure, Azure  Website WebMatrix, Azure  Website WebMatrix, Web Matrix Azure, WebMatrix Azure" description="了解如何开发和部署包含 WebMatrix 的 Azure 网站。" metaCanonical="" services="web-sites" documentationCenter=".NET" title="Develop and deploy a  Website with Microsoft WebMatrix" authors="" solutions="" manager="" editor="" />






# 使用 Microsoft WebMatrix 开发和部署网站
本指南介绍如何使用 Microsoft WebMatrix 创建网站并将其部署到 Azure。你将使用 WebMatrix 网站模板中的一个示例应用程序。

你将了解到以下内容：

* 如何在 WebMatrix 中登录 Azure
* 如何将内置模板和 WebMatrix 配合使用来创建网站 
* 如何将自定义的网站直接从 WebMatrix 中部署到 Azure。


## 登录 Azure

1. 启动 WebMatrix。
2. 如果这是你首次使用 WebMatrix 3，系统将会提示你登录 Azure。否则，你可以单击"登录"按钮，然后选择"添加帐户"。选择使用你的 Microsoft 帐户"登录"。

	![Add Account][addaccount]

3. 如果你已注册某一 Azure 帐户，则可以使用你的 Microsoft 帐户登录：

	![Sign into Azure][signin]	


## 使用 Azure 的内置模板创建网站

1. 在开始屏幕上，单击"新建"按钮，然后选择"模板库"以便从模板库创建一个新网站：

	![New site from Template Gallery][sitefromtemplate]

2. 模板库将显示可本地运行或在 Azure 上运行的可用模板的列表。从模板列表中选择"面包店"，输入"bakerysample"作为"网站名称"，然后单击"下一步"。

	![Create Site from Template][sitefromtemplatedetails]

3. 如果你已登录到 Azure 中，现在将具有该选项以便为你的本地网站创建一个 Azure 网站。选择一个独有名称，然后选择你希望要创建你的网站的数据中心： 

	![Create site on Azure][sitefromtemplateazure]

	在 WebMatrix 生成网站完毕后，将显示 WebMatrix IDE：

	![WebMatrix IDE][howtowebmatrixide] 

## 设置电子邮件

面包店示例中有一个模拟的订单窗体，该窗体发送一封电子邮件及所订购的商品。你可以在 Azure 上使用 SendGrid 电子邮件服务来从站点发送电子邮件。

1. 请按照[如何通过 Azure 使用 SendGrid 发送电子邮件][sendgridexample]教程中的步骤设置 SendGrid 帐户，并检索连接信息。你不需要完成整个教程，只要完成获取连接信息的部分即可。

2. 将 SendGrid NuGet 包添加到 WebMatrix 项目首先，单击"NuGet"按钮。

    ![Add SendGrid][addsendgrid]

    搜索 SendGrid 并安装它。

    ![Install SendGrid][installsendgrid]

    包安装完成后，请注意 SendGrid 程序集已经添加到 bin。

    ![SendGrid added][binsendgrid]

3. 通过双击文件名打开 *Order.cshtml* 页。

	![][modify2]

4. 在该文件的顶部，添加以下代码：

        @using SendGrid;
        @using System.Net.Mail;

4. 找到 //SMTP Configuration for Hotmail 注释，然后删除或注释掉有关使用 WebMail 的所有代码。

        /*
        //SMTP Configuration for Hotmail
        WebMail.SmtpServer = "smtp.live.com";
        WebMail.SmtpPort = 25;
        WebMail.EnableSsl = true;

        //Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

        if (WebMail.UserName.IsEmpty() || WebMail.Password.IsEmpty() || WebMail.From.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        } 
        else {
            try {
                WebMail.Send(to: customerEmail, subject: "Fourth Coffee - New Order", body: body);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }*/


5. 添加代码以使用 SendGrid 而不是 WebMail 发送电子邮件。添加以下代码，以取代你在上一步骤删除的代码。

		 if (email.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        }
        else {
            // Create the email object first, then add the properties.
            SendGridMessage myMessage = new SendGridMessage();
            myMessage.AddTo(email);
            myMessage.From = new MailAddress("FourthCoffee@example.com", "Fourth Coffee");
            myMessage.Subject = "Fourth Coffee - New Order";
            myMessage.Text = body;

            // Create credentials, specifying your user name and password.
            var credentials = new NetworkCredential("[your user name", "[your password]");

            // Create an Web transport for sending email.
            var transportWeb = new Web(credentials);

            // Send the email.
            try {
                transportWeb.Deliver(myMessage);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }


6. 在 WebMatrix 功能区上，单击"运行"以测试网站。

	![][modify4]

7. 在某个产品上单击"立即订购"，然后将订单发给你自己。

8. 检查你的电子邮件，确保获得了订单确认。如果发送电子邮件遇到困难，请参阅《ASP.NET 网页 (Razor) 故障排除指南》中的[有关发送电子邮件的问题][sendmailissues]。
 

## 将自定义的网站从 WebMatrix 部署到 Azure

1. 在 WebMatrix 中，从"主页"功能区单击"发布"，为网站显示"发布预览"对话框。

	![WebMatrix Publish Preview][howtopublishpreview]

2. 单击以选中 bakery.sdf 旁的复选框，然后单击"继续"。  发布完毕后，将在 WebMatrix IDE 底部显示 Azure 上更新网站的 URL。  

	![Publishing Complete][publishcomplete]

3. 单击该链接以在浏览器中打开该网站：

	![Bakery Sample Site][bakerysample]

	还可通过单击"网站"以显示订阅的所有网站，在 Azure 门户中找到网站的 URL。每个网站的 URL 显示在"网站"页上的"URL"列中。

## 修改该网站并将其重新发布到 Azure 网站

可使用 WebMatrix 修改该网站并将其重新发布到 Azure 网站。在以下过程中，你将添加一个复选框以指示订单是赠品。

1. 打开 *Order.cshtml* 页。

2. 找到"shiping"类窗体定义。紧接 &lt;li&gt; 块后插入以下代码。
		
		<li class="gift">
		    <div class="fieldcontainer" data-role="fieldcontain">
		        <label for="isGift">This is a gift</label>           
		        <div>@Html.CheckBox("isGift")</div>
		    </div>
		</li>

	![][modify5]

3. 在文件中找到 "var shipping = Request["orderShipping"];" 行，然后紧接其后插入下面这行代码。

		var gift = Request["isGift"];

4. 在以下代码段中紧接确认配送地址不为空的代码块后。

		if(gift != null) {
			body += "This is a gift." + "<br/>";
		}

	你的 *order.cshtml* 文件应与下图类似。

	![][modify6]

5. 保存该文件并在本地运行网站，然后向你自己发送一个测试订单。确保测试这个新复选框。

	![][modify7]

6. 通过在"主页"功能区上单击"发布"，重新发布该网站。

7. 在"发布预览"对话框中，确保选中 Order.cshtml 并单击"继续"。

8. 单击该链接以在浏览器中打开网站并测试 Azure 网站上的更新。

# 后续步骤

你已了解如何创建网站并将网站从 WebMatrix 部署到 Azure。若要了解有关 WebMatrix 的详细信息，请参阅下列资源：

* [WebMatrix for Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [WebMatrix 网站](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[howtowebmatrixide]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide2.png
[howtopublishpreview]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png
[addsendgrid]: ./media/web-sites-dotnet-using-webmatrix/addsendgridpackage.png
[installsendgrid]: ./media/web-sites-dotnet-using-webmatrix/installsendgrid.png
[binsendgrid]: ./media/web-sites-dotnet-using-webmatrix/sendgridbin.png

[publishcomplete]: ./media/web-sites-dotnet-using-webmatrix/howtopublished2.png
[bakerysample]: ./media/web-sites-dotnet-using-webmatrix/howtobakerysamplesite.png
[addaccount]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template.png
[sitefromtemplatedetails]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-details.png
[sitefromtemplateazure]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-azure.png

[modify1]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-1.png
[modify2]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-2.png
[modify3]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-3.png
[modify4]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-4.png
[modify5]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-5.png
[modify6]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-6.png
[modify7]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-7.png



[sendmailissues]: http://go.microsoft.com/fwlink/?LinkId=253001#email
[sendgridexample]: /zh-cn/documentation/articles/sendgrid-dotnet-how-to-send-email/
