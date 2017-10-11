## <a name="sample-scenario"></a>示例方案
为了更好地说明如何管理 Nsg，本文章将使用以下方案。

![VNet 方案](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

在此方案中，你将创建中每个子网的 NSG **TestVNet**虚拟网络，如下所示： 

* **Nsg-frontend**。 前端 NSG 将应用于*前端*子网，并且包含两个规则：    
  * **rdp 规则**。 此规则将允许到 RDP 流量*前端*子网。
  * **web 规则**。 此规则将允许到的 HTTP 流量*前端*子网。
* **Nsg-backend**。 后端 NSG 将应用于*后端*子网，并且包含两个规则：    
  * **sql 规则**。 此规则允许 SQL 流量只能从*前端*子网。
  * **web 规则**。 此规则拒绝所有 internet 都绑定流量从*后端*子网。

这些规则组合起来创建 DMZ 类似的方案，其中后端子网只能接收来自前端子网的 SQL 通信的传入流量和具有到 Internet，没有访问权限，而前端子网可以与 Internet 通信并接收传入 HTTP 请求。

若要部署上面所述的方案，请按照[此链接](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG)，单击**部署到 Azure**，替换默认参数值，如有必要，然后按照门户中的说明。 下面的示例说明中使用了该模板来部署资源组名称**RG NSG**。 

