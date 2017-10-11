#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>若要在云设备上创建公共终结点

1. 登录到 Azure 门户。
2. 转到**虚拟机**，然后选择并单击用作你的云设备的虚拟机。
    
3. 你需要创建网络安全组 (NSG) 规则来控制入和移出虚拟机的通信流量。 执行以下步骤以创建 NSG 规则。
    1. 选择**网络安全组**。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. 单击显示的默认网络安全组。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. 选择**入站安全规则**。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. 单击**+ 添加**创建入站的安全规则。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        在添加入站的安全规则边栏选项卡：

        1. 有关**名称**，键入终结点的以下名称： WinRMHttps。
        
        2. 有关**优先级**，选择数字小于 1000年 （这是默认规则的优先级）。 更高值，降低优先级。

        3. 设置**源**到**任何**。

        4. 有关**服务**，选择**WinRM**。 **协议**自动设置为**TCP**和**端口范围**设置为**5986**。

        5. 单击**确定**创建规则。

            ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. 最后一步是将网络安全组与子网或特定网络接口相关联。 执行以下步骤以将你的网络安全组与子网相关联。
    1. 转到**子网**。
    2. 单击**+ 关联**。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. 选择你的虚拟网络，然后选择相应的子网。
    4. 单击**确定**创建规则。

        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

创建规则后，你可以查看其详细信息，以确定公共虚拟 IP (VIP) 地址。 记录此地址。


