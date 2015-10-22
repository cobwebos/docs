<properties 
	 pageTitle="如何将内容交付网络 (CDN) 内容映射到自定义域" 
	 description="本主题演示如何将 CDN 内容映射到自定义域。" 
	 services="cdn" 
	 documentationCenter="" 
	 authors="zhangmanling" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.date="09/01/2015" 
	 wacn.date=""/>

#如何将自定义域映射到内容交付网络 (CDN) 终结点

你可以将自定义域映射到 CDN 终结点，以便在指向已缓存内容的 URL 中使用你自己的域名，而不必使用提供给你的 CDN 终结点。

可以使用两种方法将自定义域映射到 CDN 终结点。

1. **在域注册机构中创建 CNAME 记录，将你的自定义域和子域映射到 CDN 终结点** 
	
	CNAME 记录是一种 DNS 功能，用于将源域映射到目标域。在此情况下，源域是你的自定义域和子域（始终需要子域）。目标域是你的 CDN 终结点。

	但是，将你的自定义域映射到 CDN 终结点的过程会导致域在你在 Azure 管理门户中注册域时出现短暂的停机时间。 
2. **通过 Azure cdnverify 添加中间注册步骤**

	如果你的自定义域目前所支持的应用程序的服务级别协议 (SLA) 要求不能有停机时间，则可以使用 Azure **cdnverify** 子域提供中间注册步骤，以便用户在 DNS 映射进行时能够访问你的域。

> AZURE.NOTE
> 
-	你必须在域注册机构中创建 CNAME 记录，将你的域映射到 CDN 终结点CNAME 记录会映射特定的子域，例如 www.mydomain.com 或 myblog.mydomain.com。无法将 CNAME 记录映射到根域，例如 mydomain.com。
-	只能将一个子域与一个 CDN 终结点相关联。你创建的 CNAME 记录会将其目标为子域的所有流量路由到指定的终结点。例如如果将 www.mydomain.com 与 CDN 终结点关联在一起，则不能将它与其他 Azure 终结点（例如存储帐户终结点或云服务终结点）进行关联。但是，你可以将同一域中的不同子域用于不同的服务终结点。你还可以将不同子域映射到同一 CDN 终结点。

本主题中的过程将为你演示如何：

