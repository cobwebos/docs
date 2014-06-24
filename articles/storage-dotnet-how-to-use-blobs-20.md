<properties linkid="dev-net-2-how-to-blob-storage" urlDisplayName="Blob 服务 (2.0)" pageTitle="如何使用 Blob 存储 - Windows Azure 功能指南" metaKeywords="Azure Blob 入门   Azure 非结构化数据   Azure 非结构化存储   Azure Blob   Azure Blob 存储   Azure Blob .NET   Azure Blob C#   Azure Blob C#" description="了解如何使用 Windows Azure Blob 服务上载、下载、列出和删除 Blob 内容。相关示例用 C# 编写。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="如何在 .NET 中使用 Windows Azure Blob 存储服务" />


# 如何在 .NET 中使用 Windows Azure Blob 存储服务

<div class="dev-center-tutorial-selector">
<a href="/zh-cn/develop/net/how-to-guides/blob-storage-v17/" title="版本 1.7">版本 1.7</a>
<a href="/zh-cn/develop/net/how-to-guides/blob-storage/" title="版本 2.0" class="current">版本 2.0</a> 
</div>


本指南将演示如何使用 Windows Azure Blob 存储服务
执行常见任务。相关示例用 C\# 编写并使用 Windows Azure .NET 存储
客户端库（2.0 版）。涉及的任务包括上载、列出、下载和
删除 Blob。有关 Blob 的详细信息，
请参阅[后续步骤][]一节。

##目录

