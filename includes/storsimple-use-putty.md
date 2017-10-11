<!--author=SharS last changed: 9/17/15-->

#### <a name="to-connect-through-the-serial-console"></a>通过串行控制台进行连接
1. 连接到设备的串行电缆连接 （直接或通过 USB 串行适配器）。
2. 打开**控制面板**，然后打开**设备管理器**。
3. 下图中所示标识的 COM 端口。
   
     ![通过串行控制台进行连接](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)
4. 启动 PuTTY。 
5. 在右窗格中，更改**连接类型**到**串行**。
6. 在右窗格中，键入相应的 COM 端口。 请确保串行配置参数设置如下：
   
   * 速度： 115200
   * 数据位： 8
   * 停止位： 1
   * 奇偶校验： 无
   * 流控制： 无
     
     这些设置下图所示。
     
     ![PuTTY 设置](./media/storsimple-use-putty/HCS_PuttyConfig-include.png) 
     
     > [!NOTE]
     > 如果默认的流程控制设置不起作用，请尝试将流程控制设置到 XON/XOFF。
     > 
     > 
7. 单击**打开**启动串行会话。

