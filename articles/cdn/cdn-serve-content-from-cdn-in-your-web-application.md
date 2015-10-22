<properties 
	pageTitle="在 Web 应用程序中从 Azure CDN 提供内容" 
	description="本教程演示如何使用 CDN 中的内容来提高 Web 应用程序的性能。" 
	services="cdn" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="cdn" 
	ms.date="09/01/2015" 
	wacn.date=""/>

# 在 Web 应用程序中从 Azure CDN 提供内容 #

本教程说明如何利用 Azure CDN 的优势来改进 Web 应用程序的覆盖面和性能。以下情况 Azure CDN 可以帮助提高 Web 应用程序的性能：

- 在页面上有许多指向静态或半静态内容的链接
- 全球范围内的客户端都会访问你的应用程序
- 你想要卸载来自 Web 服务器的通信
- 你想要减少到 Web 服务器的并发客户端连接数（[绑定和缩减](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)上对此有详尽的讨论） 
- 你想要提高所感知到的页面加载/刷新次数

## 你要学习的知识 ##

在本教程中，你将了解如何执行以下操作：

-	[通过 Azure CDN 终结点提供静态内容](#deploy)
-	[自动将内容从 ASP.NET 应用程序加载到 CDN 终结点](#upload)
-	[将 CDN 缓存配置为能够反映所需的内容更新](#update)
-	[使用查询字符串即时提供全新的内容](#query)

## 所需的项目 ##

本教程设置了以下前提条件：

-	有效的 [Microsoft Azure 帐户](/account/)。你可以注册一个试用帐户
-	Visual Studio 2013 with [Azure SDK](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)，适用于 blob 管理 GUI
-	[Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409)（用于[自动将内容从 ASP.NET 应用程序加载到 CDN 终结点](#upload)）

> [AZURE.NOTE]完成本教程需要有一个 Azure 帐户：
> + 你可以[建立一个 Azure 帐户](/pricing/1rmb-trial/?WT.mc_id=A261C142F) - 获取可用来试用付费版 Azure 服务的信用额度，甚至在用完信用额度后，你仍可以保留帐户和使用免费的 Azure 服务（如网站）。
> + 你可以[激活 MSDN 订户权益](/pricing/member-offers/msdn-benefits-details/) - 订阅 MSDN 后，你可以每月获得相应的信用额度，这些信用额度可以用于付费的 Azure 服务。

<a name="static"></a>
## 通过 Azure CDN 终结点提供静态内容 ##

在此教程部分，你将了解如何创建 CDN 并将其用于提供静态内容。所涉及的主要步骤如下：

1. 创建存储帐户
2. 创建链接到存储帐户的 CDN
3. 在存储帐户中创建 blob 容器
4. 将内容上载到 blob 容器
5. 链接到使用 CDN URL 上载的内容

让我们开始吧。开始使用 Azure CDN 时，请遵循以下步骤：

1. 若要创建 CDN 终结点，请登录到 [Azure 管理门户](http://manage.windowsazure.cn/)。 
2. 单击“新建 > 数据服务 > 存储 > 快速创建”以创建存储帐户。指定一个 URL，一个位置，然后单击“创建存储帐户”。 

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-1.PNG)

	>[AZURE.NOTE]请注意，我将使用东亚作为区域，因为东亚足够远，我可以在以后从北美测试我的 CDN。

3. 一旦新的存储帐户的状态为“联机”，即可创建新的 CDN 终结点，将其绑定到你所创建的存储帐户。单击“新建 > 应用服务 > CDN > 快速创建”。选择你创建的存储帐户，然后单击“创建”。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2.PNG)

	>[AZURE.NOTE]创建 CDN 以后，Azure 门户就会向你显示其 URL 以及所绑定的源域。不过，需要一定的时间才能将 CDN 终结点的配置完全传播到所有节点位置。

4. 通过 ping 测试你的 CDN 终结点，以确保其处于联机状态。如果你的 CDN 终结点尚未传播到所有节点，你将看到如下所示的消息。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-fail.PNG)

	再等待一个小时，然后再次测试。CDN 终结点在传播到所有节点之后，就会响应你的 ping。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-succeed.PNG)

5. 此时，你可能已看到 CDN 终结点将何处确定为最接近你的 CDN 节点。在我的台式机中，响应 IP 地址为 **93.184.215.201**。将其插入 [www.ip-address.org](http://www.ip-address.org) 之类的站点，可以看到服务器位于华盛顿特区。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-4.PNG)

	如需所有 CDN 节点位置的列表，请参阅 [Azure 内容交付网络 (CDN) 节点位置](http://msdn.microsoft.com/zh-cn/library/azure/gg680302.aspx)。

6. 回到 Azure 门户，在“CDN”选项卡中，单击刚创建的 CDN 终结点的名称。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequerya.PNG)

7. 单击“启用查询字符串”在 Azure CDN 缓存中启用查询字符串。启用此功能后，通过不同的查询字符串进行访问的同一链接就可以缓存为不同的项。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequeryb.PNG)

	>[AZURE.NOTE]虽然启用查询字符串对于教程的此部分来说不是必需的，但为方便起见，最好是尽早启用此功能，因为在这里所做的任何更改都需要一定的时间才能传播到其余节点，而你并不想让任何非查询字符串支持的内容充斥 CDN 缓存（稍后将讨论如何更新 CDN 内容）。在[通过查询字符串即时提供全新的内容](#query)中，你将了解到如何充分利用此功能。

8. 在 Visual Studio 2013 的服务器资源管理器中单击“连接到 Microsoft Azure”按钮。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-5.PNG)

9.  按照提示操作以登录到你的 Azure 帐户。
10.  一旦登录，则请展开“Microsoft Azure > 存储 > 你的存储帐户”。右键单击“Blob”并选择“创建 Blob 容器”。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-6.PNG)

11.	指定 blob 容器名称，然后单击“确定”。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-7.PNG)

