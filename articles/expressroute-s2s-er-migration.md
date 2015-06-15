<properties pageTitle="将站点到站点 VPN 迁移到 ExpressRoute" description="本文帮助你将站点到站点 VPN 迁移到 ExpressRoute。" services="expressroute" documentationCenter="na" authors="cherylmc" manager="adinah" editor="tysonn"/>
   
<tags ms.service="expressroute" ms.date="02/23/2015" wacn.date=""/>
   
# 连接迁移：从站点到站点 VPN 到 ExpressRoute

Azure 客户可通过两种方式连接到其虚拟网络：站点到站点 VPN 和 ExpressRoute。站点到站点 VPN 连接通过公共 Internet 发生。IPsec 用于加密网络流量。ExpressRoute 是与 Azure 建立的专用连接。你可以通过 [Exchange 提供商 (EXP)](expressroute-configuring-exps) 或[网络服务提供商 (NSP)](expressroute-configuring-nsps) 连接到 Azure。

如果你已经与虚拟网络建立了站点到站点 VPN 连接，则可以按照以下步骤迁移此连接，以便通过 ExpressRoute 进行连接。

1) 登录到 Azure 门户

2) 在导航窗格中，单击“网络”，然后单击已建立 VPN 连接的虚拟网络

3) 单击“仪表板”。在页面底部，单击“删除网关”，然后单击“是”。

  此步骤将会删除站点到站点 VPN 网关。

4) 在门户针对虚拟网络显示“未创建网关”后，请单击“配置”页。

a) 单击“使用 ExpressRoute”复选框

b) 将网关子网的 CIDR 更改为 /28

![订阅共享](./media/expressroute-s2s-er-migration/expressroute-s2s-er.png)

5) 在页面底部，单击“保存”，然后单击“是”。

6) 单击“仪表板”。在页面底部，单击“创建网关”，然后单击“是”。

 此步骤将创建 ExpressRoute 网关
 
创建新网关后，虚拟网络便可以连接到 ExpressRoute 线路。在整个迁移过程中，虚拟网络中的所有虚拟机都将保持启动和运行。你不需要移动或关闭这些虚拟机。

转到“通过  ExpressRoute”教程的最后一步,[Exchange 提供商](expressroute-configuring-exps)/[网络服务提供商配置](expressroute-configuring-nsps)将虚拟网络链接到 ExpressRoute 线路。

<!---HONumber=60-->