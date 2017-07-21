<!--author=alkohli last changed: 01/13/17-->

如果卷容器具有关联的卷，请首先使那些卷脱机。 请按照[使卷脱机](../articles/storsimple/storsimple-manage-volumes.md#take-a-volume-offline)中的步骤操作。 卷处于脱机状态后，可以删除它们。 当卷容器没有关联的卷时，删除卷容器。 可以执行以下过程来删除卷容器。

#### 删除卷容器
<a id="to-delete-a-volume-container" class="xliff"></a>
1. 转到 StorSimple Device Manager 服务并单击“设备”。 选择并单击“设备”，然后转到“设置”>“管理”>“卷容器”。

    ![“卷容器”边栏选项卡](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. 从卷容器的表格式列表中，选择要删除的卷容器，右键单击“...”，然后选择“删除”。

    ![删除卷容器](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. 如果卷容器没有关联的卷，可以删除它。 当提示进行确认时，查看并选中陈述了删除卷容器带来的影响的复选框。 单击“删除”以删除卷容器。

    ![确认删除](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

卷容器的列表将更新以反映删除的卷容器。

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


