<properties linkid="manage-services-mediaservices-scale-media-service" urlDisplayName="How to scale" pageTitle="如何缩放 Media Services | Azure 文档" metaKeywords="" description="了解如何通过指定要为帐户设置的"按需流式处理保留单位"和"编码保留单位"数，缩放 Media Services 。" metaCanonical="" services="media-services" documentationCenter="" title="How to Scale a Media Service" authors="migree" solutions="" manager="" editor="" />





# 如何缩放 Media Services   

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]


通过指定要为帐户设置的"按需流式处理保留单位"和"编码保留单位"数，可以缩放 Media Services。 


<h2>按需流式处理保留单位</h2>

按需流式处理保留单位为你提供了可按照 200 Mbps 的增量购买的专用出口容量和其他功能（当前包括[动态打包功能](https://msdn.microsoft.com/zh-cn/library/jj889436.aspx)）。默认情况下，将在共享实例模型中配置按需流式处理，在该模型中，服务器资源（例如，计算、出口容量等）将与所有其他用户共享。若要增加按需流式处理吞吐量，建议购买按需流式处理保留单位。 

若要更改按需流式处理保留单位数，请执行以下操作：

1. 在[管理门户](https://manage.windowsazure.cn/)中单击"Media Services"。然后，单击 Media Services 的名称。

2. 选择"流式处理终结点"页。然后，单击要修改的流式处理终结点。


3. 若要指定流式处理单位数，请选择"缩放"选项卡并移动"保留容量"滑块。

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. 按"保存"按钮保存更改。

	分配任何新的按需流式处理单位均需约 20 分钟才能完成。 

	 
	>[WACOM.Note] 当前，将按需流式处理单位的任何正值设置回"无"可将按需流式处理功能禁用最多 1 小时。

	>[WACOM.Note] 为 24 小时期间指定的最大单位数将用于计算成本。有关定价详细信息的信息，请参阅 [Media Services 定价详细信息](/pricing/details/media-services/)。

<h2>编码保留单位</h2>

设置的编码保留单位数等于可在给定帐户中同时处理的媒体任务数。例如，如果你的帐户具有 5 个保留单元，则只要有任务要处理，就可以同时运行 5 个媒体任务。其余任务将排队等待，运行的任务完成后才选择它们以按顺序进行处理。如果帐户未设置任何保留单元，则按顺序选择任务进行处理。在这种情况下，完成一个任务和开始下一个任务之间的等待时间将取决于系统中资源的可用性。

若要更改编码保留单位数，请执行以下操作：

1. 在[管理门户](https://manage.windowsazure.cn/)中单击"Media Services"。然后，单击 Media Services 的名称。

2. 选择"编码"页。 
	
	通过"编码"页可以在三种不同类型的编码保留单位之间进行选择：基本、标准和高级（如下所示）。

	![Processors page](./media/media-services-how-to-scale/media-services-encoding-scale.png) 

	可以使用"编码"滑块来更改所选"保留单位类型"的保留单位数。  
	
	保留单位类型之间的主要区别在于速度。例如，与"基本"保留单位类型相比，使用"标准"类型时，相同的编码作业运行速度更快。有关详细信息，请参阅 [Milan Gada](http://azure.microsoft.com/blog/author/milanga/) 撰写的"编码保留单位类型"。

	>[WACOM.Note] 以下数据中心不提供高级保留单位类型：新加坡、香港、大阪、北京、上海。

3. 按"保存"按钮保存更改。

	按"保存"后，会立即分配新的编码保留单位。

	>[WACOM.Note] 为 24 小时期间指定的最大单位数将用于计算成本。

<h2>创建支持票证</h2>


默认情况下，每个 Media Services 帐户最多可缩放到 25 个编码保留单元和 5 个点播流保留单元。你可以通过创建支持票证申请更高的限制值。

若要开具支持票证，请执行以下操作： 

1. 在[管理门户](http://manage.windowsazure.cn)中登录到你的 Azure 帐户。
2. 转到[支持](http://www.windowsazure.cn/zh-cn/support/contact/)。
3. 单击"获取支持"。
4. 选择你的订阅。
5. 在支持类型下选择"技术"。
6. 单击"创建票证"。
7. 在下一页显示的产品列表中选择"Azure Media Services"。
8. 选择"媒体处理"作为"问题类型"，然后在类别下面选择"保留单元"。
9. 单击"继续"。
10. 根据下一页上的说明操作，然后输入有关所需编码保留单元数或点播流保留单元数的详细信息。
11. 单击"提交"以创建该票证。





 




