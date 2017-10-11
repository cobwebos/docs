## <a name="scenario"></a>方案
若要更好地说明如何创建 Udr，本文档将使用以下方案。

![映像说明](./media/virtual-network-create-udr-scenario-include/figure1.png)

在此方案中，你将创建为一个 UDR*前端子网*和有关的另一个 UDR*后端子网*，如下所述： 

* **Udr-frontend**。 前端 UDR 将应用于*前端*子网，并且包含一个路由：    
  * **RouteToBackend**。 此路由将所有流量都发送到后端子网到**FW1**虚拟机。
* **Udr-backend**。 后端 UDR 将应用于*后端*子网，并且包含一个路由：    
  * **RouteToFrontend**。 此路由将所有流量都发送到前端子网到**FW1**虚拟机。

这些路由的组合将确保从一个子网到另一个目标的所有流量将都路由到**FW1**正在用作虚拟设备的虚拟机。 你还需要打开该 VM，以确保它可以接收流量定向到其他 Vm 的 IP 转发。

