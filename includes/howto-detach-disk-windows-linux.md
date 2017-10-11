当你不再需要数据磁盘附加到虚拟机时，你可以轻松地分离它。 分离磁盘从虚拟机中，将删除的磁盘，但不会删除该磁盘的 Azure 存储帐户。

如果你想要再次使用磁盘上的现有数据，可以将其重新附加到相同的虚拟机或另一个。  

> [!NOTE]
> 若要分离操作系统磁盘，首先需要删除虚拟机。
>

## <a name="find-the-disk"></a>找到磁盘
如果你不知道磁盘名称，或者想要在分离之前验证，请按照下列步骤。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 单击**虚拟机**，然后选择适当的 VM。

3. 单击**磁盘**沿左边缘的虚拟机仪表板下**设置**。

 虚拟机仪表板列出的名称和所有附加的磁盘类型。 例如，此屏幕显示具有一个操作系统 (OS) 磁盘和一个数据磁盘的虚拟机：

    ![找到数据磁盘](./media/howto-detach-disk-windows-linux/vmwithdisklist.png)

## <a name="detach-the-disk"></a>分离磁盘
1. 从 Azure 门户中，单击**虚拟机**，然后单击具有要分离数据磁盘的虚拟机的名称。

2. 单击**磁盘**沿左边缘的虚拟机仪表板下**设置**。

3. 单击你想要分离的磁盘。

  ![标识要分离的磁盘](./media/howto-detach-disk-windows-linux/disklist.png)

4. 从命令栏中，单击**分离**。

  ![找到的分离命令](./media/howto-detach-disk-windows-linux/diskdetachcommand.png)

5. 在确认窗口中，单击**是**分离该磁盘。

  ![确认分离磁盘](./media/howto-detach-disk-windows-linux/confirmdetach.png)

磁盘保留在存储，但不再附加到虚拟机。
