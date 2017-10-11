


## <a name="attach-an-empty-disk"></a>附加空磁盘
附加空磁盘是一种简单的方法添加数据磁盘，因为 Azure 可为你创建.vhd 文件并将其存储在存储帐户。

1. 单击**虚拟机 （经典）**，然后选择适当的 VM。

2. 在设置菜单中，单击**磁盘**。

   ![附加新的空磁盘](./media/howto-attach-disk-windows-linux/menudisksattachnew.png)

3. 在命令栏中，单击**附加新**。  
    **附加新磁盘**对话框随即出现。

    ![附加新磁盘](./media/howto-attach-disk-windows-linux/newdiskdetail.png)

    填写以下信息：
    - 在**文件名**，接受默认名称或键入另一个.vhd 文件。 数据磁盘使用自动生成的名称，即使你键入的.vhd 文件的另一个名称。
    - 选择**类型**的数据磁盘。 所有虚拟机都支持标准的磁盘。 多个虚拟机还支持高级磁盘。
    - 选择**大小 (GB)**的数据磁盘。
    - 有关**主机缓存**、 选择无或只读模式。
    - 单击确定以完成。

4. 创建并附加数据磁盘后，它在 VM 的磁盘部分中列出。

   ![已成功附加新的空数据磁盘](./media/howto-attach-disk-windows-linux/newdiskemptysuccessful.png)

> [!NOTE]
> 添加数据磁盘后，你需要登录到 VM 并初始化磁盘，以便可以使用它。

## <a name="how-to-attach-an-existing-disk"></a>如何： 附加现有磁盘
附加现有磁盘需要具有存储帐户中的可用的.vhd。 使用[Add-azurevhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) cmdlet 将.vhd 文件上载到存储帐户。 你已经创建并上载.vhd 文件后，你可以将其附加到 VM。

1. 单击**虚拟机 （经典）**，然后选择相应的虚拟机。

2. 在设置菜单中，单击**磁盘**。

3. 在命令栏中，单击**附加现有**。

    ![附加数据磁盘](./media/howto-attach-disk-windows-linux/menudisksattachexisting.png)

4. 单击**位置**。 显示可用的存储帐户。 接下来，选择适当的存储帐户的中列出。

    ![提供磁盘存储帐户](./media/howto-attach-disk-windows-linux/existdiskstorageaccounts.png)

5. A**存储帐户**保存包含硬盘 (vhd) 的一个或多个容器。 从所列中选择适当的容器。

    ![提供虚拟机 windows 的容器](./media/howto-attach-disk-windows-linux/existdiskcontainers.png)

6. **Vhd**面板列出保存在容器中的磁盘驱动器。 单击其中一个磁盘，，然后单击选择。

    ![为虚拟机 windows 提供磁盘映像](./media/howto-attach-disk-windows-linux/existdiskvhds.png)

7. **附加现有磁盘**面板显示同样，替换为包含存储帐户、 容器和选定的硬盘 (vhd) 将添加到虚拟机的位置。

  设置**主机缓存**为 none 或读取仅，然后单击确定。

    ![已成功附加的数据磁盘](./media/howto-attach-disk-windows-linux/exisitingdisksuccessful.png)
