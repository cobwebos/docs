## <a name="scenario"></a>方案
若要更好地说明如何创建 VNet 和子网，本文档将使用以下方案。

![VNet 方案](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

在此方案中，你将创建名为 VNet **TestVNet**使用保留 CIDR 块的**192.168.0.0./16**。 你的 VNet 将包含以下子网： 

* **前端**，使用**192.168.1.0/24**作为其 CIDR 块。
* **后端**，使用**192.168.2.0/24**作为其 CIDR 块。

