<properties
   pageTitle="设置 Service Fabric 开发环境 |Microsoft Azure"
   description="安装 Service Fabric 运行时、SDK 和工具并创建本地开发群集。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="09/10/2015"
   wacn.date=""/>

# 设置 Service Fabric 开发环境
 本文介绍了开始构建 [Service Fabric][1] 应用所需的一切，包括安装运行时、SDK、工具和设置本地群集。

 >[AZURE.NOTE]这些说明适用于设置新 PC。如果你已在自己的 PC 上安装了以往版本的 Service Fabric，请按照[说明更新你的开发环境](/documentation/articles/service-fabric-update-your-development-environment)。

## 先决条件
### 支持的操作系统版本
支持以下操作系统版本：

- Windows 8/8.1
- Windows Server 2012 R2
- Windows 10

### Visual Studio 2015

适用于 Service Fabric 的工具取决于 Visual Studio 2015，你可以在[此处][2]找到。

> [AZURE.NOTE]如果你不运行其中一个支持的操作系统版本或不想在你的 PC 上安装 Visual Studio 2015，则可以在使用 VM 库中的映像预安装 Windows Server 2012 R2 和 Visual Studio 2015 的情况下设置 Azure 虚拟机。

## 安装运行时、SDK 和工具

Service Fabric 组件的安装通过 Web 平台安装程序完成。请按照这些说明安装：

1. 使用 Web 平台安装程序[下载 SDK][3]。

2. 单击“安装”开始安装过程。

3. 查看并接受 EULA。

将自动继续进行安装。

## 启用 PowerShell 脚本执行

Service Fabric 使用 Windows PowerShell 脚本创建本地开发群集和部署 Visual Studio 中的应用程序。默认情况下，Windows 会阻止这些脚本运行。若要启用它们，你必须修改你的 PowerShell 执行策略。以管理员身份打开 PowerShell 并输入以下命令：

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## 安装并启动本地群集
本地群集表示你在单个开发计算机上最终用于生产中的多计算机拓扑。若要设置本地群集，请按照这些步骤操作：


1. 关闭所有其他 PowerShell 窗口并以管理员身份启动一个新窗口。

2. 导航到群集设置目录

    ```powershell
    cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"
    ```
    
3. 运行

    ```powershell
    .\DevClusterSetup.ps1
    ```

不一会儿，你应该会看到显示节点信息的输出和已成功创建群集的确认。在某些情况下，可能会在 Service Fabric 主机服务和命名服务启动时看到警告。这些是正常现象，并且之后会立刻出现关于群集的一些基本信息。

> [AZURE.NOTE]你的本地群集使用与在 Azure 中运行的运行时完全相同的运行时。它并不以任何方式模拟或仿真。唯一的区别是所有节点都在单个计算机上运行，而不是像它们在 Azure 中那样分布到多台计算机上。

## 验证群集设置

你可以使用附带 SDK 的 Service Fabric 资源管理器工具检查你的群集是否已创建成功。

1. 通过运行启动 Service Fabric 资源管理器 

    ```powershell
    . "$env:ProgramW6432\Microsoft SDKs\Service Fabric\Tools\ServiceFabricExplorer\ServiceFabricExplorer.exe"
    ```

2. 在左上角展开 Onebox/本地群集节点。

3. 确保“应用程序”和“节点”视图显示绿色。

如果任何元素都不显示绿色或者你看到错误，稍等片刻然后单击刷新按钮。如果仍然存在问题，请查看[设置故障排除步骤](/documentation/articles/service-fabric-troubleshoot-local-cluster-setup)。

## 后续步骤
设置开发环境之后，你可以开始构建和运行应用。

- [了解编程模型：Reliable Actors 和 Reliable Services](/documentation/articles/service-fabric-choose-framework)
- [Reliable Services API 入门](/documentation/articles/service-fabric-reliable-services-quick-start)
- [Reliable Actors API 入门](/documentation/articles/service-fabric-reliable-actors-get-started)
- [在 GitHub 上检查 Service Fabric 示例](https://github.com/azure/servicefabric-samples)
- [使用 Service Fabric 资源管理器可视化群集](/documentation/articles/service-fabric-visualizing-your-cluster)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric 活动页"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[3]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "WebPI 链接"

<!---HONumber=74-->