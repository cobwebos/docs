<!--author=alkohli last changed: 06/22/17-->

#### 创建卷容器
<a id="to-create-a-volume-container" class="xliff"></a>
1. 转到 StorSimple Device Manager 服务并单击“设备”。 从设备的表格式列表中，选择并单击某个设备。 

    ![卷容器边栏选项卡](./media/storsimple-8000-create-volume-container/createvolumecontainer1.png)

2. 在设备仪表板中，单击“+ 添加卷容器”。

    ![卷容器边栏选项卡](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

3. 在“添加卷容器”边栏选项卡中：
   
   1. 将自动选择设备。
   2. 为你的卷容器提供“名称”  。 名称长度必须为 3 到 32 个字符。
   3. 选择“启用云存储加密”  启用从设备发送到云中的数据加密。
   4. 提供并确认“云存储加密密钥”  ，长度为 8 到 32 个字符。 设备使用此密钥访问加密的数据。
   5. 选择“存储帐户”  与此卷容器相关联。 可以选择现有存储帐户或选择在创建服务时生成的默认帐户。 还可以使用“新增”  选项指定未链接到此服务订阅的存储帐户。
   6. 如果想要使用所有可用带宽，请在“指定带宽”下拉列表中选择“无限制”。 还可以将此选项设置为“自定义”以进行带宽控制，并指定一个介于 1 Mbps 和 1000 Mbps 之间的值。
      如果有可用的带宽使用情况信息，可以根据计划通过指定“选择带宽模板” 分配带宽。 有关分步过程，请转到 [添加带宽模板](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template)。

      ![卷容器边栏选项卡](./media/storsimple-8000-create-volume-container/createvolumecontainer6b.png)
   7. 单击“创建” 。

        ![卷容器边栏选项卡](./media/storsimple-8000-create-volume-container/createvolumecontainer6.png)
   
       成功创建卷容器后，你会收到通知。

       ![卷容器创建通知](./media/storsimple-8000-create-volume-container/createvolumecontainer8.png)

   新创建的卷容器将在设备的卷容器列表中列出。

   ![“添加卷容器”边栏选项卡](./media/storsimple-8000-create-volume-container/createvolumecontainer9.png)


