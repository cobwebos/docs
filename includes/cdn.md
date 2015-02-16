# 使用 Azure CDN

Azure 内容交付网络 (CDN) 为开发人员提供
全局解决方案，以通过缓存 blob 和
计算实例的静态内容，在位于美国、
欧洲、亚洲、澳大利亚和南美洲的物理节点上交付高带宽内容。有关 CDN 节点位置的当前列表，
请参阅 [Azure CDN 节点位置]。

此任务包括下列步骤：

* [步骤 1：创建存储帐户](#Step1)
* [步骤 2：为存储帐户创建新的 CDN 终结点](#Step2)
* [步骤 3：访问您的 CDN 内容](#Step3)
* [步骤 4：删除您的 CDN 内容](#Step4)

使用 CDN 来缓存 Azure 数据的优点包括：

-   为远离内容源以及使用的应用程序需要很多  'internet trips' 来加载内容的最终用户，提供更好的性能和用户体验。
-   大规模分布以更好地处理瞬间的高负载，例如在产品发布活动开始时。

现有 CDN 客户当前可在 [Azure 管理门户]中使用 Azure CDN。CDN 是您的订阅的一项附加功能，具有单独的[计费计划]。

<a id="Step1"> </a>
<h2>步骤 1：创建存储帐户</h2>

使用以下过程为
Azure 订阅创建新存储帐户。存储帐户提供对 
Azure 存储服务的访问权限。存储帐户表示
访问每个 Azure 存储服务组件的命名空间的最高级别
：Blob 服务、队列服务和表服务。有关
Azure 存储服务的详细信息，请参阅[使用
Azure 存储服务]。

若要创建存储帐户，您必须是服务
管理员或关联的订阅的共同管理员。

<div class="dev-callout">
<strong>注意</strong>
<p>有关使用
Azure 服务管理 API 执行此操作的信息，请参阅<a href="http://msdn.microsoft.com/zh-cn/library/windowsazure/hh264518.aspx">创建存储帐户</a>参考主题。</p>
</div>

**为 Azure 订阅创建存储帐户**

1.  登录到 [Azure 管理门户]。
2.  在左下角单击"新建"，然后单击"存储"。
3.  单击"快速创建"。

    此时将显示"创建存储帐户"对话框。

    ![创建存储帐户][create-new-storage-account]

4. 在 **URL** 字段中，键入子域名称。输入的名称可包含 3-24 个小写字母和数字。

    此值将成为用于对订阅的 Blob、队列或表资源进行
    寻址的 URI 中的主机名。如果
    对 Blob 服务中的容器资源进行寻址，则应该使用以下格式的 
    URI，其中 *&lt;StorageAccountLabel&gt;* 引用
    您在"输入 URL"中键入的值：

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **重要说明：**URL 标签构成存储帐户 URI 的子域，
    而且必须在 Azure 的所有托管服务中是唯一的。

	此值还在门户中用作此存储帐户的名称，或在通过编程方式访问此帐户时使用。

5.  从"区域/地缘组"下拉列表中，选择存储帐户的区域或地缘组。如果您希望存储服务与您所使用的其他 Windows Azure 服务位于同一数据中心，请选择一个地缘组而不是区域。这可以提高性能，且不会对传出收费。  

    **注意：**若要创建地缘组，请打开管理门户的"设置"区域，单击"地缘组"，然后单击"添加地缘组"或"添加"。您也可以使用 Windows Azure 服务管理 API 创建和管理地缘组。有关详细信息，请参阅[针对地缘组的操作]。

6. 从"订阅"下拉列表中，选择将与存储帐户结合使用的订阅。
7.  单击"创建存储帐户"。创建存储帐户的过程可能需要几分钟时间完成。
8.  若要确认存储帐户已成功创建，请确认帐户显示在"存储"的所列项目中，而且其状态为"联机"。

<a id="Step2"> </a>
<h2>步骤 2：为存储帐户创建新的 CDN 终结点</h2>

允许 CDN 对存储帐户或托管服务的访问后，所有
公开可用的对象都可进行 CDN 边缘缓存。如果您
修改当前已在 CDN 中缓存的对象，
则将无法通过该 CDN 使用新内容，直到 CDN 在缓存内容生存时间到期时
刷新其内容。

**为存储帐户创建新的 CDN 终结点**

1. 在"Azure 管理门户"[]的导航窗格中单击"CDN"。

2. 在功能区上，单击"新建"。在"新建"对话框中，选择"应用服务"，然后选择"CDN"，再选择"快速创建"。

3. 在"原始域"下拉列表中，从可用存储帐户列表中选择您在上一部分中创建的存储帐户。 

4. 单击"创建"按钮创建新的终结点。

5. 创建终结点后，它将显示在订阅的终结点列表中。该列表视图显示用于访问缓存内容的 URL 以及原始域。 

	原始域是 CDN 从其中缓存
    内容的位置。原始域可以是存储帐户或云服务；在本例中，我们使用存储帐户。根据您指定的缓存控制设置或缓存网络的默认启发，存储内容被缓存到边缘服务器。请参阅[如何管理 Blob 内容到期](http://msdn.microsoft.com/zh-cn/library/gg680306.aspx)。 


    <div class="dev-callout">
    <strong>注意</strong>
    <p>为终结点创建的配置不会
    即时可用；注册最多需要 60 分钟的时间
    通过 CDN 网络传播。试图立即
    使用 CDN 域名的用户，将会收到状态代码 400
    （"错误的请求"），直到内容可通过 CDN 获取。</p>
    </div>

<a id="Step3"> </a>
<h2>步骤 3：访问 CDN 内容</h2> 

若要访问 CDN 上的缓存内容，请使用门户中提供的 CDN URL。缓存 Blob 的地址将如下所示：

http://<*CDNNamespace*\>.vo.msecnd.net/<*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>
<h2>步骤 4：从 CDN 删除内容</h2>

如果您不再希望在 Azure 内容交付网络 (CDN) 中缓存某一对象，
则您可以执行以下步骤之一：

-   对于 Azure Blob，您可以从公共
    容器中删除 Blob。
-   您可将容器设为专用的而不是公用的。请参阅[限制对容器和 Blob 的访问](http://msdn.microsoft.com/zh-cn/library/dd179354.aspx) 以了解更多信息。
-   您可以使用管理
    门户禁用或删除 CDN 终结点。
-   您可以修改托管服务，使其不再响应
    对象的请求。

已缓存在 CDN 中的对象将保留在缓存中，直至
对象的生存时间到期。在该生存时间
到期时，CDN 将进行检查以确定 CDN 终结点是否仍然
有效并且是否仍可对该对象进行匿名访问。如果无效，则
无法再缓存该对象。

当前没有显式的"清除"工具可用于 Azure 
CDN。

## 其他资源

-   [如何在 Azure 中创建地缘组]
-   [如何：管理 Azure 订阅的存储帐户]
-   [关于服务管理 API]
-   [如何将 CDN 内容映射到自定义域]

  [创建存储帐户]: http://msdn.microsoft.com/zh-cn/library/windowsazure/hh264518.aspx
  [Azure CDN 节点位置]: http://msdn.microsoft.com/zh-cn/library/windowsazure/gg680302.aspx
  [Azure 管理门户]: https://manage.windowsazure.com/
  [收费计划]: /zh-cn/pricing/calculator/?scenario=full
  [如何注册用于访问 Azure 中的 Blob 的自定义子域名称]: http://msdn.microsoft.com/zh-cn/library/windowsazure/ee795179.aspx
  [对地缘组的操作]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Azure CDN 概述]: http://msdn.microsoft.com/zh-cn/library/windowsazure/ff919703.aspx
  [如何：管理 Azure 订阅的存储帐户]: http://msdn.microsoft.com/zh-cn/library/windowsazure/hh531567.aspx
  [关于服务管理 API]: http://msdn.microsoft.com/zh-cn/library/windowsazure/ee460807.aspx
  [如何将 CDN 内容映射到自定义域]: http://msdn.microsoft.com/zh-cn/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[以前的管理门户]: ../../Shared/Media/previous-portal.png
<!--HONumber=41-->
