## <a name="use-the-azure-portal"></a>使用 Azure 门户
1. 选择你想要重新部署，然后选择的 VM*重新部署*按钮*设置*边栏选项卡。 你可能需要向下滚动到，请参阅**支持和故障排除**节，其中包含如以下示例所示的重新部署按钮：
   
    ![Azure VM 边栏选项卡](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. 若要确认该操作，请选择*重新部署*按钮：
   
    ![重新部署 VM 边栏选项卡](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. **状态**VM 的更改为*更新*如 VM 准备重新部署，如下面的示例中所示：
   
    ![更新 VM](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. **状态**然后将更改为*正在启动*如 VM 启动上新的 Azure 主机，如下面的示例中所示：
   
    ![启动 VM](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. VM 完成启动过程中后,**状态**然后返回到*运行*，VM，该值指示具有已成功重新部署：
   
    ![VM 正在运行](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

