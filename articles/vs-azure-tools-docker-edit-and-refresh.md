<properties
   pageTitle="使用编辑和刷新功能调试本地 Docker 容器中的应用 | Azure"
   description="了解如何通过编辑和刷新功能来修改本地 Docker 容器中运行的应用以及刷新容器"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.date="03/25/2016"
   wacn.date="" />

# 使用编辑和刷新功能调试本地 Docker 容器中的应用

## 概述
Visual Studio Tools for Docker 可让你方便地在本地 Docker 容器中本地开发和测试应用程序，而无需在每次更改代码后重新启动容器。本文将演示如何使用“编辑和刷新”功能在本地 Docker 容器中启动 ASP.NET 5 Web 应用，进行任何必要的更改，然后刷新浏览器来查看这些更改。

## 先决条件
需要安装以下工具。

- [Visual Studio 2015 Update 1](https://go.microsoft.com/fwlink/?LinkId=691979)
- [Microsoft ASP .NET 和 Web Tools 2015 RC](https://go.microsoft.com/fwlink/?LinkId=627627)
- [Docker 工具箱](https://www.docker.com/products/overview#/docker_toolbox)
- [Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS)
- [配置 Docker 客户端](/documentation/articles/vs-azure-tools-docker-setup)

> [AZURE.NOTE] 如果计算机上安装了以前的 Visual Studio 2015 Tools for Docker 版本，则需要先从控制台卸载以前的版本，然后安装最新的版本。

## 编辑本地 Docker 容器中运行的应用
Visual Studio 2015 Tools for Docker 可让 ASP.NET 5 Web 应用开发人员在 Docker 容器中测试和运行应用程序、在 Visual Studio 中更改应用程序，以及刷新浏览器来查看已应用到容器中运行的应用的更改。

1. 从 Visual Studio 菜单中，选择“文件”>“新建”>“项目”。 

1. 在“新建项目”对话框的“模板”部分下，选择“Visual C#”>“Web”。

1. 选择“ASP.NET Web 应用程序”。

1. 为新应用程序指定名称（或使用默认值）。

1. 点击“确定”。

1. 在“ASP.NET 5 模板”下，选择“ASP.NET Web 应用程序”。

1. 点击“确定”。

1. 在 Visual Studio 的“解决方案资源管理器”中，右键单击项目，然后选择“添加”>“Docker 支持”。

	![][0]
 
1. 随即会在项目节点下面创建以下文件：

	![][1]

1. 将解决方案配置设置为 `Debug`，然后按 “&lt;F5>” 开始在本地测试应用程序。

1. 容器映像已生成并在 Docker 容器中运行后，PowerShell 控制台将尝试在默认浏览器中启动 Web 应用。如果你使用的是 Microsoft Edge 浏览器，请参阅[故障排除](#troubleshooting)部分。

1. 返回 Visual Studio 并打开 `Views\Home\Index.cshtml`。

1. 将以下 HTML 内容附加到文件末尾，然后保存更改

		<div>
			<h1>Hello from Docker Container!</h1>
		</div>

1.	切换回到浏览器并刷新浏览器。

1.	滚动到主页末尾，你应会看到更改已应用！ 请注意，站点可能需要几秒钟的时间来重新编译，因此如果你的更改未立即反映出来，只要再次刷新浏览器即可。

##故障排除 

- **运行应用会导致 PowerShell 打开、显示错误，然后关闭。浏览器页面未打开。**

	这可能是由于执行 `docker-compose-up` 期间出错。如要查看该错误，请执行以下步骤：

	1. 打开 `Properties\launchSettings.json` 文件
	
	1. 找到 Docker 条目。
	
	1. 找出开头为以下内容的行：

			"commandLineArgs": "-ExecutionPolicy RemoteSigned …”
	
	1. 添加 `-noexit` 参数，使该行类似于以下内容。这将使 PowerShell 保持打开，方便你查看错误。

			"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …”

- **生成: 无法生成映像，检查 TLS 连接时出错: 主机未运行**

	验证默认 Docker 主机是否正在运行。请参阅 [Configure the Docker client](/documentation/articles/vs-azure-tools-docker-setup)（配置 Docker 客户端）一文。

- **找不到卷映射**

	默认情况下，VirtualBox 会将 `C:\Users` 共享为 `c:/Users`。如果项目不在 `c:\Users` 下，请将项目手动添加到 VirtualBox 的[共享文件夹](https://www.virtualbox.org/manual/ch04.html#sharedfolders)。

- **使用 Microsoft Edge 作为默认浏览器**

	如果你使用 Microsoft Edge 浏览器，站点可能不会打开，因为 Edge 认为该 IP 地址不安全。如要解决此问题，请执行以下步骤：
	1. 在 Windows 的“运行”框中，键入 `Internet Options`。
	2. 出现“Internet 选项”时，点击该选项。 
	2. 点击“安全性”选项卡。
	3. 选择“本地 Intranet”区域。
	4. 点击“站点”。 
	5. 将虚拟机（在本例中为 Docker 主机）的 IP 地址添加到列表中。 
	6. 在 Edge 中刷新网页，然后你应会看到站点已正常运行。 
	7. 有关此问题的详细信息，请访问 Scott Hanselman 的博客文章：[Microsoft Edge can't see or open VirtualBox-hosted local web sites（在 Microsoft Edge 中无法查看或打开 VirtualBox 托管的本地网站）](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx)。

[0]: ./media/vs-azure-tools-docker-edit-and-refresh/add-docker-support.png
[1]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-files-added.png
<!---HONumber=Mooncake_0516_2016-->