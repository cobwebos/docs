


## <a name="tagging-a-virtual-machine-through-templates"></a>标记通过模板的虚拟机
首先，让我们看一下通过模板进行标记。 [此模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags)将标记放置在以下资源： 计算 （虚拟机）、 存储 （存储帐户） 和网络 （公共 IP 地址、 虚拟网络和网络接口）。 此模板是针对 Windows VM，但可适用于 Linux Vm。

单击**部署到 Azure**按钮从[模板链接](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags)。 此操作将导航到[Azure 门户](https://portal.azure.com/)可在其中部署此模板。

![使用标记的简单部署](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

此模板包括以下标记：*部门*，*应用程序*，和*创建者*。 你可以添加/编辑这些标记直接在模板中的如果你想要不同的标记名称。

![在模板中的 azure 标记](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

如你所见，标记将被定义为键/值对，用冒号 （:） 分隔。 必须按以下格式定义标记：

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

完成编辑与所选的标记后，保存模板文件。

接下来，在**编辑参数**部分中，你可以填写值标记。

![在 Azure 门户中编辑标记](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

单击**创建**以使用标记值部署此模板。

## <a name="tagging-through-the-portal"></a>通过门户进行标记
在创建后你的资源与标记，可以查看、 添加和删除在门户中的标记。

选择标记图标，以查看标记：

![在 Azure 门户中的标记图标](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

通过定义你自己的键/值对，添加新的标记通过门户，并将其保存。

![在 Azure 门户中添加新标记](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

你的新标记现在应显示在列表中的所需的资源标记。

![在 Azure 门户中保存的新标记](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

