<properties 
	pageTitle="教程 - Azure 批处理服务入门" 
	description="了解 Azure 批处理的基本概念，如何如何将它用于简单的方案" 
	services="batch" 
	documentationCenter=".net" 
	authors="yidingzhou" 
	manager="timlt" 
	editor=""/>

# 适用于 .NET 的 Azure 批处理库入门  

本文包含以下两篇教程，以帮助你开始使用 Azure 批处理服务的 .NET 库进行开发。  

-	[教程 1：适用于 .NET 的 Azure 批处理库](#tutorial1)
-	[教程 2：适用于 .NET 的 Azure Batch Apps 库](#tutorial2)  


有关 Azure 批处理的背景信息和方案，请参阅 [Azure 批处理技术概述](/documentation/articles/batch-technical-overview/)。

## <a name="tutorial1"></a>教程 1：适用于 .NET 的 Azure 批处理库
  	
本教程将向你介绍如何创建一个控制台应用程序，以使用 Azure 批处理服务设置分布在虚拟机池之间的计算。在本教程中创建的任务先评估 Azure 存储中的文件文本，然后返回最常用的词语。示例是用 C# 代码编写的，并使用了适用于 .NET 的 Azure 批处理库。


>[AZURE.NOTE] 若要完成本教程，你需要一个 Azure 帐户。只需几分钟即可创建一个免费试用帐户。有关详细信息，请参阅 [Azure 免费试用](/pricing/1rmb-trial/)。 
>
>你需要使用 NuGet 获取 **Microsoft.Azure.Batch.dll** 程序集。在 Visual Studio 中创建项目后，可在"解决方案资源管理器"中右键单击该项目，然后选择"管理 NuGet 包"。在线搜索 **Azure.Batch**，然后单击"安装"以安装 Azure 批处理包和依赖项。
>
>请确保你的 Nuget Package Manager 版本为 2.8 或更高。可以从 Visual Studio ->"帮助"->"关于 Microsoft Visual Studio"对话框查看版本号。如果的 Nuget Package Manager 版本较旧，则需要更新 Visual Studio，否则下载正确版本的 Nuget 依赖项时可能会出现问题。
> 
>此外，你可以参考 MSDN 上的 [Azure 批处理 Hello World 示例](https://code.msdn.microsoft.com/Azure-Batch-Sample-Hello-6573967c)， 以获取与此处所述代码类似的示例。



### 概念
批处理服务用于计划可伸缩的分布式计算。使用该服务，你可以运行分布在多个虚拟机中的大型工作负荷。这些工作负荷为密集型计算提供高效支持。使用批处理服务时，可以利用以下资源：  

-	**帐户** - 服务中的唯一标识的实体。所有处理都是都通过批处理帐户完成。
-	**池**- 在其上运行任务应用程序的任务虚拟机的集合。
-	**任务虚拟机** - 分配到池中并由分配到池中运行的作业的任务使用的计算机。
-	**任务虚拟机用户** - 任务虚拟机上的用户帐户。
-	**工作项** - 指定如何对池中的任务虚拟机执行计算。
-	**作业** - 工作项的运行中实例，由一系列的任务组成。
-	**任务** - 与作业关联的、在任务虚拟机上运行的应用程序。
-	**文件** - 包含通过任务处理的信息。

让我们从最基本的用法开始。

### 创建 Azure 批处理帐户
可使用管理门户创建批处理帐户。创建帐户后，系统会向你提供一个密钥。有关详细信息，请参阅 [Azure 批处理技术概述](/documentation/articles/batch-technical-overview/)。  

### 如何：将池添加到帐户
任务虚拟机池是要运行任务时必须创建的第一组资源。  

1.	打开 Microsoft Visual Studio 2013，在"文件"菜单中，单击"新建"，然后单击"项目"。

2.	在"Windows"的"Visual C#"下，单击"控制台应用程序"，将项目命名为 **GettingStarted**，将解决方案命名为 **AzureBatch**，然后单击"确定"。

3.	将以下命名空间声明添加到 Program.cs 的顶部：

		using Microsoft.Azure.Batch;
		using Microsoft.Azure.Batch.Auth;
		using Microsoft.Azure.Batch.Common;

	确保引用 Microsoft.Azure.Batch.dll 程序集。

4.	将以下变量添加到 Program 类：

		private const string PoolName = "[name-of-pool]";
		private const int NumOfMachines = 3;
		private const string AccountName = "[name-of-batch-account]";
		private const string AccountKey = "[key-of-batch-account]";
		private const string Uri = "https://batch.core.chinacloudapi.cn";
	替换以下值：
	-	**[name-of-pool]** - 要用于池的名称。
	-	**[name-of-batch-account]** - 批处理帐户的名称。
	-	**[key-of-batch-account]** - 为你的批处理帐户提供的密钥。
5.	将以下代码添加到 Main，以定义要使用的凭据：

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
6.	将以下代码添加到 Main，以创建用于执行操作的客户端：

		IBatchClient client = BatchClient.Connect(Uri, cred);
7.	将以下代码添加到 Main，以创建池（如果池不存在）：

		using (IPoolManager pm = client.OpenPoolManager())
		{
		   IEnumerable<ICloudPool> pools = pm.ListPools();
		   if (!pools.Select(pool => pool.Name).Contains(PoolName))
		   {
		      Console.WriteLine("The pool does not exist, creating now...");
		      ICloudPool newPool = pm.CreatePool(
		         PoolName, 
		         osFamily: "3", 
		         vmSize: "small", 
		         targetDedicated: NumOfMachines);
		       newPool.Commit();                  
		    }
		}
		Console.WriteLine("Created pool {0}", PoolName);
		Console.ReadLine();
8.	保存并运行程序。已成功添加的池的状态为 **Active**。  

### 如何：列出帐户中的池
如果你不知道现有池的名称，可以获取帐户中的池列表。  

1.	将以下代码添加到 Main，以定义要使用的凭据：  

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
2.	将以下代码添加到 Main，以创建用于执行操作的客户端：

		IBatchClient client = BatchClient.Connect(Uri, cred);

3.	将 Main 进程更新为以下代码，以写入帐户中所有池的名称和状态，并创建池（如果池不存在）：

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
		IBatchClient client = BatchClient.Connect(Uri, cred);
		
		using (IPoolManager pm = client.OpenPoolManager())
		{
		    IEnumerable<ICloudPool> pools = pm.ListPools();
		
		    Console.WriteLine("Listing Pools\n=================");
		    foreach (var p in pools)
		    {
		        Console.WriteLine("Pool: " + p.Name + " State:" + p.State);
		    }  
		
		    if (!pools.Select(pool => pool.Name).Contains(PoolName))
		    {
		        Console.WriteLine("The pool does not exist, creating now...");
		        ICloudPool newPool = pm.CreatePool(
		           PoolName,
		           osFamily: "3",
		           vmSize: "small",
		           targetDedicated: NumOfMachines);
		        newPool.Commit();
		    }
		}
		Console.WriteLine("Created pool {0}. Press <Enter> to continue.", PoolName);
		Console.ReadLine();

4.	保存并运行程序。你将看到以下输出。

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

### 如何：列出帐户中的工作项
如果你不知道现有工作项的名称，可以获取帐户中的工作项列表。  

1.	将以下代码添加到 Main 的末尾，以写入帐户中所有工作项的名称和状态：

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   Console.WriteLine("Listing Workitems\n=================");
		   IEnumerable<ICloudWorkItem> workitems = wm.ListWorkItems();
		   foreach (var w in workitems)
		   {
		      Console.WriteLine("Workitem: " + w.Name + " State:" + w.State);
		   }   
		}
		Console.WriteLine("Press <Enter> to continue."); 
		Console.ReadLine();
7.	保存并运行程序。由于我们尚未提交任何工作项，因此你可能看不到任何输出。我们将在下一部分讨论如何添加工作项。  

## 如何：将工作项添加到帐户
你必须创建一个工作项，以定义任务在池中的运行方式。  

1.	将以下变量添加到 Program 类：

		private static readonly string WorkItemName = Environment.GetEnvironmentVariable("USERNAME") + DateTime.Now.ToString("yyyyMMdd-HHmmss");

2.	在创建工作项时，也将创建作业。可向工作项分配名称，但分配给作业的名称始终为 **job-0000000001**。将以下代码添加到 Main（在列出工作项代码的前面），以添加工作项：

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   ICloudWorkItem cloudWorkItem = wm.CreateWorkItem(WorkItemName);
           cloudWorkItem.JobExecutionEnvironment = new JobExecutionEnvironment() {PoolName = PoolName};
		   cloudWorkItem.Commit();
		}
		Console.WriteLine("Workitem successfully added. Press <Enter> to continue.");
		Console.ReadLine();
3.	保存并运行程序。已成功添加的工作项的状态为 **Active**。你应会看到以下输出：

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.
		
		Workitem successfully added. Press <Enter> to continue.
		
		Listing Workitems
		=================
		Workitem: yidingz20141106-111211 State:Active
		Press <Enter> to continue.

### 如何：将任务添加到作业
不包含任务的工作项没有任何作用。创建工作项并创建作业之后，可以将任务添加到作业。让我们向作业添加一个将简单的任务。  

1.	将以下变量添加到 Program 类：

		private const int Count = 4; // number of tasks that will run
		private const string JobName = "job-0000000001";

2.	将以下代码添加到 Main，以便将任务添加到作业，等待它们运行，然后报告结果：

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		    string taskName;
		    ICloudJob job = wm.GetJob(WorkItemName, JobName);
		    for (int i = 1; i <= Count; ++i)
		    {
		        taskName = "taskdata" + i;
		        string commandLine = String.Format("cmd /c echo I am {0}", taskName);
		        ICloudTask taskToAdd = new CloudTask(taskName, commandLine);
		        job.AddTask(taskToAdd);
		        job.Commit();
		        job.Refresh();
		    }
		
		    ICloudJob listjob = wm.GetJob(WorkItemName, JobName);
		    client.OpenToolbox().CreateTaskStateMonitor().WaitAll(listjob.ListTasks(),
		       TaskState.Completed, new TimeSpan(0, 30, 0));
		    Console.WriteLine("The tasks completed successfully. Terminating the workitem...");
		    wm.GetWorkItem(WorkItemName).Terminate();
		    foreach (ICloudTask task in listjob.ListTasks())
		    {
		        Console.WriteLine("Task " + task.Name + " says:\n" + task.GetTaskFile(Constants.StandardOutFileName).ReadAsString());
		    }
		    Console.ReadLine();
		}

