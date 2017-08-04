#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>在云设备上创建公共终结点

1. 登录到 Azure 门户。
2. 转到“虚拟机”，然后选择并单击正用作云设备的虚拟机。
    
3. 你需要创建一个网络安全组 (NSG) 规则来控制虚拟机内部和外部的通信流。 执行以下步骤来创建 NSG 规则。
    1. 选择“网络安全组”。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. 单击所显示的网络安全组。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. 选择“入站安全规则”。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. 单击“+ 添加”以创建入站安全规则。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        在“添加入站安全规则”边栏选项卡中：

        1. 对于“名称”，为终结点键入以下名称：WinRMHttps。
        
        2. 对于“优先级”，选择一个小于 1000（这是默认规则的优先级）的数字。 数字越大，优先级越低。

        3. 将“源”设置为“任何”。

        4. 对于“服务”，选择“WinRM”。 “协议”自动设置为“TCP”，“端口范围”设置为“5986”。

        5. 单击“确定”创建规则。

            ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. 最后一步是将网络安全组与子网或特定网络接口相关联。 执行以下步骤来将网络安全组与某个子网相关联。
    1. 转到“子网”。
    2. 单击“+ 关联”。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. 选择你的虚拟网络，然后选择相应的子网。
    4. 单击“确定”创建规则。

        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

创建规则后，可查看其详细信息来确定公用虚拟 IP (VIP) 地址。 记下此地址。


