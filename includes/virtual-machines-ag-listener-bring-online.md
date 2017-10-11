1. 在故障转移群集管理器中，展开**角色**，，然后突出显示您的可用性组。  

2. 上**资源**选项卡上，右键单击侦听器名称，然后单击**属性**。

3. 单击**依赖关系**选项卡。 如果未列出多个资源，请验证 IP 地址具有 OR、 not AND 依赖关系。  

4. 单击" **确定**"。

5. 右键单击侦听器名称，然后单击**联机**。

6. 侦听器处于联机状态，在后**资源**选项卡上，右键单击可用性组，然后单击**属性**。
   
    ![配置可用性组资源](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. 在侦听器名称资源 （不是 IP 地址资源名称），创建一个依赖项，然后单击**确定**。
   
    ![添加对侦听器名称的依赖项](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. 启动 SQL Server Management Studio，，然后连接到主副本。

9. 转到**AlwaysOn 高可用性** > **可用性组** > **\<AvailabilityGroupName\>** > **可用性组侦听器**。  
    应显示故障转移群集管理器中创建的侦听器名称。

10. 右键单击侦听器名称，然后单击**属性**。

11. 在**端口**框中，通过使用你之前用 $EndpointPort 为可用性组侦听器指定端口号 （在本教程中，1433年是默认值），然后单击**确定**。

