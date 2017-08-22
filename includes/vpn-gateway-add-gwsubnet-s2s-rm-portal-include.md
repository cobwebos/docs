1. 在门户中，导航到要为其创建虚拟网关的虚拟网络。
2. 在 VNet 页的“设置”部分单击“子网”，展开“子网”页。
3. 在“子网”页中，单击顶部的“+网关子网”打开“添加子网”页。

  ![添加网关子网](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/add-gw-subnet.png "添加网关子网")
4. 子网的“名称”自动填充为值“GatewaySubnet”。 Azure 需要 GatewaySubnet 值才能识别作为网关子网的子网。 调整自动填充的**地址范围**值，使其匹配配置要求。

  ![添加网关子网](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/gwsubnetip.png "添加网关子网")
5. 若要创建子网，请单击页底部的“确定”。