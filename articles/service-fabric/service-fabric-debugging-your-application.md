<properties
   pageTitle="在 Visual Studio 中调试应用程序 | Microsoft Azure"
   description="通过在本地开发群集上采用 Visual Studio 进行开发和调试，来提高服务的可靠性和性能。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="01/28/2016"
   wacn.date=""/>

# 使用 Visual Studio 调试 Service Fabric 应用程序

可以通过在本地计算机开发群集中部署和调试 Azure Service Fabric 应用程序来节省时间和资金。Visual Studio 可以将应用程序部署到本地群集并自动将调试器连接到应用程序的所有实例。

1. 按[设置 Service Fabric 开发环境](/documentation/articles/service-fabric-get-started)中的步骤来创建本地开发群集。

2. 按“F5”或单击“调试”>“启动调试”。

    ![开始调试应用程序][startdebugging]

3. 通过单击“调试”菜单中的命令来设置代码中的断点并单步执行应用程序。

    > [AZURE.NOTE] Visual Studio 将附加到应用程序的所有实例。单步执行代码时，断点可能被多个进程命中，从而产生并发会话。尝试通过在线程 ID 上设置每个断点条件，或使用诊断事件，在命中后禁用断点、。

4. “诊断事件”窗口将会自动打开，以便你可以实时查看诊断事件。

    ![查看实时诊断事件][diagnosticevents]

5. 你也可以打开服务器资源管理器中的“诊断事件”窗口。在“Azure”下，右键单击“Service Fabric 群集”>“查看诊断事件”。

    ![打开“诊断事件”窗口][viewdiagnosticevents]

6. 诊断事件可以在自动生成的“ServiceEventSource.cs”文件中查看并从应用程序代码中进行调用。

    ```csharp
    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, Service.ServiceTypeName);
    ```

7. “诊断事件”窗口支持筛选、暂停和检查实时事件。筛选是对事件消息及其内容进行的简单字符串搜索。

    ![筛选、暂停和恢复，或检查实时事件][diagnosticeventsactions]

8. 调试服务与调试任何其他应用程序类似。一般可通过 Visual Studio 设置断点以方便进行调试。即使 Reliable Collections 在多个节点间进行复制，它们仍会实现 IEnumerable。这意味着，在调试时可以使用 Visual Studio 中的结果视图来查看其中存储的内容。只需在代码的任意位置设置一个断点即可。

    ![开始调试应用程序][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 后续步骤

- [测试 Service Fabric 服务](/documentation/articles/service-fabric-testability-overview)。
- [在 Visual Studio 中管理 Service Fabric 应用程序](/documentation/articles/service-fabric-manage-application-in-visual-studio)。

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
 

<!---HONumber=Mooncake_0307_2016-->