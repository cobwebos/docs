<properties 
 pageTitle="如何在 Azure 内容交付网络 (CDN) 中管理 Bob 内容的过期问题" 
 description="" 
 services="cdn" 
 documentationCenter=".NET" 
 authors="zhangmanling" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="cdn" 
 ms.date="09/01/2015" 
 wacn.date=""/>


#如何在 Azure 内容交付网络 (CDN) 中管理 Bob 内容的过期问题  

最得益于 Azure CDN 缓存的 Blob 是那些在其生存时间 (TTL) 内被访问最频繁的 Blob。Blob 在其生存时间 (TTL) 内会始终保留在缓存中，在该时间过后会由 Blob 服务进行刷新。随后，此过程将重复。

你可以使用两个选项来控制 TTL。

1.	不设置缓存值，因此使用的是默认 TTL：7 天。 
2.	显式设置 *x-ms-blob-cache-control* 属性（针对“放置 Blob”、“放置阻止列表”或“设置 Blob 属性”请求），或者使用 Azure 托管库来设置 [BlobProperties.CacheControl](https://msdn.microsoft.com/zh-cn/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) 属性。设置此属性也就是设置 blob 的 *Cache-Control* 标头的值。标头或属性的值应以秒为单位指定相应的值。例如，若要将最大缓存期设置为 1 年，你可以将请求标头指定为 `x-ms-blob-cache-control: public, max-age=31556926`。有关设置缓存标头的详细信息，请参阅 [HTTP/1.1 规范](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html)。  

你希望通过 CDN 缓存的任何内容都必须作为可公开访问的 blob 存储在 Azure 存储帐户中。有关 Azure Blob 服务的更多详细信息，请参阅 **Blob 服务概念**。

你可以通过多种不同的方式来使用 Blob 服务中的内容：

-	使用 **Azure 托管库参考**所提供的托管 API。
-	使用第三方存储管理工具。
-	使用 Azure 存储服务 REST API。  

以下代码示例是一个控制台应用程序，该应用程序使用 Azure 托管库来创建容器、设置公共访问权限，以及在容器中创建 blob。它还通过在 blob 上设置 Cache-Control 标头来显式指定所需的刷新间隔。

假设你已启用 CDN（如上所示），则创建的 blob 将通过 CDN 进行缓存。请务必使用你自己的存储帐户和访问密钥指定帐户凭据：

	using System;
	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.StorageClient;
	
	namespace BlobsInCDN
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            //Specify storage credentials.
	            StorageCredentialsAccountAndKey credentials = new StorageCredentialsAccountAndKey("storagesample",
	                "m4AHAkXjfhlt2rE2BN/hcUR4U2lkGdCmj2/1ISutZKl+OqlrZN98Mhzq/U2AHYJT992tLmrkFW+mQgw9loIVCg==");
	            
	            //Create a reference to your storage account, passing in your credentials.
	            CloudStorageAccount storageAccount = new CloudStorageAccount(credentials, true);
	            
	            //Create a new client object, which will provide access to Blob service resources.
	            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
	
	            //Create a new container.
	            CloudBlobContainer container = blobClient.GetContainerReference("cdncontent");
	            container.CreateIfNotExist();
	
	            //Specify that the container is publicly accessible.
	            BlobContainerPermissions containerAccess = new BlobContainerPermissions();
	            containerAccess.PublicAccess = BlobContainerPublicAccessType.Container;
	            container.SetPermissions(containerAccess);
	
	            //Create a new blob and write some text to it.
	            CloudBlob blob = blobClient.GetBlobReference("cdncontent/testblob.txt");
	            blob.UploadText("This is a test blob.");
	
	            //Set the Cache-Control header on the blob to specify your desired refresh interval.
	            blob.SetCacheControl("public, max-age=31536000");
	        }
	    }
	
	    public static class BlobExtensions
	    {
	        //A convenience method to set the Cache-Control header.
	        public static void SetCacheControl(this CloudBlob blob, string value)
	        {
	            blob.Properties.CacheControl = value;
	            blob.SetProperties();
	        }
	    }
	}

测试你的 blob 是否可通过特定于 CDN 的 URL 进行访问。对于上述 blob，该 URL 将如下所示：

	http://az1234.vo.msecnd.net/cdncontent/testblob.txt  

如果需要，你可以使用 **wget** 或 Fiddler 之类的工具来查看请求和响应的详细信息。

##另请参阅

[如何在 Azure 内容交付网络 (CDN) 中管理云服务内容的过期问题](/documentation/articles/cdn-manage-expiration-of-cloud-service-content)

<!---HONumber=74-->