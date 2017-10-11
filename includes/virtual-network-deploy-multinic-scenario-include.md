## <a name="scenario"></a>方案
本文档将指导，在 Vm 中在特定方案中使用多个 Nic 的部署。 在此方案中，必须在 Azure 中托管的两层 IaaS 工作负荷。 每个层中其自己的子网中的虚拟网络 (VNet) 部署。 前端层组成几个 web 服务器，以实现高可用性设置负载平衡器中组合在一起。 后端层组成多台数据库服务器。 将具有两个 Nic，每一个来进行数据库访问，另一个用于管理部署这些数据库服务器。 方案还包括网络安全组 (Nsg) 若要控制哪些流量允许到每个子网和 NIC，在部署中。 下图显示了此方案的基本体系结构。  

![MultiNIC 方案](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

