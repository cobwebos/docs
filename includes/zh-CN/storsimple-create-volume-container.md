<!--author=SharS last changed: 9/17/15-->

#### <a name="to-create-a-volume-container"></a>创建卷容器
1. 在设备“快速启动”页中，单击“添加卷容器”。 这会显示“创建卷容器”对话框。
   
    ![创建卷容器](./media/storsimple-create-volume-container/HCS_CreateVolumeContainerM-include.png)
2. 在“创建卷容器”  对话框中：
   
   1. 为卷容器提供“名称”。 名称长度必须为 3 到 32 个字符。
   2. 选择“存储帐户”  与此卷容器相关联。 可选择在创建服务时生成的默认帐户。 还可以使用“新增”  选项指定未链接到此服务订阅的存储帐户。
   3. 选择“启用云存储加密”  启用从设备发送到云中的数据加密。
   4. 提供并确认“云存储加密密钥”  ，长度为 8 到 32 个字符。 设备使用此密钥访问加密的数据。
   5. 如果想要使用所有可用带宽，请在“指定带宽”下拉列表中选择“无限制”。 还可以将此选项设置为“自定义”以进行带宽控制，并指定一个 1 和 1000 Mbps 之间的值。 
      如果有可用的带宽使用情况信息，可以根据计划通过指定“选择带宽模板” 分配带宽。 有关分步过程，请转到 [添加带宽模板](../articles/storsimple/storsimple-manage-bandwidth-templates.md#add-a-bandwidth-template)。
   6. 单击选中图标  ![check-icon](./media/storsimple-create-volume-container/HCS_CheckIcon-include.png) 保存此卷容器并退出向导。 
   
   新创建的卷容器会在“卷容器”页上列出。

![可用视频](./media/storsimple-create-volume-container/Video_icon.png) **可用视频**

若要观看如何在 StorSimple 解决方案中创建卷容器的演示视频，请单击 [此处](https://azure.microsoft.com/documentation/videos/create-a-volume-container-in-your-storsimple-solution/)。