-	[为 Azure CDN 终结点注册自定义域](#subheading1)
-	[使用中间 cdnverify 子域为 Azure CDN 终结点注册自定义域](#subheading2)
-	[验证该自定义子域引用你的 CDN 终结点](#subheading3) 

##<a name="subheading1"></a>为 Azure CDN 终结点注册自定义域

1.	登录到 [Azure 管理门户](http://manage.windowsazure.cn)。
2.	在导航窗格中，单击“CDN”。
3.	在列表视图中单击 CDN 终结点的名称（你需要将子域与其相关联），以便导航到该终结点的详细信息页。
4.	在功能区中，单击“管理域”以显示“管理自定义域”对话框。在对话框的文本中，你会看到从 CDN 终结点派生的主机名，该名称可用于创建新的 CNAME 记录。主机名地址的格式将显示为 **az#####.vo.msecnd.net**（其中 **az#####** 是 CDN 终结点的标识符）。你可以复制此主机名，将其用于创建 CNAME 记录。对于此过程，请忽略引用 **cdnverify** 子域的文本。
5.	导航到域注册机构的网站，找到创建 DNS 记录的部分。可能会在“域名”、“DNS”或“名称服务器管理”等部分中找到此页。
6.	找到用于管理 CNAME 的部分。你可能需要转至高级设置页面，并找到“CNAME”、“别名”或“子域”字样。
7.	创建一个新的 CNAME 记录，将所选子域（例如 **www** 或 **cdn**）映射到在“管理自定义域”对话框中提供的主机名。
8.	返回到“管理自定义域”对话框，在对话框中输入自定义域（包括子域）。例如，按 www.mydomain.com 或 cdn.mydomain.com 格式输入域名。   

	Azure 会验证你所输入的域名是否存在 CNAME 记录。如果 CNAME 正确，则你的自定义域验证成功，可以用于 CDN 内容。

	请注意，在某些情况下，CNAME 记录传播到 Internet 上的名称服务器需要一定的时间。如果你的域并没有立即获得验证，而你确信 CNAME 记录是正确的，则请等待数分钟，然后重试。

##<a name="subheading2"></a>使用中间 cdnverify 子域为 Azure CDN 终结点注册自定义域  


1.	登录到 [Azure 管理门户](http://manage.windowsazure.cn/)。
2.	在导航窗格中，单击“CDN”。
3.	在列表视图中单击 CDN 终结点的名称（你需要将子域与其相关联），以便导航到该终结点的详细信息页。
4.	在功能区中，单击“管理域”以显示“管理自定义域”对话框。在对话框的文本中，你会看到从 CDN 终结点派生的主机名，该名称可用于创建新的 CNAME 记录，只需使用 **cdnverify** 中间子域即可。主机名地址的格式将显示为 **cdnverify.az#####.vo.msecnd.net**。你可以复制此主机名，将其用于创建 CNAME 记录。
5.	导航到域注册机构的网站，找到创建 DNS 记录的部分。可能会在“域名”、“DNS”或“名称服务器管理”等部分中找到此页。
6.	找到用于管理 CNAME 的部分。你可能需要转至高级设置页面，并查找“CNAME”、“别名”或“子域”字样。
7.	创建一个新的 CNAME 记录，并且提供包括 **cdnverify** 子域的子域别名。例如，你指定的子域将采用 **cdnverify.www** 或 **cdnverify.cdn** 格式。然后请提供主机名（你的 CDN 终结点），采用的格式为 **cdnverify.az#####.vo.msecnd.net**（其中 **az#####** 为 CDN 终结点的标识符）。在“管理自定义域”对话框中为你提供了主机名的格式。
8.	返回到“管理自定义域”对话框，在对话框中输入自定义域（包括子域）。例如，按 **www.mydomain.com** 或 **cdn.mydomain.com** 格式输入域名。请注意在此步骤中，你无需为子域添加前缀 **cdnverify**。  

	Azure 会验证你所输入的 cdnverify 域名是否存在 CNAME 记录。
9.	此时，你的自定义域已由 Azure 进行了验证，但传输到你的域的流量尚未路由到你的 CDN 终结点。若要完成此过程，请返回到你的 DNS 注册机构的网站，创建将你的子域映射到你的 CDN 终结点的另一条 CNAME 记录。例如，可将子域指定为 **www** 或 **cdn**，将主机名指定为 **az#####.vo.msecnd.net**。完成此步骤后，也就完成了你的自定义域的注册。 
10.	最后，你可以使用 **cdnverify** 删除你创建的 CNAME 记录，因为只需要将其作为中间步骤使用。  


##<a name="subheading3"></a>验证该自定义子域引用你的 CDN 终结点

-	完成自定义域的注册以后，你就可以通过自定义域访问缓存在 CDN 终结点的内容。首先，请确保你在终结点缓存了公共内容。例如，如果你的 CDN 终结点与某个存储帐户相关联，则 CDN 会将内容缓存在公共 blob 容器中。若要测试自定义域，请确保已将你的容器设置为允许公共访问，而且该容器包含至少一个 blob。
-	在浏览器中，导航到使用自定义域的 blob 的地址。例如，如果你的自定义域为 **www.mydomain.com**，则指向缓存的 blob 的 URL 将类似于以下 URL：  
	
		http://www.mydomain.com/mypubliccontainer/acachedblob.jpg
-	如果你的 CDN 终结点与云服务相关联，则已缓存内容的地址将类似于以下 URL：

		http://www.mydomain.com/cdn/mycloudservice

##另请参阅


[如何对 Azure 启用内容交付网络 (CDN)](/documentation/articles/cdn-create-new-endpoint)

 

<!---HONumber=74-->