## <a name="scenario"></a>方案
本文档将指导，使用静态公共 IP 地址分配给虚拟机 (VM) 的部署。 在此方案中，你具有其自己的静态公用 IP 地址的单个 VM。 VM 是名为的子网的一部分**前端**并且还具有静态专用 IP 地址 (**192.168.1.101**) 该子网中。

你可能需要在其中的 SSL 证书链接到的 IP 地址的 SSL 连接的 web 服务器需要静态 IP 地址。 

![映像说明](./media/virtual-network-deploy-static-pip-scenario-include/figure1.png)

你可以按照以下步骤来部署在上图中所述的环境。

