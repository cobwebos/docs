###通过 Composer 安装

1. [安装 Git][安装 git]。

	<div class="dev-callout"> 
	<b>说明</b> 
	<p>在 Windows 上，您还需要将 Git 可执行文件添加到您的 PATH 环境变量。</p>
	</div>

2. 创建一个名为文件**composer.json**根目录中的项目并向其中添加以下代码：

		{
			"require": {
				"microsoft/windowsazure": "*"
			},			
			"repositories": [
				{
					"type": "pear",
					"url": "http://pear.php.net"
				}
			],
			"minimum-stability": "dev"
		}

3. 下载**[composer.phar][编辑器 phar]**项目根目录中。

4. 打开命令提示符并在项目根目录中执行该文件

		php composer.phar install

###手动安装

若要手动下载并安装用于 Azure 的 PHP 客户端库，请按照下列步骤：

1. 下载包含从库的.zip 存档[GitHub][php sdk-github]。或者，分叉存储库，并将其克隆到您的本地计算机。（后一种方法需要一个 GitHub 帐户并已在本地安装 git。）

	<div class="dev-callout"> 
	<b>说明</b> 
	<p>用于 Azure 的 PHP 客户端库有依赖关系<a href="http://pear.php.net/package/HTTP_Request2">HTTP_Request2</a>， <a href="http://pear.php.net/package/Mail_mime">Mail_mime</a>，和<a href="http://pear.php.net/package/Mail_mimeDecode">Mail_mimeDecode</a> PEAR 包。若要解决这些依赖关系的推荐的方法是将使用这些程序包安装<a href="http://pear.php.net/manual/en/installation.php">PEAR 包管理器</a>。</p> 
	</div>


2. 复制`WindowsAzure`到您的应用程序目录结构已下载存档的目录。

有关安装用于 Azure （包括信息有关作为 PEAR 包安装） 的 PHP 客户端库的详细信息，请参阅[下载 Azure SDK for PHP][下载 SDK PHP]。


[php sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[安装 git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[下载 SDK PHP]: ../php-download-sdk/
[编辑器 phar]: http://getcomposer.org/composer.phar
