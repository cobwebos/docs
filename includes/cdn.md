# <a name="using-cdn-for-azure"></a>使用 Azure CDN
Azure 内容交付网络 (CDN) 通过在遍布美国、欧洲、亚洲、澳大利亚和南美洲的众多物理节点上缓存 Blob 和计算实例的静态内容，为开发人员提供一个传送高带宽内容的全球性解决方案。 有关最新的 CDN 节点位置列表，请参阅 [Azure CDN 节点位置]。

此任务包括下列步骤：

* [步骤 1：创建存储帐户](#Step1)
* [步骤 2：为存储帐户创建新的 CDN 终结点](#Step2)
* [步骤 3：访问 CDN 内容](#Step3)
* [步骤 4：删除 CDN 内容](#Step4)

使用 CDN 来缓存 Azure 数据的优点包括：

* 为远离内容源的最终用户提供更好的性能和用户体验，使用的应用程序需要很多“Internet 往返”来加载内容
* 大规模分布以更好地处理瞬间的高负载，例如在产品发布等活动开始时

目前，现有 CDN 客户可在 [Azure 经典门户]中使用 Azure CDN。 CDN 是订阅的一项附加功能，具有单独的[计费计划]。

<a id="Step1"> </a>

<h2>步骤 1：创建存储帐户</h2>

使用以下过程为 Azure 订阅创建新的存储帐户。 通过存储帐户可访问 Azure 存储服务。 存储帐户表示用于访问下述每个 Azure 存储服务组件的最高级别的命名空间：Blob 服务、队列服务、表服务。 有关 Azure 存储服务的详细信息，请参阅[使用 Azure 存储服务](http://msdn.microsoft.com/library/azure/gg433040.aspx)。

要创建一个存储帐户，必须是相关订阅的服务管理员或联合管理员。

> [!NOTE]
> 有关使用 Azure 服务管理 API 执行此操作的信息，请参阅[创建存储帐户](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx)参考主题。
> 
> 

**为 Azure 订阅创建存储帐户**

1. 登录到 [Azure 经典门户]。
2. 在左下角单击“新建”。 在“新建”对话框中，选择“数据服务”，并单击“存储”，再单击“快速创建”。
   
   此时会显示“创建存储帐户”对话框。
   
   ![创建存储帐户][create-new-storage-account]
3. 在“URL”字段中，键入子域名称。 输入的名称可包含 3-24 个小写字母和数字。
   
    此值将成为用于对订阅的 Blob、队列或表资源进行寻址的 URI 中的主机名。 若要对 BLOB 服务中的容器资源进行寻址，需使用以下格式的 URI，其中 *&lt;StorageAccountLabel&gt;* 是指在“输入 URL”中键入的值：
   
    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*
   
    **重要提示：**该 URL 标签构成存储帐户 URI 的子域，必须在 Azure 中的所有托管服务中独一无二。
   
    此值还在门户中用作此存储帐户的名称，或在通过编程方式访问此帐户时使用。
4. 从“区域/地缘组”下拉列表中，选择存储帐户的区域或地缘组。 如果希望存储服务与所用的其他 Microsoft Azure 服务位于同一数据中心，请选择一个地缘组而不是区域。 这可以提高性能，且不会对传出收费。  
   
   **注意：**要创建地缘组，请打开管理门户的“设置”区域，单击“地缘组”，并单击“添加地缘组”或“添加”。 也可以使用 Microsoft Azure 服务管理 API 创建和管理地缘组。 有关详细信息，请参阅[针对地缘组的操作]。
5. 从“订阅”下拉列表中，选择将与存储帐户结合使用的订阅。
6. 单击“创建存储帐户” 。 创建存储帐户的过程可能需要几分钟时间完成。
7. 若要验证存储帐户是否已成功创建，请确认帐户显示在“存储”的所列项目中，并且其状态为“联机”。

<a id="Step2"> </a>

<h2>步骤 2：为存储帐户创建新的 CDN 终结点</h2>

一旦启用对存储帐户或托管服务的 CDN 访问，所有公开可用的对象将有资格获得 CDN 边缘缓存。 如果修改一个当前在 CDN 中缓存的对象，则只有 CDN 在缓存内容生存时间到期时刷新了对象的内容后，才能通过 CDN 访问新内容。

**为存储帐户创建新的 CDN 终结点**

1. 在 [Azure 经典门户]的导航窗格中单击“CDN”。
2. 在功能区上，单击“新建”。 在“新建”对话框中，依次选择“应用服务”、“CDN”、“快速创建”。
3. 在“原始域”下拉列表中，从可用存储帐户列表中选择在上一部分中创建的存储帐户。 
4. 单击“创建”按钮创建新的终结点。
5. 终结点创建后将出现在订阅的终结点的列表中。 该列表视图显示用于访问缓存内容的 URL 以及原始域。 
   
    原始域是 CDN 缓存内容的位置。 原始域可以是存储帐户或云服务；在本例中，我们使用存储帐户。 根据指定的缓存控制设置或缓存网络的默认启发，存储内容被缓存到边缘服务器。 

    > [AZURE.NOTE] 为终结点创建的配置将不能立即可用；最多需要 60 分钟时间进行注册以便通过 CDN 网络传播。 尝试立即使用 CDN 域名的用户可能会收到状态代码 400（错误请求），直到可通过 CDN 访问该内容。

<a id="Step3"> </a>

<h2>步骤 3：访问 CDN 内容</h2> 

若要访问 CDN 上的缓存内容，请使用门户中提供的 CDN URL。 缓存 Blob 的地址将如下所示：

http://<*CDNNamespace*\>.vo.msecnd.net/<*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>

<h2>步骤 4：从 CDN 删除内容</h2>

如果不再想在 Azure 内容交付网络 (CDN) 中缓存对象，则可执行下列步骤之一：

* 对于 Azure Blob，可从公共容器中删除 Blob。
* 可将容器设为专用的而不是公用的。 有关详细信息，请参阅[限制对容器和 Blob 的访问](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/#restrict-access-to-containers-and-blobs)。
* 可使用管理门户禁用或删除 CDN 终结点。
* 可将托管服务修改为不再响应此对象的请求。

已在 CDN 中缓存的对象将保持缓存状态，直到该对象的生存时间到期为止。 当生存时间到期时，CDN 将查看 CDN 终结点是否仍有效，且是否仍可对该对象进行匿名访问。 如果不能，则不再对该对象进行缓存。

Azure 管理门户当前不支持即时清除内容的功能。 如果需要立即清除内容，请联系 [Azure 支持](https://azure.microsoft.com/support/options/)。 

## <a name="additional-resources"></a>其他资源
* [如何在 Azure 中创建地缘组]
* [如何管理 Azure 订阅的存储帐户]
* [关于服务管理 API]
* [如何将 CDN 内容映射到自定义域]

[Create Storage Account]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
[Azure CDN 节点位置]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
[Azure 经典门户]: https://manage.windowsazure.com/
[计费计划]: /pricing/calculator/?scenario=full
[如何在 Azure 中创建地缘组]: http://msdn.microsoft.com/library/azure/ee460798.aspx
[Overview of the Azure CDN]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
[关于服务管理 API]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
[如何将 CDN 内容映射到自定义域]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Previous Management Portal]: ../../Shared/Media/previous-portal.png
