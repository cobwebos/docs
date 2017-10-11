<!--author=v-sharos last changed: 11/06/15-->

#### <a name="to-add-a-storsimple-backup-policy"></a>若要添加 StorSimple 备份策略
1. 在设备上**快速启动**页上，单击**备份策略**选项卡。 这将需要你**备份策略**页。
2. 在页面底部，单击**添加**以启动添加备份策略向导。
   
    ![添加备份策略 1](./media/storsimple-add-backup-policy-u2/AddBackupPolicy1.png)
3. 在**添加备份策略**对话框中，在**定义备份策略**，执行以下操作：
   
   1. 指定包含 3 到 150 个字符的备份策略名称。
   2. 单击复选框以将一个或多个卷分配到此备份策略。 请注意，不能选择使用不同云服务提供程序的卷。 如果你使用多个云服务提供商，基于你的第一个选择，该列表将显示属于只有该云服务提供商的卷。 这将允许你到属于单个云服务提供程序在快照中的组卷。
   3. 单击箭头图标 ![箭头图标](./media/storsimple-add-backup-policy-u2/HCS_ArrowIcon-include.png) 转到下一页。
      
      ![添加备份策略 2](./media/storsimple-add-backup-policy-u2/AddBackupPolicy2.png)
4. 下**定义计划**，执行以下操作：
   
   1. 在**类型的备份**框中，选择**云快照**或**本地快照**从下拉列表。
   2. 指示备份的频率 (指定一个数字，然后选择**天**或**周**从下拉列表)。
   3. 输入保留计划。
   4. 输入的时间和备份策略的开始日期。  
   5. 单击复选图标 ![选中图标](./media/storsimple-add-backup-policy-u2/HCS_CheckIcon-include.png) 若要保存策略。

新添加的策略将显示在表格的视图上**备份策略**页。

