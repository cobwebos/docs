
每个终结点具有*公用端口*和*专用端口*:

* Azure 负载均衡器使用公用端口侦听从 Internet 传入的虚拟机流量。
* 虚拟机使用专用端口侦听通常发送到应用程序或虚拟机上运行的服务的传入流量。

IP 协议和众所周知的网络协议时使用 Azure 门户创建终结点提供的 TCP 或 UDP 端口的默认值。 对于自定义终结点，你将需要指定正确的 IP 协议 （TCP 或 UDP） 以及公用和专用端口。 若要将传入流量随机分发到多个虚拟机，你将需要创建包含多个终结点的负载平衡集。

创建一个终结点后，你可以使用访问控制列表 (ACL) 来定义允许或拒绝基于源 IP 地址的终结点的公用端口的传入流量的规则。 但是，如果虚拟机在 Azure 虚拟网络，你应使用网络安全组。 有关详细信息，请参阅[关于网络安全组](../articles/virtual-network/virtual-networks-nsg.md)。

> [!NOTE]
> Azure 虚拟机的防火墙配置为使用 Azure 自动设置的远程连接终结点关联的端口会自动完成。 对于指定的端口的所有其他终结点，为虚拟机的防火墙自动不进行任何配置。 在创建虚拟机的终结点时，你将需要确保虚拟机的防火墙也允许的协议和专用端口对应的终结点配置的流量。 若要配置防火墙，请参阅文档或虚拟机上运行的操作系统的联机帮助。
>
>

## <a name="create-an-endpoint"></a>创建一个终结点
1. 如果你尚未这样做，登录到[Azure 门户](https://portal.azure.com)。
2. 单击**虚拟机**，然后单击你想要配置虚拟机的名称。
3. 单击**终结点**中**设置**组。 **终结点**页将列出的虚拟机的所有当前终结点。 （此示例是 Windows VM。 Linux VM 将默认显示终结点 ssh。）

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![终结点](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)

4. 在终结点条目上方的命令栏中，单击**添加**。
5. 上**添加终结点**页上，键入中的终结点的名称**名称**。
6. 在**协议**，请执行以下某**TCP**或**UDP**。
7. 在**公用端口**，键入从 Internet 传入流量的端口号。 在**专用端口**，键入虚拟机正在侦听的端口号。 这些端口号可以不同。 确保虚拟机上的防火墙配置为允许与协议 （在步骤 6） 和专用端口对应的流量。
10. 单击“确定” 。

新的终结点上会列出**终结点**页。

![成功创建终结点](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>管理终结点上的 ACL
若要定义的一组可以发送流量的计算机，终结点上的 ACL 可以限制基于源 IP 地址的流量。 请按照下列步骤以添加、 修改或删除终结点上的 ACL。

> [!NOTE]
> 如果终结点是负载平衡集的一部分，终结点上的 ACL 的任何更改都应用于集中的所有终结点。
>
>

如果虚拟机是 Azure 虚拟网络中，我们建议而不是 Acl 的网络安全组。 有关详细信息，请参阅[关于网络安全组](../articles/virtual-network/virtual-networks-nsg.md)。

1. 如果你尚未登录到 Azure 门户的这样做。
2. 单击**虚拟机**，然后单击你想要配置虚拟机的名称。
3. 单击**终结点**。 从列表中，选择相应的终结点。 ACL 列表位于页面底部。

   ![指定 ACL 详细信息](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. 用于行在列表中添加、 删除或编辑 ACL 规则和更改其顺序。 **远程子网**值是从 Azure 负载平衡器用于允许或拒绝基于源 IP 地址的流量 Internet 传入流量的 IP 地址范围。 请务必以 CIDR 格式，也称为地址前缀格式指定 IP 地址范围。 一个示例是`10.1.0.0/8`。

 ![新的 ACL 条目](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


可以使用规则为允许来自 Internet 上你的计算机所对应的特定计算机的唯一流量或拒绝流量从特定的已知地址范围。

各个规则的计算顺序，从第一条规则开始和结束与上个规则。 这意味着规则应从限制性最弱排序到最高限制。 有关示例和详细信息，请参阅[什么是网络访问控制列表](../articles/virtual-network/virtual-networks-acl.md)。
