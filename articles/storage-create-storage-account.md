<properties linkid="manage-services-how-to-create-a-storage-account" urlDisplayName="如何创建" pageTitle="如何创建存储帐户 - Windows Azure" metaKeywords="" description="了解如何在 Windows Azure 管理门户中创建存储帐户。" metaCanonical="" services="storage" documentationCenter="" title="如何创建存储帐户" authors=""  solutions="" writer="tysonn" manager="" editor=""  />




<h1><a id="createstorageaccount"></a>如何创建存储帐户</h1>

要在 Windows Azure 中存储 Blob、表和队列服务中的文件和数据，您必须在要存储数据的地理区域创建存储帐户。一个存储帐户最多可包含 200 TB 的数据，前提是它是在 2012 年 6 月 8 日或之后创建的。对于在 2012 年 6 月 8 日之前创建的存储帐户，可存储 100 TB 的 Blob、表和队列数据。您可以为每个 Windows Azure 订阅创建多达 20 个存储帐户。有关更多信息，请参见 [Windows Azure 存储可伸缩性和性能目标](http://msdn.microsoft.com/zh-cn/library/dn249410.aspx)。

本主题介绍如何在 Windows Azure 管理门户中创建存储帐户。

<div class="dev-callout"> 
<b>注意</b>
<p>对于 Windows Azure 虚拟机，如果您在部署位置尚不具有存储帐户，则会在该位置自动创建一个存储帐户。存储帐户名称将基于虚拟机名称。</p>
</div>

##目录##

* [如何创建存储帐户](#create)
* [后续步骤](#next)

<h2><a id="create"></a>如何创建存储帐户</h2>

1. 登录到[管理门户](https://manage.windowsazure.com)。

2. 依次单击“新建”、“存储”和“快速创建”。

	![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3. 在“URL”中，输入要在存储帐户 URL 中使用的子域名称。若要访问存储中的对象，请将该对象的位置附加到终结点。例如，访问 Blob 的 URL 可以是 http://*myaccount*.blob.core.windows.net/*mycontainer*/*myblob*

4. 在“区域/地缘组”中，为存储选择区域或地缘组。如果您希望存储服务与您所使用的其他 Windows Azure 服务位于同一数据中心，请选择一个地缘组而不是区域。这可以提高性能，且不会对传出收费。

	> [WACOM.NOTE]
        > 若要创建地缘组，请打开管理门户的“网络”<b></b>区域，单击“地缘组”<b></b>，然后单击“创建新的地缘组”<b></b>或“创建”<b></b>。可以使用您在以前的管理门户中创建的地缘组。若要打开其他门户，请单击标题栏上的“预览”<b></b>，然后单击“转到以前的门户”<b></b>。（要返回此门户，请单击门户底部的“查看预览门户”。<b></b>）您也可以使用 Windows Azure 服务管理 API 创建和管理地缘组。有关更多信息，请参见<a href="http://msdn.microsoft.com/zh-cn/library/windowsazure/ee460798.aspx">针对地缘组的操作</a>。

5. 如果您具有多个 Windows Azure 订阅，则显示“订阅”字段。在“订阅”中，输入要使用存储帐户的 Windows Azure 订阅。您可以为一个订阅创建多达 5 个存储帐户。

6. 在“复制”中，选择对您的存储帐户所期望的复制级别。

	默认情况下，复制设置为 Geo-Redundant.。使用地域冗余复制，在主要位置中发生重大灾难时，您的存储帐户以及帐户中的所有数据都将故障转移到辅助位置。Windows Azure 在同一区域中分配辅助位置，并且不能更改。在进行故障转移后，辅助位置将成为存储帐户的主要位置，并且会将数据复制到新的辅助位置。

 	如果不希望使用地域冗余复制，或者如果贵组织的策略不允许使用它，则可以将“复制”设置为“本地冗余”。此时将使用以优惠价提供的本地冗余存储。请注意，如果您关闭地域冗余复制，但稍后决定重新打开它，那么在您将现有数据复制到辅助位置时，将向您收取一次性费用。

	第三个复制选项“读取访问地域冗余”目前处于预览状态。此选项允许对辅助位置中的已复制数据进行只读访问。通过读取访问地域冗余复制，您可以在一个位置变得不可用时从主位置或辅助位置访问您的数据。

	> [WACOM.NOTE]
        > 若要使用读取访问地域冗余复制（尽管它现在处于预览状态），您必须手动请求为您的订阅启用该功能。请访问 <a href="https://account.windowsazure.com/PreviewFeatures">Windows Azure 预览功能</a>页，请求针对您的订阅的读取访问地域冗余复制。有关读取访问地域冗余复制的详细信息，请参阅 <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/04/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx">Windows Azure 存储团队博客</a>。
	> 如果没有将读取访问地域冗余复制作为针对您的订阅的预览功能启用，将禁用为您的存储帐户选择它的选项。

	有关存储帐户复制的定价信息，请参见[存储定价详细信息](http://www.windowsazure.com/zh-cn/pricing/details/storage/)。

6. 单击“创建存储帐户”。

	可能需要几分钟时间来创建存储帐户。若要检查状态，您可以监视门户底部的通知。创建存储帐户后，您的新存储帐户将处于“联机”状态并且随时可供使用。

	![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)

<h2><a id="next"></a>后续步骤</h2>

- 若要了解有关 Windows Azure 存储服务的详细信息，请参阅[了解云存储](http://www.windowsazure.com/zh-cn/develop/net/fundamentals/cloud-storage/)以及 [Blob、队列和表](http://msdn.microsoft.com/zh-cn/library/gg433040.aspx)。

- 访问 [Windows Azure 存储团队博客](http://blogs.msdn.com/b/windowsazurestorage/)。

- 配置您的应用程序以使用 Windows Azure Blob、表和队列服务。[Windows Azure 开发人员中心](http://www.windowsazure.com/zh-cn/develop/overview/)提供了将 Blob、表和队列存储服务与 .NET、Node.js、Java 和 PHP 应用程序结合使用的操作方法指南。有关特定于编程语言的说明，请参阅该语言相应的操作方法指南。