3.	保存并运行程序。结果应如下所示：

		The tasks completed successfully. Terminating the workitem...
		Task taskdata1 says:
		I am taskdata1
		
		Task taskdata2 says:
		I am taskdata2
		
		Task taskdata3 says:
		I am taskdata3

### 创建任务处理程序
现在，我们可以在 VM 上运行 hello world。让我们实际操作一下。在本部分中，我们将创建任务处理程序，并将它上载到运行任务的任务虚拟机。  

1.	在解决方案资源管理器的 **AzureBatch** 解决方案中创建名为 **ProcessTaskData** 的新控制台应用程序项目。

2.	将以下命名空间声明添加到 Program.cs 的顶部：

		using System.Net;

3.	将以下代码添加到要用于处理数据的 Main：

		string blobName = args[0];
		int numTopN = int.Parse(args[1]);
		
		WebClient myWebClient = new WebClient();
		string content = myWebClient.DownloadString(blobName);
		
		string[] words = content.Split(' ');
		var topNWords =
		   words.
		   Where(word => word.Length > 0).
		   GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
		   OrderByDescending(x => x.Value).
		   Take(numTopN).
		   ToList();
		
		foreach (var pair in topNWords)
		{
		    Console.WriteLine("{0} {1}", pair.Key, pair.Value);
		}

