# PandoraNext

## 手动部署

* 在[Releases](https://github.com/pandora-next/deploy/releases)中下载对应操作系统和架构的包。
* 解压后修改同目录中的`config.json`至你需要的参数。
* 将获取到的`JWT Token`内容写进同目录的`license.jwt`文件中。
* 启动`PandoraNext`或`PandoraNext.ext`即可。

## Docker 部署

```bash
$ docker pull pengzhile/pandora-next
$ docker run -d --restart always --name PandoraNext --net=bridge -p 8181:8181 \
             -e PANDORA_NEXT_LICENSE="<JWT Token>" pengzhile/pandora-next

```

* 容器内默认监听`8181`端口，映射宿主机的`8181`端口，可自行修改。
* 你可以映射目录到容器内的`/data`目录，`config.json`和`tokens.json`放在其中。
* 自行使用真实的`JWT Token`替换命令中的`<JWT Token>`，没有`<`和`>`，不要搞错。

## Docker Compose 模版

```yaml
version: '3'
services:
  pandora-next:
    image: pengzhile/pandora-next
    container_name: PandoraNext
    network_mode: bridge
    restart: always
    ports:
      - "8181:8181"
    environment:
      - PANDORA_NEXT_LICENSE=<JWT Token>
```

* 对照上述`Docker 部署`的内容自行修改。

* 如果你映射了`/data`目录，要提供`config.json`，这是一个示例：

```json
{
  "bind": "127.0.0.1:8181",
  "timeout": 600,
  "proxy_url": "",
  "public_share": false,
  "site_password": "",
  "whitelist": null
}
```

## Nginx 配置

```
server {
	listen 443 ssl http2;
	server_name chat.zhile.io;
	
	charset utf-8;
	
	ssl_certificate      certs/chat.zhile.io.crt;
	ssl_certificate_key  certs/chat.zhile.io.key;

	...省略若干其他配置...
	
	location / {
		proxy_http_version 	1.1;
		proxy_pass 		http://127.0.0.1:8181/;
		proxy_set_header	Connection		"";
		proxy_set_header   	Host			$http_host;
		proxy_set_header 	X-Forwarded-Proto 	$scheme;
		proxy_set_header   	X-Real-IP          	$remote_addr;
		proxy_set_header   	X-Forwarded-For    	$proxy_add_x_forwarded_for;
		
		proxy_buffering off;
		proxy_cache off;
		
		send_timeout 600;
		proxy_connect_timeout 600;
		proxy_send_timeout 600;
		proxy_read_timeout 600;
	}

	...省略若干其他配置...
}
```

* Nginx建议开启`http2`。
* 以上仅为推荐配置，可根据具体情况进行改动。
* 建议开启`ssl`也即`https`，否则浏览器限制将无法复制网页内容。

## tokens 配置

* 以下是一个示例`tokens.json`文件

```json
{
  "test-1": {
    "token": "access token / session token / refresh token / share token",
    "shared": true,
    "show_user_info": false
  },
  "test-2": {
    "token": "access token / session token / refresh token / share token",
    "shared": true,
    "show_user_info": true,
    "plus": true
  },
  "test2": {
    "token": "access token / session token / refresh token / share token",
    "password": "12345"
  }
}
```

* `token`支持示例文件中所写的所有类型。`session token`和`refresh token`可自动刷新。
* 每个key被称为`token key`，可在登录框作用户名输入。如上：`test-1`、`test-2`等。
* 如果设置了`password`则输入完`token key`进入输入密码页面输入匹配。
* 如果设置`shared`为`true`，则这个账号会出现在`/shared.html`中，登录页面会出现它的链接。
* `/shared.html`中的账号和共享站功能相同，可以自行设置隔离密码进行会话隔离。
* `plus`用来标识`/shared.html`上账号是否有金光，没有其他作用。
* `show_user_info`表示共享时是否显示账号邮箱信息，GPTs建议开启。

## 关于 license.jwt文件

* 在这里获取：[https://dash.pandoranext.com](https://dash.pandoranext.com)
* **没有固定IP的情况**，IP变动后在上述服务重新拉取授权即可。
* `PHP`是世界上最好的编程语言。
