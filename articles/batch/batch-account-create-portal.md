<properties
	pageTitle="创建 Azure Batch 帐户 | Microsoft Azure"
	description="了解如何在 Azure 门户中创建 Azure Batch 帐户，以便在云中运行大规模并行工作负荷"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.date="03/18/2016"
	wacn.date=""/>



# 在 Azure 门户中创建和管理 Azure Batch 帐户

> [AZURE.SELECTOR]
- [Azure 门户](/documentation/articles/batch-account-create-portal.md)
- [Batch Management .NET](/documentation/articles/batch-management-dotnet.md)

[Azure 门户][azure_portal]提供了创建和管理 Azure Batch 帐户所需的工具，你可将这些工具用于大规模的并行工作负荷处理。在本文中，我们将使用门户逐步说明如何创建 Batch 帐户，以及讨论 Batch 帐户最重要的几项设置与属性。例如，使用 Batch 开发的应用程序和服务需要帐户的 URL 和访问密钥（在 Azure 门户中可找到这两项）才能与 Batch 服务 API 进行通信。

>[AZURE.NOTE] Azure 门户当前支持 Batch 服务所提供的部分功能，包括创建帐户及管理帐户设置和属性。开发人员可通过 Batch API 获取 Batch 的完整功能（包括创建和运行作业与任务）。

## 创建批处理帐户

1. 登录到 [Azure 门户][azure_portal]。

2. 单击“新建”>“计算”>“Batch 服务”。

	![应用商店中的批处理][marketplace_portal]

3. 在“Batch 服务”边栏选项卡上检查信息，然后单击“创建”。请注意，已禁用部署模型选择功能。这是因为 Batch 仅使用资源组部署模型。

	![Azure 门户中的创建 Batch 服务边栏选项卡][3]

4. “新建 Batch 帐户”边栏选项卡随即显示。请参阅以下 a 到 ee 项，以获取每个边栏选项卡元素的说明。

    ![创建批处理帐户][account_portal]

	a.**帐户名** -- 为 Batch 帐户指定唯一的名称。此名称必须是创建帐户的 Azure 区域内的唯一名称（请参阅下面的“位置”）。它只能包含小写字符、数字，且长度必须为 3-24 个字符。

	b.**订阅** -- 选择要在其中创建 Batch 帐户的订阅。如果你只有一个订阅，则默认选择此项目。

	c.**资源组** -- 选择新的 Batch 帐户的资源组，如果订阅中没有资源组，则新建一个。

	d.**位置** -- 选择要在其中创建 Batch 帐户的 Azure 区域。只有订阅和资源组支持的区域显示为选项。

    e.**存储帐户**（可选）-- 可以将存储帐户关联（链接）到新的 Batch 帐户。Batch 的[应用程序包](batch-application-packages.md)功能在应用程序包的存储和检索操作中使用链接的存储帐户。有关此功能的详细信息，请参阅[使用 Azure Batch 应用程序包部署应用程序](batch-application-packages.md)。

     > [AZURE.TIP] 在链接的存储帐户中重新生成密钥时需要特别注意。有关详细信息，请参阅下面的 [Batch 帐户注意事项](#considerations-for-batch-accounts)。

5. 单击“创建”以创建帐户。

  门户将指示它“正在部署”帐户，“Batch 帐户”边栏选项卡将在完成时显示。

## 查看 Batch 帐户属性

Batch 帐户边栏选项卡显示帐户的多个属性，并且可让你访问其他设置，例如访问密钥、配额、用户和存储帐户关联。

* **Batch 帐户 URL** -- 当你使用 [Batch REST][api_rest] API 或 [Batch .NET][api_net] 客户端库时，此 URL 可用于访问 Batch 帐户，并遵循以下格式：

  `https://<account_name>.<region>.batch.azure.com`

* **访问密钥** -- 若要查看及管理 Batch 帐户的访问密钥，请单击密钥图标打开“管理密钥”边栏选项卡，或单击“所有设置”>“密钥”。与 Batch 服务 API（例如 [Batch REST][api_rest] 或 [Batch .NET][api_net] 客户端库）通信时，需要有访问密钥。

 ![批处理帐户密钥][account_keys]

* **所有设置** -- 若要管理 Batch 帐户的所有设置，或要查看其属性，请单击“所有设置”以打开“设置”边栏选项卡。此边栏选项卡可供访问帐户的所有设置和属性，包括查看帐户配额、选择要链接到 Batch 帐户的 Azure 存储帐户，以及管理用户。

 ![Batch 帐户设置和属性边栏选项卡][5]

## Batch 帐户注意事项

* 你也可以使用 [Batch PowerShell cmdlet](batch-powershell-cmdlets-get-started.md) 和 [Batch Management .NET](batch-management-dotnet.md) 库来创建和管理 Batch 帐户。

* 你不需要支付 Batch 帐户本身的费用。你需要支付 Batch 解决方案所用 Azure 计算资源的费用，以及工作负荷运行时其他服务所用资源的费用。例如，你需要支付池中计算节点的费用，而如果使用[应用程序包](batch-application-packages.md)功能，则需支付用于存储应用程序包版本的 Azure 存储空间资源费用。有关详细信息，请参阅 [Batch 定价][batch_pricing]。

* 你可以在单个 Batch 帐户中运行多个 Batch 工作负荷，或者在不同 Azure 区域的 Batch 帐户之间分散工作负荷。

* 如果你正在运行多个大规模 Batch 工作负荷，请注意适用于 Azure 订阅和每个 Batch 帐户的特定 [Batch 服务配额与限制](batch-quota-limit.md)。Batch 帐户的当前配额显示在门户上的帐户属性中。

* 如果将存储帐户与 Batch 帐户相关联，在重新生成存储帐户访问密钥时，请保持谨慎。应该只重新生成单个存储帐户密钥，请单击链接的存储帐户边栏选项卡中上的“同步密钥”，等待 5 分钟，让密钥传播到池中的计算节点，然后重新生成并同步其他密钥（如果需要）。如果同时重新生成这两个密钥，计算节点将无法同步任何一个密钥，并且无法访问存储帐户。

  ![重新生成存储帐户密钥][4]

## 后续步骤

* 若要深入了解 Batch 服务的概念和功能，请参阅 [Azure Batch 功能概述](batch-api-basics.md)。本文讨论主要 Batch 资源（例如池、计算节点、作业和任务），并提供能够进行大规模计算工作负荷执行的服务功能概述。

* 了解使用 [Batch .NET 客户端库](batch-dotnet-get-started.md)开发支持 Batch 的应用程序的基本概念。[简介文章](batch-dotnet-get-started.md)介绍了使用 Batch 服务在多个计算节点上执行工作负荷的可行应用程序，并说明如何使用 Azure 存储空间进行工作负荷文件暂存和检索。

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.cn
[batch_pricing]: /pricing/details/batch/

[3]: ./media/batch-account-create-portal/batch_acct_03.png "Azure 门户中的创建 Batch 服务边栏选项卡"
[4]: ./media/batch-account-create-portal/batch_acct_04.png "重新生成存储帐户密钥"
[5]: ./media/batch-account-create-portal/batch_acct_05.png "Batch 帐户设置和属性边栏选项卡"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG

<!---HONumber=Mooncake_0405_2016-->