12.	在服务器资源管理器中双击所创建的 blob 容器，以便对其进行管理。你会看到中心窗格中的管理界面。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-8.PNG)

13.	单击“上载 Blob”按钮将网页所用的图像、脚本或样式表上载到 blob 容器。上载进度将显示在“Azure 活动日志”中，这些 blob 在上载后会显示在容器视图中。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-9.PNG)

14.	上载完 blob 后，必须将其设置为公共才能对其进行访问。在“服务器资源管理器”中，右键单击容器，然后选择“属性”。将“公共读取访问权限”属性设置为“Blob”。

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-10.PNG)

15.	在浏览器中导航到其中一个 blob 的 URL，对 blob 的公共访问权限进行测试。例如，我可以通过 `http://cephalinstorage.blob.core.windows.net/cdn/cephas_lin.png` 测试已上载项列表中的第一个图像。

	请注意，我用的不是在 Visual Studio 的 blob 管理界面中给出的 HTTPS 地址。通过 HTTP，你可以测试内容是否可以公共访问，这是对 Azure CDN 的要求。

16.	如果你可以看到 blob 在浏览器中正确呈现，则将 URL 从 `http://<yourStorageAccountName>.blob.core.windows.net` 更改为 Azure CDN 的 URL。在我的示例中，若要测试 CDN 终结点的第一个图像，需要使用 `http://az623979.vo.msecnd.net/cdn/cephas_lin.png`。

	>[AZURE.NOTE]你可以在 Azure 管理门户的“CDN”选项卡中找到 CDN 终结点的 URL。

	如果比较直接 blob 访问和 CDN 访问的性能，你就会看到使用 Azure CDN 的好处。下面是通过 blob URL 访问我的图像时，Internet Explorer 11 F12 开发人员工具获得的屏幕快照：

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-blob.PNG)

	这是通过 CDN URL 访问相同图像获得的结果

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-cdn.PNG)

 	请注意“请求”计时数字，该计时是针对第一个字节的时间，或者说是发送请求并从服务器获得第一个响应的时间。当我访问该 blob（托管在东亚区域）时，共花了我 266 毫秒的时间，这是因为该请求必须横跨整个太平洋才能到达服务器。但是，当我访问 Azure CDN 时，只花了 16 毫秒，这差不多是 **20 倍的性能收益**！
	
17.	现在，你只需在网页中使用新链接就可以了。例如，我可以添加以下图像标记：

		<img alt="Mugshot" src="http://az623979.vo.msecnd.net/cdn/cephas_lin.png" />

在本部分，你学习了如何创建 CDN 终结点、如何将内容上载到该终结点，以及如何链接到任意网页中的 CDN 内容。

<a name="upload"></a>
## 自动将内容从 ASP.NET 应用程序加载到 CDN 终结点 ##

如果你想要轻松地将 ASP.NET Web 应用程序中的所有静态内容上载到 CDN 终结点，或者想要通过连续交付部署 Web 应用程序（如需示例，请参阅[在 Azure 中持续交付云服务](/documentation/articles/cloud-services/cloud-services-dotnet-continuous-delivery)），则可在每次部署 Web 应用程序时，使用 Azure PowerShell 自动将最新的内容文件同步到 Azure blob。例如，你可以运行[将 ASP.NET 应用程序中的内容文件上载到 Azure Blob](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a) 中的脚本，以便上载 ASP.NET 应用程序中的所有内容文件。若要使用此脚本，请执行以下操作：

