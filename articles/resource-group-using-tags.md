<properties
	pageTitle="使用标记来组织 Azure 资源"
	description=""
	services=""
	documentationCenter=""
	authors="flanakin"
	writer=""
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.date="04/28/2015"
	wacn.date=""/>


# 使用标记来组织 Azure 资源

Azure 门户和基础资源管理器的作用体现在，让你完全根据需要组织资源和定制体验。

在 Azure 经典门户中，只能通过订阅来为资源分类和分组。在 Azure 门户中，[我们引入了资源组](resource-group-portal)，可让你为相关实体分组。而[随着基于角色的访问的引入](role-based-access-control-configure)，这项功能变得更加有用。根据相同的设计理念，你可以使用键/值对来标记资源，以跨资源组以及在门户中跨订阅分类和查询资源。

按照团队、项目甚至环境为资源分组，可以在需要时重点查看所需的资源。


## 在 Azure 门户中进行标记

在门户中标记资源和资源组的过程非常简单。使用“浏览”中心导航到要标记的资源或资源组，然后在边栏选项卡顶部的“概览”部分中单击“标记”部件。

![资源和资源组边栏选项卡上的“标记”部件](./media/resource-group-using-tags/rgblade.png)

此时将打开一个边栏选项卡，其中包含已应用的标记列表。如果这是你的第一个标记，该列表将为空。若要添加标记，只需指定名称和值，然后按 Enter。添加若干个标记后，系统会根据预先存在的标记名称和值提供自动填充选项，以更好地确保各项资源的分类保持一致，并避免常见错误，如拼写错误。

![使用名称/值对标记资源](./media/resource-group-using-tags/tag-resources.png)

在这里，你可以单击每个标记来查看具有相同标记的所有资源的列表。当然，如果这是你的第一个标记，该列表的信息不会很丰富。现在，让我们改为使用 PowerShell 来快速标记所有资源。


## 使用 PowerShell 进行标记

第一步是获取最新的 [Azure PowerShell 模块](install-configure-powershell)。如果这是你首次使用 Azure PowerShell 模块，请[阅读文档](install-configure-powershell)以快速入门。在本文中，我们假设你已添加一个帐户，并选择了包含你要标记的资源的订阅。

标记功能只适用于[资源管理器](http://msdn.microsoft.com/zh-cn/library/azure/dn790568.aspx)中提供的资源和资源组，因此要做的下一步是改为使用资源管理器。有关详细信息，请参阅[将 Azure PowerShell 与 Azure 资源管理器配合使用](powershell-azure-resource-manager)。

    Switch-AzureMode AzureResourceManager

标记直接存在于资源和资源组中，因此，若要查看已应用了哪些标记，只需分别使用 `Get-AzureResource` 或 `Get-AzureResourceGroup` 获取资源或资源组。让我们从一个资源组着手。

![在 PowerShell 中使用 Get-AzureResourceGroup 获取标记](./media/resource-group-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

此 cmdlet 将返回有关资源组的元数据的多个片段，包括已应用了哪些标记（如果有）。若要标记资源组，只需将使用 `Set-AzureResourceGroup` 并指定标记名称和值。

![在 PowerShell 中使用 Set-AzureResourceGroup 设置标记](./media/resource-group-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

请记住，标记将作为一个整体更新，因此，如果要将一个标记添加到已标记的资源，需要保存一个数组，其中包含你要保留的所有标记。若要删除一个标记，只需保存不包含你要删除的标记的数组。

该过程对于资源是相同，不过，使用的是 `Get-AzureResource` 和 `Set-AzureResource` cmdlet。若要获取具有特定标记的资源或资源组，请结合 `-Tag` 参数使用 `Get-AzureResource` 或 `Get-AzureResourceGroup` cmdlet。

![在 PowerShell 中分别使用 Get-AzureResource 和 Get-AzureResourceGroup 获取已标记的资源和资源组](./media/resource-group-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## 使用资源管理器进行标记

门户和 PowerShell 在幕后都使用[资源管理器 REST API](http://msdn.microsoft.com/zh-cn/library/azure/dn790568.aspx)。如果需要将标记集成到另一个环境中，可以对资源 ID 使用 GET 以获取标记，并使用 PATCH 调用更新标记集。


## 管理分类

以前，我们已介绍过自动填充如何帮助你确保一致性和避免出错。自动填充是根据订阅可用标记设置的分类进行的。添加到资源或资源组的每个标记将自动添加到订阅范围的分类，但是，随着将来不断地标记资源，你可以在分类中预先填充你要使用的标记名称和值。

若要使用 PowerShell 获取订阅中所有标记的列表，请使用 `Get-AzureTag` cmdlet。

![PowerShell 中的 Get-AzureTag](./media/resource-group-using-tags/Get-AzureTag-in-PowerShell.png)


你可能会看到，有些标记以“hidden-”和“link:”开头。这属于内部标记，你应忽略这些标记并避免更改。

使用 `New-AzureTag` cmdlet 可将新标记添加到分类。即使这些标记尚未应用到任何资源或资源组，也会包含在自动填充内容中。若要删除某个标记名称/值，请先从它可能已应用到的所有资源中将它删除，然后使用 `Remove-AzureTag` cmdlet 从分类中将它删除。

若要在门户中查看标记的分类，请使用“浏览”中心查看“所有内容”，然后选择“标记”。

![通过“浏览”中心查找标记](./media/resource-group-using-tags/browse-tags.png)

将最重要的标记固定启动板以便快速访问，准备工作到此完成。尽情使用吧！

![将标记固定到启动板](./media/resource-group-using-tags/pin-tags.png)

## 后续步骤
入门

- [Azure 资源管理器概述](resource-group-overview)  
- [将 Azure PowerShell 与 Azure 资源管理器配合使用](powershell-azure-resource-manager)
- [将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure 资源管理配合使用](xplat-cli-azure-resource-manager)  
- [使用 Azure 门户管理 Azure 资源](resource-group-portal)  

创建和部署应用程序

- [创作 Azure 资源管理器模板](resource-group-authoring-templates)  
- [使用 Azure 资源管理器模板部署应用程序](resource-group-template-deploy)  
- [Azure 中的资源组部署疑难解答](resource-group-deploy-debug)  
- [Azure 资源管理器模板函数](resource-group-template-functions)  
- [高级模板操作](resource-group-advanced-template)  

管理和审核访问权限

- [管理和审核对资源的访问权限](resource-group-rbac)  
- [通过 Azure 资源管理器对服务主体进行身份验证](resource-group-authenticate-service-principal)  
- [使用 Azure 经典门户创建新的 Azure 服务主体](resource-group-create-service-principal-portal)  

<!---HONumber=61-->