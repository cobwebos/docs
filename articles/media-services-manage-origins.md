<properties linkid="scripting-center-index" urlDisplayName="index" pageTitle="脚本中心索引" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Manage Origins in a Media Services Account" authors="juliako" solutions="" manager="" editor="" />





# <a id="managemediaservicesorigins"></a>如何在 Media Services 帐户中管理流式处理终结点

在 Media Services 中，流式处理终结点表示一个流服务，该服务可以直接将内容传递给客户端播放器应用程序，也可以传递给内容交付网络 (CDN) 以进一步分发。目前，Microsoft Azure Media Services 未提供无缝 CDN 集成，但你可以通过市面上的一个 CDN 提供商（Azure CDN 或 Akamai）获取。流式处理终结点服务的出站流可以是实时流，也可以是 Media Services 帐户中的视频点播资产。 

此外，还可以通过调整扩展单元（也称为流单元）来控制流式处理终结点服务处理不断增长的带宽需求的能力。建议为生产环境中的应用程序分配一个或多个扩展单元。缩放单元提供能够以 200 Mbps 为增量购买的专用出口容量和附加功能（当前包括使用动态打包）。 

本主题说明如何使用 Azure 管理门户管理流式处理终结点。


## 添加和删除流式处理终结点 

1. 在[管理门户](https://manage.windowsazure.cn/)中单击"Media Services"。然后，单击 Media Services 的名称。
2. 选择"流式处理终结点"页。 
3. 单击页底部的"添加"或"删除"按钮。请注意，不能删除默认的流式处理终结点。 
4. 单击"启动"按钮以启动流式处理终结点。 
5. 单击要配置的流式处理终结点的名称。   

	![Origin page][origin-page]

## <a id="scale_streaming_endpoints"></a>缩放流式处理终结点

流式处理单位为你提供了可按照 200 Mbps 的增量购买的专用出口容量和其他功能（当前包括[动态包装功能](https://msdn.microsoft.com/zh-cn/library/jj889436.aspx)）。默认情况下，将在共享实例模型中配置按需流式处理，在该模型中，服务器资源（例如，计算、出口容量等）将与所有其他用户共享。若要增加按需流式处理吞吐量，建议购买流式处理单位。 

若要更改流式处理单位数，请执行以下操作：

1. 若要指定流式处理单位数，请选择"缩放"选项卡并移动"保留容量"滑块。

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. 按"保存"按钮保存更改。

	分配所有新的流式处理单位大约需要 20 分钟才能完成。 

	 
	>[WACOM.NOTE] 当前，将流式处理单位的任何正值设置回"无"可将按需流式处理功能禁用最多 1 小时。

	>[WACOM.NOTE] 为 24 小时期间指定的最大单位数将用于计算成本。有关定价详细信息的信息，请参阅 [Media Services 定价详细信息](/pricing/details/media-services/)。
	
## 配置流式处理终结点

可以根据下图中所示，使用"配置"选项卡执行配置。后面提供了字段的说明。

>[WACOM.NOTE] 此页上的配置仅适用于至少具有一个保留单元的流式处理终结点。保留按需流式处理保留单元。

![Configure origin][configure-origin]
  

1. 设置在 HTTP 响应的缓存控制标头中指定的最长缓存期。此值将不覆盖在 blob 内容上显式设置的最大缓存值。

2. 指定将允许连接到发布的流式处理终结点的 IP 地址。如果未指定 IP 地址，则任何 IP 地址都可以连接。

3. 指定 Akamai 签名标头身份验证的配置。

4. 你可以为 Adobe Flash 客户端指定跨域访问策略（有关详细信息，请参阅[跨域策略文件规范](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)），以及为 Microsoft Silverlight 客户端指定客户端访问策略（有关详细信息，请参阅[跨域边界提供服务](https://msdn.microsoft.com/zh-cn/library/cc197955(v=vs.95).aspx)）。  

5. 还可以通过单击"配置"按钮配置自定义主机名。有关详细信息，请参阅 [StreamingEndpont](https://msdn.microsoft.com/zh-cn/library/dn783468.aspx) 主题中的 **CustomHostNames** 属性。  




[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png
