<properties linkid="manage-services-how-to-create-websites" urlDisplayName="How to create" pageTitle="如何创建网站 - Azure 服务管理" metaKeywords="Azure creating  Website, Azure deleting  Website" description="了解如何使用 Azure 管理门户创建网站。" metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a  Website" authors="timamm" solutions="" manager="" editor="" />

# 如何创建网站

本主题说明如何从库或通过使用管理门户创建网站。

有关如何将内容部署到你创建的 Azure 网站的信息，请参阅 [Azure 网站](/zh-cn/documentation/services/web-sites/)中的**部署**部分。

## 目录 ##

- [如何：使用管理门户创建网站](#createawebsiteportal)
- [如何：从库创建网站](#howtocreatefromgallery)
- [如何：删除网站](#deleteawebsite)
- [后续步骤](#nextsteps)

## <a name="createawebsiteportal"></a>如何：使用管理门户创建网站

遵照以下步骤在 Azure 中创建网站。
	
1. 登录到 [Azure 管理门户](http://manage.windowsazure.cn/)。

2. 单击该管理门户左下角的"新建"图标。

3. 单击"网站"图标，再单击"快速创建"图标，输入 URL 的值，然后单击页面右下角的"创建网站"旁边的复选标记。

4. 创建网站后，你会看到文本"创建网站 <*网站名称*> 已成功"。你可以通过单击门户页底部的"浏览"，浏览到该网站。

5. 在门户中，单击在网站列表中显示的网站的名称以打开该网站的"快速启动"管理页面。

6. 在"快速启动"页上，将向你提供用于获取网站开发工具、为你的网站设置发布或从源代码管理提供程序（例如 TFS 或 Git）设置部署的选项。默认为网站设置 FTP 发布，并且 FTP 主机名显示在"仪表板"页的"速览"部分中的"FTP 主机名"下方。在使用 FTP 或 Git 进行发布前，选择"仪表板"页上的"重置部署凭据"选项，以便你可以在将内容部署到你的网站时对 FTP 主机或 Git 存储库进行身份验证。

7. "配置"管理页将公开网站的设置，例如：

	- .NET Framework 的版本，或 Web 应用程序的 PHP
	- SSL 绑定
	- 自定义域名
	- 日志记录选项
	- Azure 环境的应用程序设置（例如，覆盖开发环境的 Web.config 中的 <appSettings>）
	- 连接字符串（例如，覆盖开发环境的 Web.config 中的 <connectionStrings>）
	- 针对特定文件扩展名（如 *.php）的脚本处理器

## <a name="howtocreatefromgallery"></a>如何：从库创建网站

[WACOM.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

## <a name="deleteawebsite"></a>如何：删除网站
使用 Azure 管理门户中的"删除"图标删除网站。在单击"网站"以列出所有网站时，"删除"图标位于 Microsoft Azure 门户中，并且将位于每个网站管理页面的底部。

## <a name="nextsteps"></a>后续步骤

有关详细信息，请参阅 [Azure 网站](/zh-cn/documentation/services/web-sites/)。
