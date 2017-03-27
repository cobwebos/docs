> 不能将 v1（经典）、Azure 虚拟网络与基于 Linux 的 HDInsight 配合使用。 虚拟网络必须是 v2 (Azure Resource Manager) 才能在 Azure 预览门户中的 HDInsight 群集创建过程中列为选项，或者在通过 Azure CLI 或 Azure PowerShell 创建群集时可用。
> 
> 如果在 v1 网络中包含资源，并且想要让 HDInsight 通过虚拟网络直接访问这些资源，请参阅[将经典 VNet 连接到新的 VNet](../articles/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)，获取有关如何将 v2 虚拟网络连接到 v1 虚拟网络的信息。 建立此连接后，便可以在 v2 虚拟网络中创建 HDInsight 群集。
> 
> 



<!--HONumber=Jan17_HO3-->