1. 从“开始”菜单运行“Microsoft Azure PowerShell”。
2. 在 Azure PowerShell 窗口中，运行 `Get-AzurePublishSettingsFile` 以下载 Azure 帐户的发布设置文件。
3. 下载发布设置文件以后，运行以下命令： 

		Import-AzurePublishSettingsFile "<yourDownloadedFilePath>"

	>[AZURE.NOTE]一旦导入你的发布设置文件，它将成为默认 Azure 帐户，用于所有 Azure PowerShell 会话。这意味着上述步骤只需执行一次。
	
5. 从[下载页](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a)下载该脚本。将其保存到 ASP.NET 应用程序的项目文件夹。
6. 右键单击下载的脚本，然后单击“属性”。
7. 单击“取消阻止”。
8. 打开 PowerShell 窗口并运行以下命令：

		cd <ProjectFolder>
		.\UploadContentToAzureBlobs.ps1 -StorageAccount "<yourStorageAccountName>" -StorageContainer "<yourContainerName>"

此脚本中会将 *\\Content* 和 *\\Scripts* 文件夹中的所有文件上载到指定的存储帐户和容器。它具有以下优点：

-	自动复制 Visual Studio 项目的文件结构
-	根据需要自动创建 blob 容器
-	针对多个 Web 应用程序（每一个都位于不同的 blob 容器中）重复使用同一个 Azure 存储帐户和 CDN 终结点。
-	轻松地使用新内容更新 Azure CDN。有关内容更新的详细信息，请参阅[将 CDN 缓存配置为能够反映所需的内容更新](#update)。

对于 `-StorageContainer` 参数，你可以使用 Web 应用程序的名称或 Visual Studio 项目名称。虽然我此前使用了一般化的“cdn”作为容器名称，但你最好是使用你的 Web 应用程序的名称，这样可以将相关内容组织到同一个可以轻松标识的容器中。

内容上载完以后，你就可以使用 `http://<yourCDNName>.vo.msecnd.net/<containerName>` 通过 HTML 代码（例如 .cshtml 文件）链接到 *\\Content* 和 *\\Scripts* 文件夹中的任何内容。下面是可以在 Razor 视图中使用的内容的示例：

	<img alt="Mugshot" src="http://az623979.vo.msecnd.net/MyMvcApp/Content/cephas_lin.png" />

如需将 PowerShell 脚本集成到持续交付配置中的示例，请参阅[在 Azure 中持续交付云服务](/documentation/articles/cloud-services-dotnet-continuous-delivery)。

<a name="update"></a>
## 将 CDN 缓存配置为能够反映所需的内容更新 ##

现在，假设你在将 Web 应用中的静态文件上载到 blob 容器以后，又对项目中的某个文件进行了更改，并将其再次上载到 blob 容器。你认为它会自动更新到 CDN 终结点中，但事实上，当你访问该内容的 CDN URL 时，你看不到相应的更新，此时你可能会感到很迷惑。

实际上，CDN 确实会自动通过 blob 存储进行更新，但在更新时，对内容应用默认的缓存规则，即缓存 7 天。这意味着，当某个 CDN 节点从 blob 存储拉取内容以后，同一内容要等到在缓存中过期后才会进行更新。

不过，好在你可以自定义缓存过期时间。与大多数浏览器一样，Azure CDN 会遵守在内容的 Cache-Control 标头中指定的过期时间。你可以导航到 Azure 门户中的 blob 容器并对 blob 属性进行编辑，从而指定自定义 Cache-Control 标头值。下面的屏幕快照显示缓存过期时间设置为 1 小时（3600 秒）。

![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-updates-1.PNG)

也可以在 PowerShell 脚本中执行此操作，对所有 blob 的 Cache-Control 标头进行设置。对于[自动将内容从 ASP.NET 应用程序加载到 CDN 终结点](#upload)中的脚本，请查找以下代码段：

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType} `
        -Force

然后对其进行修改，如下所示：

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType; CacheControl="public, max-age=3600"} `
        -Force

你可能仍然需要等待整整 7 天，等待 Azure CDN 上的缓存内容过期，然后 CDN 终结点才会拉取新内容，其中包含新的 Cache-Control 标头。这表明了一个事实，即，如果你需要对内容进行即时更新（例如进行 JavaScript 或 CSS 更新），自定义缓存值并没有用。不过，你可以使用查询字符串通过文件重命名或版本控制来解决此问题。有关详细信息，请参阅[使用查询字符串即时提供全新的内容](#query)。

当然，需要设置缓存的时间和位置。例如，你的内容可能不需要频繁更新（比如即将举行的世界杯比赛可以每 3 小时刷新一次），但你的全局流量却很大，因此你需要将这些流量从自己的 Web 服务器中去掉以减轻负荷。这种情况下就可以使用 Azure CDN 缓存。

<a name="query"></a>
## 使用查询字符串即时提供全新的内容 ##

在 Azure CDN 中，你可以启用查询字符串，以便分开缓存带有特定查询字符串的 URL 中的内容。如果你希望将某些内容更新即时推送到客户端浏览器中，则这是一项很有用的功能，因为这样一来就不必等待缓存的 CDN 内容过期。假设我发布网页时在 URL 中设置了版本号。
  
	<link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.0.0" rel="stylesheet"/>

当我发布 CSS 更新并在 CSS URL 中使用其他版本号时：

	<link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.1.1" rel="stylesheet"/>

对于启用了查询字符串的 CDN 终结点来说，这两个 URL 相对于彼此都属于唯一 URL，因此该终结点会向我的 Web 服务器发出新的请求，以便检索新的 *bootstrap.css*。但对于没有启用查询字符串的 CDN 终结点来说，这两个 URL 属于同一 URL，因此会直接提供缓存的 *bootstrap.css*。

这里涉及到的诀窍是，需要自动更新版本号。在 Visual Studio 中，这很容易操作。在 .cshtml 文件中，我会使用上面的链接，因此可以根据部件号指定版本号。

	@{
	    var cdnVersion = System.Reflection.Assembly.GetAssembly(
	        typeof(MyMvcApp.Controllers.HomeController))
	        .GetName().Version.ToString();
	}
	
	...
	
	<link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=@cdnVersion" rel="stylesheet"/>

如果你每次发布都更改部件号，则可确保每次发布 Web 应用时都获得唯一版本号，该版本号在进行下次发布之前会保持不变。或者，你也可以让 Visual Studio 在你每次生成 Web 应用时自动递增部件版本号，只需在 Visual Studio 项目中打开 *Properties\\AssemblyInfo.cs*，然后在 `AssemblyVersion` 中使用 `*` 即可。例如：

	[assembly: AssemblyVersion("1.0.0.*")]

## 在 ASP.NET 中绑定脚本和样式表怎么样？ ##

使用 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 和 [Azure 云服务](/services/cloud-services/)，你可以获得最佳的 Azure CDN 集成，即集成 [ASP.NET 绑定和缩减](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)。

将 Azure App Service 或 Azure 云服务与 Azure CDN 集成具有以下优势：

- 在 Azure Web 应用的[持续部署](/documentation/articles/web-sites-publish-source-control)过程中集成内容部署（图像、脚本和样式表）
- 轻松升级 CDN 提供的 NuGet 包，例如 jQuery 或 Bootstrap 版本 
- 在同一 Visual Studio 界面中管理 Web 应用程序和 CDN 提供的内容

相关教程请参阅：
- [在 Azure App Service 中使用 Azure CDN](/documentation/articles/cdn-websites-with-cdn) 
- [将云服务与 Azure CDN 集成](/documentation/articles/cdn-cloud-service-with-cdn)

如果不集成 Azure App Service Web Apps 或 Azure 云服务，则也可将 Azure CDN 用于脚本捆绑包，但会出现以下说明：

- 必须将捆绑的脚本手动上载到 blob 存储。[stackoverflow](http://stackoverflow.com/a/13736433) 中提供了编程解决方案。
- 请在你的 .cshtml 文件中对呈现的脚本/CSS 标记进行转换，以便使用 Azure CDN。例如：

		@Html.Raw(Styles.Render("~/Content/css").ToString().Insert(0, "http://<yourCDNName>.vo.msecnd.net"))

## 更多信息 ##
- [Azure 内容交付网络 (CDN) 概述](/documentation/articles/cdn-overview)
- [在 Azure App Service 中使用 Azure CDN](/documentation/articles/cdn-websites-with-cdn)
- [将云服务与 Azure CDN 集成](/documentation/articles/cdn-cloud-service-with-cdn)
- [如何将内容交付网络 (CDN) 内容映射到自定义域](http://msdn.microsoft.com/zh-cn/library/azure/gg680307.aspx)
- [使用 Azure CDN](/documentation/articles/cdn-how-to-use-cdn)
 

<!---HONumber=74-->