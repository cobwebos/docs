## <a name="install-wordpress"></a>安装 WordPress

若要尝试堆栈，请安装示例应用。 例如，以下步骤安装用于创建网站和博客的开源 [WordPress](https://wordpress.org/) 平台。 其他可尝试的工作负荷包括 [Drupal](http://www.drupal.org) 和 [Moodle](https://moodle.org/)。 

此 WordPress 设置适用于概念证明。 有关生产安装的详细信息和设置，请参阅 [WordPress 文档](https://codex.wordpress.org/Main_Page)。 



### <a name="install-the-wordpress-package"></a>安装 WordPress 包

运行以下命令：

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>配置 WordPress

将 WordPress 配置为使用 MySQL 和 PHP。 运行以下命令，打开所选文本编辑器并创建文件 `/etc/wordpress/config-localhost.php`：

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
将以下行复制到文件中，并将 *yourPassword* 替换为自己的数据库密码（其他值不变）。 然后保存文件。

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```

在工作目录中创建文本文件 `wordpress.sql`，以便配置 WordPress 数据库： 

```bash
sudo sensible-editor wordpress.sql
```

添加以下命令，将 *yourPassword* 替换为自己的数据库密码（其他值不变）。 然后保存文件。

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
FLUSH PRIVILEGES;
```


若要创建数据库，请运行以下命令：

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

该命令完成后，请删除文件 `wordpress.sql`。

将 WordPress 安装移至 Web 服务器文档根目录：

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

现在可以完成 WordPress 设置并在平台上进行发布了。 打开浏览器并转到 `http://yourPublicIPAddress/wordpress`。 替换 VM 的公共 IP 地址。 应如下图所示。

![WordPress 安装页](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)