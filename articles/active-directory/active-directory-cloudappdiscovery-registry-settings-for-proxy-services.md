<properties 
	pageTitle="代理服务的 Cloud App Discovery 注册表设置 | Azure" 
	description="本主题旨在为你提供在运行 Cloud App Discovery 代理的计算机上设置必要端口所需执行的步骤。" 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="stevenpo"/>

<tags 
	ms.service="active-directory"
	ms.date="04/07/2016" 
	wacn.date=""/>

# 代理服务的 Cloud App Discovery 注册表设置

默认情况下，Cloud App Discovery 代理配置为仅使用端口 80 或 443。
如果计划在具有使用自定义端口（既不是 80 也不是 443）的代理服务器的环境中安装 Cloud App Discovery，则需要将代理配置为使用此端口。
该配置以注册表项为基础。


本主题旨在为你提供在运行 Cloud App Discovery 代理的计算机上设置必要端口所需执行的步骤。



**若要修改运行 Cloud App Discovery 代理的计算机所使用的端口，请执行下列步骤：**


1. 启动注册表编辑器。<br> ![运行](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)

2. 导航到或创建以下注册表项：<br> **HKLM\_LOCAL\_MACHINE\\Software\\Microsoft\\Cloud App Discovery\\Endpoint**

3. 创建一个新的**多字符串**值，命名为**端口**。![新建](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)

4. 若要打开“编辑多字符串”对话框，请双击“端口”值。


5. 在“值数据”文本框中键入以下值，然后添加你组织所使用的所有自定义端口：<br><br> 
**80** <br> 
**8080** <br> 
**8118** <br> 
**8888** <br> 
**81** <br> 
**12080** <br> 
**6999** <br> 
**30606** <br> 
**31595** <br> 
**4080** <br> 
**443** <br> 
**1110** <br><br>
![编辑多字符串](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)

6. 单击“确定”以关闭“编辑多字符串”对话框。



**其他资源**


* [如何发现本组织中使用的未经认可的云应用](/documentation/articles/active-directory-cloudappdiscovery-whatis) 



<!---HONumber=Mooncake_0613_2016-->