可用性组侦听器是 SQL Server 可用性组侦听的 IP 地址和网络名称。 若要创建可用性组侦听器，请执行以下操作：

1. <a name="getnet"></a>获取群集网络资源的名称。

    a. 使用 RDP 连接到承载主副本的 Azure 虚拟机。 

    b。 打开故障转移群集管理器。

    c. 选择**网络**节点，并记下群集网络名称。 使用此名称在`$ClusterNetworkName`变量中的 PowerShell 脚本。 在下图中的群集网络名称是**群集网络 1**:

   ![群集网络名称](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

2. <a name="addcap"></a>添加客户端访问点。  
    客户端访问点是应用程序用于连接到可用性组中的数据库的网络名称。 在故障转移群集管理器中创建的客户端访问点。

    a. 展开群集名称，然后单击**角色**。

    b。 在**角色**窗格中，右键单击可用性组名称，然后选择**添加资源** > **客户端访问点**。

   ![客户端访问点](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. 在**名称**框中，创建此新的侦听器的名称。 
   为新的侦听器名称是应用程序用于连接到 SQL Server 可用性组中数据库的网络名称。
   
    d. 若要完成创建侦听器，请单击**下一步**两次，然后单击**完成**。 不要在此时使侦听器或资源联机。

3. <a name="congroup"></a>配置可用性组的 IP 资源。

    a. 单击**资源**选项卡上，然后展开你创建的客户端访问点。  
    客户端访问点处于脱机状态。

   ![客户端访问点](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b。 右键单击 IP 资源，然后单击属性。 记下的名称的 IP 地址，并使用它在`$IPResourceName`变量中的 PowerShell 脚本。

    c. 下**IP 地址**，单击**静态 IP 地址**。 设置为使用 Azure 门户上设置负载平衡器地址时的相同地址的 IP 地址。

   ![IP 资源](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

4. <a name = "dependencyGroup"></a>使 SQL Server 可用性组资源依赖于客户端访问点。

    a. 在故障转移群集管理器中，单击**角色**，然后单击你的可用性组。

    b。 上**资源**选项卡上，在**信息的其他资源**，可用性的资源组中，右键单击，然后单击**属性**。 

    c. 在依赖关系选项卡上，添加客户端访问点 （侦听器） 资源的名称。

   ![IP 资源](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. 单击" **确定**"。

5. <a name="listname"></a>生成依赖于 IP 地址的客户端访问点资源。

    a. 在故障转移群集管理器中，单击**角色**，然后单击你的可用性组。 

    b。 上**资源**选项卡上，右键单击下的客户端访问点资源**服务器名称**，然后单击**属性**。 

   ![IP 资源](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. 单击**依赖关系**选项卡。 验证 IP 地址是依赖项。 如果不是这样，设置依赖项的 IP 地址。 如果有多个资源列出，请验证 IP 地址具有 OR、 not AND 依赖关系。 单击" **确定**"。 

   ![IP 资源](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    d. 右键单击侦听器名称，然后单击**联机**。 

    >[!TIP]
    >你可以验证正确配置了依赖关系。 在故障转移群集管理器中，转到角色中，右键单击可用性组，单击**更多操作**，然后单击**显示依赖关系报告**。 正确配置依赖关系，可用性组依赖于网络名称，且网络名称是依赖于 IP 地址。 


6. <a name="setparam"></a>在 PowerShell 中设置的群集参数。
    
    a. 将以下 PowerShell 脚本复制到一个 SQL Server 实例。 更新你的环境的变量。     
    
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>
    
    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

    b。 通过在某个群集节点上运行 PowerShell 脚本设置群集参数。  

    > [!NOTE]
    > 如果您的 SQL Server 实例位于不同的区域中，你需要两次运行 PowerShell 脚本。 这是首次使用`$ILBIP`和`$ProbePort`从第一个区域。 第二次使用`$ILBIP`和`$ProbePort`第二个区域中。 群集网络名称和群集 IP 资源名称是相同的。 
