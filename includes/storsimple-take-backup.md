<!--author=alkohli last changed: 9/17/15-->

### <a name="to-take-a-backup"></a>执行备份
1. 在设备“快速启动”页上，单击“添加备份策略”。 这会启动“添加备份策略向导”。 
2. 在“定义备份策略”  页上：
   
   1. 为备份策略提供名称，包含 3 到 150 个字符。
   2. 选择要备份的卷。 如果选择多个卷，这些卷将组合在一起，创建在崩溃时保持一致的备份。
   3. 单击箭头图标  ![arrow-icon](./media/storsimple-take-backup/HCS_ArrowIcon-include.png)。 
      
      ![Add-backup-policy](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard1M-include.png)
3. 在“定义计划”  页上：
   
   1. 从下拉列表中选择备份类型。 对于更快恢复，请选择“本地快照” 。 对于数据弹性，请选择“云快照” 。
   2. 指定备份频率，以分钟、小时、天或周为单位。
   3. 计划保留时间。 保留选择取决于备份频率。 例如，对于每日策略，以周为单位指定保留；对于每月策略，则以月为单位指定。
   4. 选择备份策略的开始时间和日期。
   5. 选择“启用”  复选框启用备份策略。 
   6. 单击选中图标  ![check-icon](./media/storsimple-take-backup/HCS_CheckIcon-include.png) 保存策略。
      
      ![Add-backup-policy](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard2M-include.png)
      
      现有备份策略将创建卷数据的计划备份。

设备配置已完成。 

![可用视频](./media/storsimple-take-backup/Video_icon.png) **可用视频**

若要观看如何执行 StorSimple 备份的演示视频，请单击 [此处](https://azure.microsoft.com/documentation/videos/take-a-storsimple-backup/)。