-   [什么是 Blob 存储][]
-   [概念][]
-   [创建 Windows Azure 存储帐户][]
-   [设置存储连接字符串][]
-   [如何以编程方式访问 Blob 存储][]
-   [如何创建容器][]
-   [如何将 Blob 上载到容器][]
-   [如何列出容器中的 Blob][]
-   [如何下载 Blob][]
-   [如何删除 Blob][]
-   [后续步骤][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a><span  class="short-header">创建帐户</span>创建 Windows Azure 存储帐户
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="setup-connection-string"></a><span  class="short-header">设置连接字符串</span>设置存储连接字符串

Windows Azure .NET 存储客户端库支持使用
存储连接字符串来配置终结点和用于访问存储服务
的凭据。您可以将存储连接字符串置于配置文件中，而不是在代码
中对其进行硬编码：

- 在使用 Windows Azure 云服务时，建议使用 Windows Azure 服务配置系统（*.csdef 和 *.cscfg 文件）存储连接字符串。
- 当使用 Windows Azure 网站、Windows Azure 虚拟机或构建准备在 Windows Azure 外部运行的 .NET 应用程序时，建议您使用 .NET 配置系统（如 web.config 或 app.config 文件）来存储连接字符串。

稍后将在本指南中介绍如何检索连接字符串。

### 使用云服务时配置连接字符串

该服务配置机制是 Windows Azure 云服务项目特有的，
它使您能够从 Windows Azure 管理门户动态更改配置设置，
而无需部署您的
应用程序。

在 Windows Azure 服务配置中配置
连接字符串：

1. 在 Visual Studio 解决方案资源管理器内 Windows Azure 部署
项目的“角色”文件夹中，右键单击 Web 角色或辅助角色，
然后单击“属性”。
    ![在 Visual Studio 中选择云服务角色的属性][Blob5]

2. 单击“设置”选项卡并按“添加设置”按钮。
    ![在 Visual Studio 中添加云服务设置][Blob6]

    新的 Setting1 条目稍后将显示在设置网格中。

3. 在新的 Setting1 条目的“类型”下拉列表中，
选择“连接字符串”。
    ![Blob7][Blob7]

4. 单击 Setting1 条目最右侧的 ... 按钮。
    将打开“存储帐户连接字符串”对话框。

5. 选择是要定位到存储仿真程序（在本地计算机
上模拟的 Windows Azure 存储），还是云中的
存储帐户。本指南中的代码使用其中
任一方式。如果您希望使用我们之前在 Windows Azure 中
创建的存储帐户存储 Blob 数据，请输入从本教程前面
的步骤中复制的“主访问密钥”值。
    ![Blob8][Blob8]

6. 将条目“名称”从 Setting1 更改为更友好
的名称，例如 StorageConnectionString。稍后将在本指南的代码中引用
此连接字符串。
    ![Blob9][Blob9]
	
### 使用 .NET 配置来配置连接字符串

如果您正在编写不是 Windows Azure 云服务的应用程序（请参见上一节），则建议您使用 .NET 配置系统（如 web.config 或 app.config）。这包括 Windows Azure 网站或 Windows Azure 虚拟机，以及设计为在 Windows Azure 外部运行的应用程序。您可以使用 <appSettings> 元素存储连接字符串，如下所示：

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
  		</appSettings>
	</configuration>

阅读[配置连接字符串][]，了解有关存储连接字符串的详细信息。
	
您现在即可准备执行本指南中的操作任务。

## <a name="configure-access"> </a><span  class="short-header">以编程方式访问</span>如何以编程方式访问 Blob 存储

###获得程序集
您可以使用 NuGet 获得 Microsoft.WindowsAzure.Storage.dll 程序集。在“解决方案资源管理器”中，右键单击您的项目并选择“管理 NuGet 包”。在线搜索“WindowsAzure.Storage”，然后单击“安装”以安装 Windows Azure 存储包和依赖项。

Microsoft.WindowsAzure.Storage.dll 还包含在 Windows Azure SDK for .NET 2.0 中，可从 <a href="http://www.windowsazure.com/zh-cn/develop/net/#">.NET 开发人员中心</a>下载该版本。程序集安装在 %Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\v2.0\ref\ 目录中。

###命名空间声明
将以下命名空间声明添加到任何 C\# 文件的顶部，
您希望使用此类文件以编程方式访问 Windows Azure 存储：

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

确保您引用 Microsoft.WindowsAzure.Storage.dll 程序集。

###检索连接字符串
可以使用 CloudStorageAccount 类型来表示您的
存储帐户信息。如果您使用的是 Windows Azure 项目
模板并且/或者引用 Microsoft.WindowsAzure.CloudConfigurationManager，
则可以使用 CloudConfigurationManager 类型
从 Windows Azure 服务配置中检索
存储连接字符串和存储帐户信息：

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

如果您要创建应用程序而不引用 Microsoft.WindowsAzure.CloudConfigurationManager，并且您的连接字符串位于前面显示的 web.config 或 app.config 中，那么您可以使用 ConfigurationManager 来检索连接字符串。您需要将对 System.Configuration.dll 的引用添加到您的项目中，并为其添加另一个命名空间声明：

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

您可以使用 CloudBlobClient 类型检索表示存储在 Blob 存储
服务中的容器和 Blob 的对象。以下代码
使用我们在上面检索到的存储帐户对象
创建 CloudBlobClient 对象：

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###ODataLib 依赖项
.NET 存储客户端库中的 ODataLib 依赖项可通过在 NuGet （而非 WCF 数据服务）上获得的 ODataLib（5.0.2 版）包来解析。ODataLib 库可直接下载或者通过 NuGet 由代码项目引用。特定的 ODataLib 包为 [OData]、[Edm] 和 [Spatial]。

## <a name="create-container"> </a><span  class="short-header">创建容器</span>如何创建容器

所有存储 Blob 都驻留在一个容器中。您可以使用
CloudBlobClient 对象获取对要使用的容器的引用。如果该容器不存在，您可以创建它：

    // 从连接字符串检索存储帐户。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 创建 Blob 客户端。
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 检索对容器的引用。
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // 如果该容器不存在，则创建它。
    container.CreateIfNotExists();

默认情况下，新容器是专用容器，因此
您必须指定存储访问密钥才能从该容器
下载 Blob。如果您要让容器中的文件可供所有人使用，
则可以使用以下代码将容器设置为公共
容器：

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

Internet 中的所有人都可以查看公共容器中的 Blob，但是，
仅在您具有相应的访问密钥时，才能修改或删除它们。

<h2> <a name="upload-blob"> </a><span  class="short-header">上载到容器</span>如何将 Blob 上载到容器</h2>

Windows Azure Blob 存储支持块 Blob 和页 Blob。大多数情况下，推荐使用块 Blob。

若要将文件上载到块 Blob，请获取容器引用，
并使用它获取块 Blob 引用。获取 Blob 引用后，可以通过调用 UploadFromStream 方法，
将任何数据流上载到该 Blob。如果之前不存在 Blob，此操作将创建一个；
如果存在 Blob，此操作将覆盖它。下面的示例演示了如何将 Blob 上载到容器中，并假定已创建容器。

    // 从连接字符串检索存储帐户。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 创建 Blob 客户端。
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 检索对以前创建的容器的引用。
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // 检索对名为“myblob”的 Blob 的引用。
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // 使用来自本地文件的内容创建或覆盖“myblob”Blob。
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

##<a name="list-blob"> </a><span  class="short-header">列出容器中的 Blob</span>如何列出容器中的 Blob

若要列出容器中的 Blob，首先需要获取容器引用。然后，您可以使用容器的 ListBlobs 方法来检索
其中的 Blob 和/或目录。若要访问返回的 IListBlobItem 的丰富属性
和方法，您必须将它转换到 CloudBlockBlob、
CloudPageBlob 或 CloudBlobDirectory 对象。如果类型未知，您可以使用类型检查来确定
要将其转换为哪种类型。以下代码
演示了如何检索和输出 photos 容器中
每项的 URI：

    // 从连接字符串检索存储帐户。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 创建 Blob 客户端。
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// 检索对以前创建的容器的引用。
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// 循环访问容器内的项并且输出长度和 URI。
	foreach (IListBlobItem item in container.ListBlobs(null, false))
	{
		if (item.GetType() == typeof(CloudBlockBlob))
		{
			CloudBlockBlob blob = (CloudBlockBlob)item;

			Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
                                        
		}
		else if (item.GetType() == typeof(CloudPageBlob))
		{
			CloudPageBlob pageBlob = (CloudPageBlob)item;

			Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

		}
		else if (item.GetType() == typeof(CloudBlobDirectory))
		{
			CloudBlobDirectory directory = (CloudBlobDirectory)item;
			
			Console.WriteLine("Directory: {0}", directory.Uri);
		}
	}

如上所示，还可将 Blob 服务视为容器中的
目录。这是为了让您能够以更类似于文件夹的结构来
组织 Blob。例如，考虑名为 photos 的容器中包含的下面
一组块 Blob：

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

当您对“photos”容器调用 ListBlobs 时（如上面的示例所示），
返回的集合将包含 CloudBlobDirectory 和 CloudBlockBlob 对象，
分别表示最高层中所含的目录和 Blob。下面是生成的输出：

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


或者，也可以将 ListBlobs 方法的 UseFlatBlobListing 参数
设置为 true。这将导致每个 Blob 将作为 CloudBlockBlob 返回，
而无论目录如何。下面是对 ListBlobs 的调用：

    // 循环访问容器内的项并且输出长度和 URI。
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

下面是结果：

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

有关更多信息，请参见 [CloudBlobContainer.ListBlobs][]。

## <a name="download-blobs"> </a><span  class="short-header">下载 Blob</span>如何下载 Blob

若要下载 Blob，首先要检索 Blob 引用，然后调用 DownloadToStream 方法。以下示例使用 DownloadToStream 方法
将 Blob 内容传输到稍后可以保存
到本地文件的流对象。

    // 从连接字符串检索存储帐户。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 创建 Blob 客户端。
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 检索对以前创建的容器的引用。
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // 检索对名为“photo1.jpg”的 Blob 的引用。
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // 将 Blob 内容保存到一个文件。
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

也可以使用 DownloadToStream 方法以文本字符串形式下载 Blob 的内容。

	// 从连接字符串检索存储帐户。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 创建 Blob 客户端。
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 检索对以前创建的容器的引用。
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// 检索对名为“myblob.txt”的 Blob 的引用
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

##<a name="delete-blobs"> </a><span  class="short-header">删除 Blob</span>如何删除 Blob

若要删除 Blob，首先要获取 Blob 引用，然后对其
调用 Delete 方法。

    // 从连接字符串检索存储帐户。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 创建 Blob 客户端。
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 检索对以前创建的容器的引用。
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // 检索对名为“myblob.txt”的 Blob 的引用。
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // 删除 Blob。
    blockBlob.Delete(); 

## <a name="next-steps"></a><span  class="short-header">后续步骤</span>后续步骤

现在，您已了解有关 Blob 存储的基础知识，单击下面的链接
可了解如何执行更复杂的存储任务。
<ul>
<li>查看 Blob 服务参考文档，了解有关可用 API 的完整详情：
  <ul>
    <li><a href="http://msdn.microsoft.com/zh-cn/library/windowsazure/wa_storage_api_ref_reference_home.aspx">.NET 存储客户端库参考</a>
    </li>
    <li><a href="http://msdn.microsoft.com/zh-cn/library/windowsazure/dd179355">REST API 参考</a></li>
  </ul>
</li>
<li>在以下位置了解有关可以使用 Windows Azure 存储执行的更高级任务：<a href="http://msdn.microsoft.com/zh-cn/library/windowsazure/gg433040.aspx">在 Windows Azure 中存储和访问数据</a>。</li>
<li>查看更多功能指南，以了解在 Windows Azure 中存储数据的其他方式。
  <ul>
    <li>使用<a href="/zh-cn/develop/net/how-to-guides/table-services/">表存储</a>存储结构化数据。</li>
    <li>使用 <a href="/zh-cn/develop/net/how-to-guides/sql-database/">SQL Database</a> 存储关系数据。</li>
  </ul>
</li>
</ul>

  [后续步骤]: #next-steps
  [什么是 Blob 存储]: #what-is
  [概念]: #concepts
  [创建 Windows Azure 存储帐户]: #create-account
  [设置存储连接字符串]: #setup-connection-string
  [如何以编程方式访问 Blob 存储]: #configure-access
  [如何创建容器]: #create-container
  [如何将 Blob 上载到容器]: #upload-blob
  [如何列出容器中的 Blob]: #list-blob
  [如何下载 Blob]: #download-blobs
  [如何删除 Blob]: #delete-blobs
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs-20/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs-20/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs-20/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs-20/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs-20/blob9.png
  
  [在 Windows Azure 中存储和访问数据]: http://msdn.microsoft.com/zh-cn/library/windowsazure/gg433040.aspx
  [Windows Azure 存储团队博客（可能为英文网页）]: http://blogs.msdn.com/b/windowsazurestorage/
  [配置连接字符串]: http://msdn.microsoft.com/zh-cn/library/windowsazure/ee758697.aspx
  [.NET 客户端库引用]: http://msdn.microsoft.com/zh-cn/library/windowsazure/wl_svchosting_mref_reference_home
  [REST API 参考]: http://msdn.microsoft.com/zh-cn/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

