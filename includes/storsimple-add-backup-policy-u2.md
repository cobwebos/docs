<!--author=v-sharos last changed: 11/06/15-->

#### <a name="to-add-a-storsimple-backup-policy"></a>添加 StorSimple 备份策略
1. 在设备“快速启动”页上，单击“备份策略”选项卡。 这将转到“备份策略”页。
2. 在页面底部，单击“添加”启动“添加备份策略”向导。
   
    ![添加备份策略 1](./media/storsimple-add-backup-policy-u2/AddBackupPolicy1.png)
3. 在“添加备份策略”对话框的“定义备份策略”下，执行以下操作：
   
   1. 指定备份策略名称，其中包含 3 到 150 个字符。
   2. 单击复选框，为该备份策略指定一个或多个卷。 请注意，不能选择使用不同云服务提供程序的卷。 如果基于第一个选择使用多个云服务提供程序，列表将显示仅属于该云服务提供程序的卷。 这将允许你对属于快照中单个云服务提供程序的卷进行分组。
   3. 单击箭头图标  ![箭头图标](./media/storsimple-add-backup-policy-u2/HCS_ArrowIcon-include.png) 转到下一页。
      
      ![添加备份策略 2](./media/storsimple-add-backup-policy-u2/AddBackupPolicy2.png)
4. 在“定义计划”下，执行以下操作：
   
   1. 在“备份类型”框中，从下拉列表中选择“云快照”或“本地快照”。
   2. 指示备份频率（指定一个数字，然后从下拉列表中选择“天”或“周”）。
   3. 输入保留计划。
   4. 输入备份策略开始的时间和日期。  
   5. 单击选中图标  ![选中图标](./media/storsimple-add-backup-policy-u2/HCS_CheckIcon-include.png) 保存策略。

新添加的策略将显示在“备份策略”页的表格视图中。



<!--HONumber=Jan17_HO4-->