4.	保存并生成项目。  

### 准备资源以运行任务

当你使用批处理服务创建分布式计算方案时，可以使用以下基本工作流：  

1.	将你要在分布式计算方案中使用的文件上载到 Azure 存储帐户。这些文件必须在存储帐户中，批处理服务才能够访问它们。当任务运行时，这些文件将会加载到任务虚拟机上。
2.	将相关二进制文件上载到存储帐户。二进制文件包括由任务运行的程序和相关程序集。这些文件还必须从存储访问，并且加载到任务虚拟机上。
3.	创建任务虚拟机池。你可以分配要在创建该池时使用的任务虚拟机的大小。在任务运行时，将从该池为其分配一台虚拟机。
4.	创建工作项。你可以使用工作项管理任务作业。在创建工作项时，将自动创建作业。
5.	将任务添加到作业。每个任务都使用所上载的程序处理已上载的文件中的信息。
6.	监视输出结果。  

我们已经演示了步骤 3 至 6。现在，让我们了解如何准备 Azure 存储空间以运行任务。

#### 获取存储帐户
你需要有一个存储帐户才能继续完成本教程的余下内容。如果你不知道如何获取帐户，请参阅[创建 Azure 存储帐户](#tutorial1_storage)。

#### 上载数据

1. 在 Azure 存储帐户中创建名为"gettingstarted"的容器。这可以在 Azure 门户中完成。请确保将"访问"字段设置为"公共容器"。

2. 将"ProcessTaskData.exe"上载到该容器。

3. 创建三个文本文件（taskdata1.txt、taskdata2.txt 和 taskdata3.txt，每个文件包含以下段落之一），并将这些文件上载到该容器：

		当你需要弹性资源来满足业务需求时，可使用 Azure 虚拟机来设置可按需缩放的计算基础结构。可以从库中创建运行 Windows、Linux 和企业应用程序（如 SharePoint 和 SQL Server）的虚拟机。也可以捕获和使用你自己的映像来创建自定义的虚拟机。
		
		使用 Azure 云服务快速部署和管理功能强大的应用程序和服务。只需上载应用程序，Azure 将处理部署细节（从设置和负载平衡到运行状况监视）以实现持续可用性。你的应用程序由行业领先的每月 99.95% 的 SLA 提供支持。你只需专注于应用程序而非基础结构。
		
		Azure 网站为托管 Web 应用程序提供了可缩放的、可靠的且易于使用的环境。从一系列框架和模板中进行选择，几秒钟就可以创建一个网站。使用任何工具或 OS 以利用 .NET、PHP、Node.js 或 Python 开发网站。从各种源代码管理选项（包括 TFS、GitHub 和 BitBucket）中进行选择，可设置持续集成并像一个团队一样进行开发。利用其他 Azure 托管服务（如存储、CDN 和 SQL Database）随时间扩展网站功能。


>[AZURE.NOTE] 在生产环境中，建议你使用共享访问签名。


>[AZURE.NOTE] Azure 存储团队发表了一篇[博客文章](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)， 其中列出了可帮助上载文件的 Azure 存储资源管理器。



### 更新任务提交代码

1.	将以下变量添加到 Program 类：

		private const string BlobPath = "[storage-path]"; 
	替换以下值：
	-	**[storage-path]** - Blob 在存储空间中的路径。例如: http://yiding.blob.core.chinacloudapi.cn/gettingstarted/

2. 按如下所示更新任务提交代码。

		string taskName;
		ICloudJob job = wm.GetJob(WorkItemName, JobName);
		for (int i = 1; i <= Count; ++i)
		{
		    taskName = "taskdata" + i;
		    IResourceFile programFile = new ResourceFile(BlobPath + "ProcessTaskData.exe", "ProcessTaskData.exe");
		    IResourceFile supportFile = new ResourceFile(BlobPath + taskName + ".txt", taskName);
		    string commandLine = String.Format("ProcessTaskData.exe {0} {1}", BlobPath + taskName + ".txt", 3);
		    ICloudTask taskToAdd = new CloudTask(taskName, commandLine);
		    taskToAdd.ResourceFiles = new List<IResourceFile>();
		    taskToAdd.ResourceFiles.Add(programFile);
		    taskToAdd.ResourceFiles.Add(supportFile);
		    job.AddTask(taskToAdd);
		}
		job.Commit(); 

3. 保存并运行程序。你应会看到：

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.
		
		Workitem successfully added. Press <Enter> to continue.
		
		Listing Workitems
		=================
		Workitem: yidingz20141106-132140 State:Active
		Press <Enter> to continue.
		
		The tasks completed successfully. Terminating the workitem...
		Task taskdata1 says:
		can 3
		you 3
		and 3
		
		Task taskdata2 says:
		and 5
		application 3
		the 3
		
		Task taskdata3 says:
		a 5
		and 5
		to 3

### 如何：删除池和工作项
处理工作项后，你可以通过删除池和工作项释放资源。

#### 删除池
1.	将以下代码添加到 Main 的末尾，以删除池：

		using (IPoolManager pm = client.OpenPoolManager())
		{
		   pm.DeletePool(PoolName);
		}
		Console.WriteLine("Pool successfully deleted.");
		Console.ReadLine();
2.	保存并运行程序。


#### 删除工作项
1.	将以下代码添加到 Main，以删除工作项：

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   wm.DeleteWorkItem(WorkItemName);
		}
		Console.WriteLine("Workitem successfully deleted.");
		Console.ReadLine();
