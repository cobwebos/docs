
<!--author=alkohli last changed: 01/20/2017-->

#### 创建手动备份
<a id="to-create-a-manual-backup" class="xliff"></a>

1. 转到 StorSimple Device Manager 服务，然后单击“设备”。 从设备的表格式列表中，选择你的设备。 转到“设置”>“管理”>“备份策略”。

2. “备份策略”边栏选项卡会以表格形式列出所有备份策略，包括要备份的卷的策略。 选择与你要备份的卷关联的策略，然后单击右键以调用上下菜单。 从下拉列表中，选择“立即备份”。

    ![创建手动备份](./media/storsimple-8000-create-manual-backup/createmanualbu1.png)

3. 在“立即备份”边栏选项卡中，执行以下步骤：

    1. 从下拉列表中选择合适的**快照类型**：“本地”快照或“云”快照。 要用于快速备份或还原，请选择本地快照，要用于数据复原，请选择云快照。

        ![创建手动备份](./media/storsimple-8000-create-manual-backup/createmanualbu2.png)

    2. 单击“确定”以启动作业来创建快照。 成功创建作业后，你会在页面底部看到一条通知。

        ![创建手动备份](./media/storsimple-8000-create-manual-backup/createmanualbu4.png)

    3. 若要监视作业，请单击该通知。 这会转至“作业”边栏选项卡，可以在其中查看作业进度。


5. 备份作业完成后，请转到“备份目录”  选项卡。

6. 将筛选器选择设置为相应的设备、备份策略和时间范围。 备份应显示在目录中所示的备份集列表中。

