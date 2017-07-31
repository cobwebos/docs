<!--author=alkohli last changed: 02/10/17-->

#### <a name="to-add-a-storsimple-backup-policy"></a>添加 StorSimple 备份策略

1. 转到 StorSimple 设备并单击“备份策略”。

2. 在“备份策略”边栏选项卡中，从命令栏中单击“+ 添加策略”。
   
    ![添加备份策略](./media/storsimple-8000-add-backup-policy-u2/addbupol1.png)

3. 在“创建备份策略”边栏选项卡中，执行以下步骤：
   
   1. “选择设备”会根据你选择的设备自动填充。
   
   2. 指定备份**策略名称**，其中包含 3 到 150 个字符。 创建策略后，无法重命名该策略。
       
   3. 要向此备份策略分配卷，请选择“添加卷”，然后在卷的表格式列表中，单击相应的复选框来将一个或多个卷分配到此备份策略。

       ![添加备份策略](./media/storsimple-8000-add-backup-policy-u2/addbupol2.png)

   4. 要为此备份策略定义计划，请单击“第一个计划”，并修改以下参数：

       ![添加备份策略](./media/storsimple-8000-add-backup-policy-u2/addbupol3.png)

       1. 从“快照类型”中，选择“云”或“本地”。

       2. 指示备份频率（指定一个数字，并从下拉列表中选择“天”或“周”。

       3. 输入保留计划。

       4. 输入备份策略开始的时间和日期。

       5. 单击“确定”来定义计划。

   5. 单击“创建”来创建备份策略。

       ![添加备份策略](./media/storsimple-8000-add-backup-policy-u2/addbupol4.png)
   
   6. 在备份策略创建后，将收到通知。 新添加的策略会显示在“备份策略”边栏选项卡上的表格式视图中。

       ![添加备份策略](./media/storsimple-8000-add-backup-policy-u2/addbupol7.png)