2.	保存并运行程序。

### <a name="tutorial1_storage"></a>附录：创建 Azure 存储帐户
在运行本教程中的代码之前，你必须对 Azure 中的存储帐户拥有访问权限。  

1.	登录到 [Azure 管理门户](http://manage.windowsazure.cn/)。
2.	在导航窗格的底部，单击"新建"。  
![][1]
3.	依次单击"数据服务"、"存储"和"快速创建"。
![][2]

4.	在"URL"中，键入要在存储帐户的 URI 中使用的子域名称。该条目可能包含 3 到 24 个小写字母和数字。此值将成为用于对订阅的 Blob、队列或表资源进行寻址的 URI 中的主机名。
5.	选择要在其中查找存储的"位置/地缘组"。
6.	还可以选择启用地域复制。
7.	单击"创建存储帐户"。  

有关 Azure 存储空间的详细信息，请参阅[如何在 .NET 中使用 Azure Blob 存储服务](/documentation/articles/storage-dotnet-how-to-use-blobs/)。  


## <a name="tutorial2"></a>教程 2：适用于 .NET 的 Azure Batch Apps 库
本教程说明如何在 Azure 批处理上，使用 Batch Apps 服务运行并行计算工作负载。

Batch Apps 是 Azure 批处理的一项功能，提供以应用程序为中心的方法来管理和执行批处理工作负载。使用 Batch Apps SDK 可以创建包，以便能够批处理应用程序，并将这些包部署在自己的帐户中，或提供给其他批处理用户使用。批处理还提供数据管理、作业监视、内置的诊断和日志记录，以及任务间依赖关系支持。此外，它还包含一个管理门户，你可以在其中管理作业、查看日志和下载输出，而无需编写自己的客户端。

在 Batch Apps 方案中，可以使用 Batch Apps 云 SDK 编写代码，将作业分割成并行任务、描述这些任务之间的任何依赖关系，以及指定如何执行每个任务。此代码将部署到批处理帐户。然后，客户端只需指定作业类型和 REST API 的输入文件，即可执行作业。

>[AZURE.NOTE] 若要完成本教程，你需要一个 Azure 帐户。只需几分钟即可创建一个免费试用帐户。有关详细信息，请参阅 [Azure 免费试用](/pricing/1rmb-trial/)。可以使用 NuGet 来获取 <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/">Batch Apps 云</a>程序集和 <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/">Batch Apps 客户端</a>程序集。在 Visual Studio 中创建项目后，可在"解决方案资源管理器"中右键单击该项目，然后选择"管理 NuGet 包"。你还可以下载 Batch Apps 的 Visual Studio 扩展，其中包含可启用应用程序云功能的项目模板和部署应用程序的功能。若要下载该扩展，请单击<a href="https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a">此处</a>，或者在 Visual Studio 中通过"扩展和更新"菜单项搜索 **Batch Apps**。你也可以查找 <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">MSDN 上的端到端示例</a>。
>

### Azure Batch Apps 基础知识 
批处理旨在用于处理现有的计算密集型应用程序。它利用现有的应用代码，并在动态、虚拟化、通用环境中运行。为了使应用程序能够与 Batch Apps 配合运行，必须先完成一些准备工作：

1.	准备现有应用程序可执行文件（在传统服务器场或群集中运行的相同可执行文件）的 zip 文件，以及它所需的任何支持文件。然后，使用管理门户或 REST API 将此 zip 文件上载到批处理帐户。
2.	创建"云程序集"（用于将工作负载分派给应用程序）的 zip 文件，然后通过管理门户或 REST API 上载它。云程序集包含两个针对云 SDK 构建的组件：
	1.	作业拆分器 - 将作业分解成可独立处理的任务。例如，在动画案例中，作业拆分器将使用某个影片作业，并将影片分割成若干帧。 
	2.	任务处理器- 调用给定任务的应用程序可执行文件。例如，在动画案例中，任务处理器将调用一个渲染程序，以渲染现有任务指定的单个帧。 
3.	提供一种方式以将作业提交给 Azure 中启用的应用程序。这可能是应用程序 UI 中的插件、Web 门户，甚至是执行管道中的无人值守服务。有关示例，请参阅 MSDN 上的<a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">示例</a>。



### Batch Apps 的重要概念
Batch Apps 的编程和使用模式围绕以下重要概念：

#### 作业
**作业**是用户提交的一项工作。提交作业后，用户将指定作业的类型、该作业的任何设置，以及作业所需的数据。启用的实现可以代表用户确定这些详细信息，或者，在某些情况下，用户可以通过客户端显式提供此信息。作业具有返回的结果。每个作业都有主要输出，有时还有预览输出。如果需要，作业还可以返回额外的输出。

#### 任务
**任务**是要在作业中完成的一项工作。在用户提交作业后，该作业将分解为一些较小的任务。然后，服务将处理其中的各个任务，再将任务结果组合成完整的作业输出。任务的性质取决于作业的类型。作业拆分器根据应用程序主要处理的任务部分，定义如何将作业细分为任务。下载任务输出时，也可以单独下载，这在某些情况下可能很有用，例如，用户可能想要从动画作业中下载单个任务。

#### 合并任务
**合并任务**是将单个任务的结果组合成最终作业结果的特殊任务。在影片渲染作业中，合并任务可能会将渲染的帧组合成影片，或将所有渲染的帧压缩成单个文件。即使不需要实际进行任何"合并"，每个作业仍会有合并任务。

#### 文件
**文件**是输入到作业中的数据片段。作业可能没有关联的输入文件，也可能有一或多个关联的输入文件。相同的文件也可以用于多个作业，例如，对于影片渲染作业，文件可能是纹理、模型，等等。对于数据分析作业，文件可能是一组观察值或测量值。

### 启用云应用程序
应用程序必须包含静态字段或属性，该字段或属性包含应用程序的所有详细信息。它指定应用程序的名称，以及应用程序所处理的作业类型。使用 SDK（可通过 Visual Studio 库下载）中的模板时，便会提供这个字段或属性。 

下面是并行工作负载的云应用程序声明的一个示例：

	public static class ApplicationDefinition
	{
	    public static readonly CloudApplication App = new ParallelCloudApplication
	    {
	        ApplicationName = "ApplicationName",
	        JobType = "ApplicationJobType",
	        JobSplitterType = typeof(MyJobSplitter),
	        TaskProcessorType = typeof(MyTaskProcessor),
	    };
	}

#### 实现作业拆分器和任务处理器
对于易并行工作负载，必须实现一个作业拆分器和一个任务处理器。 

#### 实现 JobSplitter.SplitIntoTasks
SplitIntoTasks 实现必须任务序列。每个任务代表一项独立的工作，它将被排入队列供计算节点处理。每个任务都必须是独立的，并且必须设有任务处理器所需的全部信息。 

作业拆分器指定的任务以 TaskSpecifier 对象表示。TaskSpecifier 有一些可以在返回任务之前设置的属性。
  

-	TaskIndex 将被忽略，但可供任务处理器使用。你可以使用此属性将索引传递给任务处理器。如果不需要传递索引，则不需要设置此属性。
-	默认情况下，Parameters 是一个空集合。可以将项目添加给它，或将它替换为新集合。可以使用 WithJobParameters 或 WithAllJobParameters 方法从作业参数集合中复制条目。  


默认情况下，RequiredFiles 是一个空集合。可以将项目添加给它，或将它替换为新集合。可以使用 RequiringJobFiles 或 RequiringAllJobFiles 方法从作业文件集合中复制条目。

可以指定依赖于另一个特定任务的任务。为此，请设置 TaskSpecifier.DependsOn 属性，并传递它所依赖的任务 ID：

	new TaskSpecifier {
	    DependsOn = TaskDependency.OnId(5)
	}

任务必须等到依赖的任务生成输出之后才会运行。   

你还可以指定整组任务必须等到另一组任务完成之后才开始处理。在此情况下，可以设置 TaskSpecifier.Stage 属性。具有给定 Stage 值的任务必须等到 Stage 值更小的所有任务完成之后，才会开始处理。例如，必须等到 Stage 0、 1 或 2 的所有任务完成之后，具有 Stage 3 的任务才会开始处理。Stage 必须从 0 开始，各个 Stage 的顺序必须没有间隔，并且 SplitIntoTasks 必须依 Stage 顺序返回任务：例如，在返回 Stage 为 1 的任务之后返回 Stage 为 0 的任务，即为错误。

每个并行操作以一项称为合并任务的特殊任务来结束。合并任务的作业是将并行处理任务的结果组合成最终结果。Batch Apps 会自动为你创建合并任务。  

在极少数情况下，你可能想要显式控制合并任务，例如，自定义其参数。在此情况下，可以返回 TaskSpecifier 并将其 IsMerge 属性设置为 true，以指定合并任务。这会覆盖自动合并任务。如果要创建显式合并任务：  

-	可以只创建一个显式合并任务
-	它必须是序列中的最后一个任务
-	它必须将 IsMerge 设置为 true，而且其他每个任务必须将 IsMerge 设置为 false  


但请记住，通常不需要显式创建合并任务。  

以下代码演示了 SplitIntoTasks 的一个简单实现。  

	protected override IEnumerable<TaskSpecifier> SplitIntoTasks(
	    IJob job,
	    JobSplitSettings settings)
	{
	    int start = Int32.Parse(job.Parameters["startIndex"]);
	    int end = Int32.Parse(job.Parameters["endIndex"]);
	    int count = (end - start) + 1;
	 
	    // Processing tasks
	    for (int i = 0; i < count; ++i)
	    {
	        yield return new TaskSpecifier
	        {
	            TaskIndex = start + i,
	        }.RequiringAllJobFiles();
	    }
	}
#### 实现 ParallelTaskProcessor.RunExternalTaskProcess

从作业拆分器返回的每个非合并任务都会调用 RunExternalTaskProcess。它应该使用适当的参数来调用你的应用程序，并返回需要保留以供将来使用的输出集合。

以下代码段演示了如何调用名为 application.exe 的程序。请注意，可以使用 ExecutablePath 帮助器方法来创建绝对文件路径。

云 SDK 中的 ExternalProcess 类提供了有用的帮助器逻辑来运行应用程序可执行文件。ExternalProcess 可以处理取消操作、将退出代码转换为异常、捕获标准输出和标准错误，以及设置环境变量。此外，你也可以根据需要直接使用 .NET 进程类来运行程序。

RunExternalTaskProcess 方法返回 TaskProcessResult，其中包含输出文件的列表。至少必须包含合并所需的所有文件。可以选择性地返回日志文件、预览文件和中间文件（例如，用于在任务失败时进行诊断）。请注意，你的方法将返回文件路径，而不是文件内容。

每个文件都必须以它所包含的输出类型来标识：output（即，最终作业输出的一部分）、preview、log 或 intermediate。这些值来自 TaskOutputFileKind 枚举。以下代码段将返回单个任务输出，而不返回预览或日志文件。TaskProcessResult.FromExternalProcessResult 方法可以简化从命令行程序中捕获退出代码、处理器输出和输出文件的常见方案：

以下代码演示了 ParallelTaskProcessor.RunExternalTaskProcess 的一个简单实现。

	protected override TaskProcessResult RunExternalTaskProcess(
	    ITask task,
	    TaskExecutionSettings settings)
	{
	    var inputFile = LocalPath(task.RequiredFiles[0].Name);
	    var outputFile = LocalPath(task.TaskId.ToString() + ".jpg");
	 
	    var exePath = ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("-in:{0} -out:{1}", inputFile, outputFile);
	 
	    var result = new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath,
	        CancellationToken = settings.CancellationToken
	    }.Run();
	
	    return TaskProcessResult.FromExternalProcessResult(result, outputFile);
	}
