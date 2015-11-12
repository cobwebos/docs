<properties 
	pageTitle="如何为高级 Azure Redis 缓存配置数据暂留" 
	description="了解如何为高级级别的 Azure Redis 缓存实例配置和管理数据暂留" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="cache"
	ms.date="10/01/2015"
	wacn.date=""/>

# 如何为高级 Azure Redis 缓存配置数据暂留

Azure Redis 缓存具有不同的缓存产品（包括新推出的高级级别，目前为预览版），使缓存大小和功能的选择更加灵活。

Azure Redis 缓存高级级别包括群集、暂留和虚拟网络支持。本文介绍如何配置高级 Azure Redis 缓存实例中的暂留。

有关其他高级缓存功能的信息，请参阅[如何配置高级 Azure Redis 缓存的群集功能](/documentation/articles/cache-how-to-premium-clustering)和[如何配置高级 Azure Redis 缓存的虚拟网络支持](/documentation/articles/cache-how-to-premium-vnet)。

>[AZURE.NOTE]Azure Redis 缓存高级级别目前处于预览状态。在预览期间，暂留不能与群集功能或虚拟网络一起使用。

## 什么是数据暂留？
Redis 暂留可让你保留存储在 Redis 中的数据。你还可获取快照并备份数据，以便在出现硬件故障时进行加载。这相对于基本级别或标准级别是一项巨大优势，因为基本级别或标准级别将所有数据存储在内存中，在出现故障的情况下，如果缓存节点停机，则可能导致数据丢失。

Azure Redis 缓存提供的 Redis 暂留功能允许将数据存储在 Azure 存储帐户中。公共预览版支持 [RDB 模型](http://redis.io/topics/persistence)，并且很快就会支持 [AOF](http://redis.io/topics/persistence)。

## 数据暂留
配置暂留以后，Azure Redis 缓存会按照可配置的备份频率，将 Redis 缓存的快照以 Redis 二进制格式暂留在磁盘上。如果发生了灾难性事件，导致主缓存和副缓存都无法使用，则会使用最新快照重新构造缓存。

暂留可在创建缓存期间在“新建 Redis 缓存”边栏选项卡中配置。若要创建缓存，首先请登录到 [Azure 预览门户](https://manage.windowsazure.cn)，然后单击“新建”>“数据服务”>“Redis 缓存”。

![创建 Redis 缓存][redis-cache-new-cache-menu]

若要配置暂留，请首先在“选择定价层”边栏选项卡中选择一个“高级”缓存。

![选择你的定价层][redis-cache-premium-pricing-tier]

一旦选中某个高级定价层，则请单击“Redis 暂留”。

![Redis 暂留][redis-cache-persistence]

单击“启用”以启用 RDB（Redis 数据库）备份。

从下拉列表中选择“备份频率”。选项包括“60 分钟”、“6 小时”、“12 小时”和“24 小时”。在上一个备份操作成功完成以后，此时间间隔就会开始倒计时，同时会启动新的备份。

单击“存储帐户”选择要使用的存储帐户。“存储密钥”会自动填充，但如果该密钥是为存储帐户重新生成的，则可在此处对其进行更新。建议使用“高级存储”帐户，因为高级存储的吞吐量更大。

>[AZURE.NOTE]AOF 在高级级别预览期间不可用，但缓存团队会争取尽快增加此功能。有关 RDB 和 AOF 以及各自优势的详细信息，请参阅 [Redis 暂留](http://redis.io/topics/persistence)。

![Redis 暂留][redis-cache-persistence-selected]

单击“确定”可保存暂留配置。

创建完缓存后，一旦备份频率间隔时间已过，则会启动第一次备份。

## 保留常见问题

以下列表包含有关 Azure Redis 缓存保留常见问题的解答。

## 能否在此前已创建的缓存的基础上启用保留？

在预览期间，你只能在高级缓存的创建过程中启用和配置保留。在公共预览期间，不允许从基本/标准版扩展到高级版，但很快就会允许。

## 能否在创建缓存后更改备份频率？

在预览期间，你只能在缓存创建过程中配置保留。若要更改保留配置，请先删除缓存，然后使用所需保留配置创建新的缓存。这是预览版存在的限制，很快会推出相应的支持。

## 为何我的备份频率为 60 分钟，而两次备份的间隔却超过 60 分钟？

在上一次备份过程成功完成之前，本次备份不会开始，其频率所对应的时间间隔也不会开始计算。如果备份频率为 60 分钟，而备份过程需要 15 分钟才能成功完成，则在上一次备份开始以后，要再过 75 分钟才会开始下一次备份。

## 进行新备份以后，旧备份会发生什么情况

除最新备份外的所有备份都会自动删除。这种删除可能不会即刻发生，但旧备份是不会无限期保留下去的。

## 后续步骤
了解如何使用更多的高级版缓存功能。

-	[如何为高级 Azure Redis 缓存配置群集功能](/documentation/articles/cache-how-to-premium-clustering)
-	[如何为高级 Azure Redis 缓存配置虚拟网络支持](/documentation/articles/cache-how-to-premium-vnet)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

<!---HONumber=79-->