1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在左上角开始，请单击**新建 > 计算 > Windows Server 2016 Datacenter**。

    ![导航到门户中的 Azure VM 映像](./media/virtual-machines-common-portal-create-fqdn/marketplace-new.png)

3. 在 Windows Server 2016 数据中心中，选择经典部署模型。 单击创建。

    ![显示在门户中可用的 Azure VM 映像的屏幕截图](./media/virtual-machines-common-portal-create-fqdn/deployment-classic-model.png)

## <a name="1-basics-blade"></a>1.基础知识边栏选项卡

基础知识边栏选项卡请求虚拟机的管理的信息。

1. 输入**名称**为虚拟机。 在示例中， _HeroVM_是虚拟机的名称。 名称必须是 1-15 个字符之间并且不能包含特殊字符。

2. 输入**用户名**以及强**密码**，用于在 VM 上创建本地帐户。 本地帐户用于登录到和管理 VM。 在示例中， _azureuser_是用户名称。

 密码必须是 8-123 个字符且满足个四个以下复杂性要求三个： 一个小写字母字符、 一个大写字符、 一个数字和一个特殊字符。 查看更多[用户名和密码要求](../articles/virtual-machines/windows/faq.md)。

3. **订阅**是可选的。 一个常用的设置是"即付即用"。

4. 选择现有**资源组**或键入一个新的名称。 在示例中， _HeroVMRG_是资源组的名称。

5. 选择 Azure 数据中心**位置**其中你想要将 VM 运行。 在示例中，**美国东部**是的位置。

6. 完成后，单击**下一步**继续到下一步的边栏选项卡。

    ![配置 Azure VM 基础知识边栏选项卡显示的设置的屏幕截图](./media/virtual-machines-common-portal-create-fqdn/basics-blade-classic.png)

## <a name="2-size-blade"></a>2.大小边栏选项卡

大小边栏选项卡标识 VM 上，配置详细信息，并列出了各种选项，包括操作系统、 处理器数量、 磁盘存储类型和估计每月使用费用。  

选择 VM 大小，然后单击**选择**以继续。 在此示例中， _DS1_\__V2 标准_将 VM 大小。

  ![显示 Azure VM 大小，你可以选择的大小边栏选项卡的屏幕截图](./media/virtual-machines-common-portal-create-fqdn/vm-size-classic.png)


## <a name="3-settings-blade"></a>3.设置边栏选项卡

设置边栏选项卡请求存储和网络的选项。 你可以接受默认设置。 在必要时，azure 将创建相应的条目。

如果你选择了支持它的虚拟机大小，你可以通过选择磁盘类型中的高级 (SSD) 尝试 Azure 高级存储。

完成更改后，单击**确定**。

## <a name="4-summary-blade"></a>4.摘要边栏选项卡

摘要边栏选项卡列出了在以前的边栏选项卡中指定的设置。 单击**确定**当你准备好使图像。

 ![摘要边栏选项卡报表提供指定虚拟机的设置](./media/virtual-machines-common-portal-create-fqdn/summary-blade-classic.png)

创建虚拟机后，门户将列出这些出新虚拟机**的所有资源**，并在仪表板上显示的虚拟机的磁贴。 相应的云服务和存储帐户还创建，并列出。 同时在虚拟机和云服务都会自动启动，其状态显示为**运行**。

 ![配置 VM 代理和虚拟机的终结点](./media/virtual-machines-common-portal-create-fqdn/portal-with-new-vm.png)