#### 实现 ParallelTaskProcessor.RunExternalMergeProcess

这是为合并任务调用的方法。它应该调用应用程序来组合先前任务的输出（以适合应用程序的任何方式），然后返回组合的输出。 

RunExternalMergeProcess 的实现非常类似于 RunExternalTaskProcess，差别在于：  

-	RunExternalMergeProcess 使用先前任务的输出而不是用户输入文件。因此，应该根据任务 ID 来判断想要处理的文件的名称，而不是从 Task.RequiredFiles 集合来判断。
-	RunExternalMergeProcess 返回 JobOutput 文件，并选择性地返回 JobPreview 文件。


以下代码演示了 ParallelTaskProcessor.RunExternalMergeProcess 的一个简单实现。

	protected override JobResult RunExternalMergeProcess(
	    ITask mergeTask,
	    TaskExecutionSettings settings)
	{
	    var outputFile =  "output.zip";
	 
	    var exePath =  ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("a -application {0} *.jpg", outputFile);
	 
	    new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath
	    }.Run();
	 
	    return new JobResult {
	        OutputFile = outputFile
	    };
	}

### 将作业提交到 Batch Apps 
作业描述要运行的工作负载，需要包含有关工作负载的所有信息（文件内容除外）。例如，作业包含从客户端流向作业拆分器，再流向任务的配置设置。MSDN 上提供的示例演示了如何提交作业并提供多个客户端，包括 Web 门户和命令行客户端。 


<!--Anchors-->


<!--Image references-->
[1]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-01.jpg
[2]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-02.jpg
[3]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-03.jpg
[4]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-04.jpg


