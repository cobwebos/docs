<!--author=alkohli last changed: 01/12/17-->

### <a name="to-take-a-backup"></a>执行备份

1. 转到 StorSimple Device Manager 服务。 从设备的表格式列表中，选择并单击你的设备，然后单击“所有设置”。 在“设置”边栏选项卡中，转到“设置”>“管理”>“备份策略”。

    ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu1.png)

2. 在“备份策略”边栏选项卡中，单击“+ 添加策略”。

    ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu2.png)

3. 在“创建备份策略”边栏选项卡中，为备份策略提供一个包含 3 到 150 个字符的名称。

4. 选择要备份的卷。 如果选择了多个卷，这些卷将组合在一起，创建在崩溃时保持一致的备份。

    ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu4.png)

5. 在“添加第一个计划”边栏选项卡上：

    1. 选择备份类型。 要用于快速还原，请选择“本地”快照。 要用于数据复原，请选择“云”快照。
    2. 指定备份频率，以分钟、小时、天或周为单位。
    3. 计划保留时间。 保留选择取决于备份频率。 例如，对于每日策略，以周为单位指定保留；对于每月策略，则以月为单位指定。
    4. 选择备份策略的开始时间和日期。
    5. 单击“确定”以创建备份策略。

        ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu5.png) 

6. 单击“创建”以开始创建备份策略。 成功创建备份策略后，你将收到通知。 备份策略的列表也将更新。
      
      ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu9.png)
      
      现在，你已有了一个创建卷数据的计划备份的备份策略。




