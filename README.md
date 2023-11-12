# deploy

## docker 部署

```bash
$ docker pull pengzhile/pandora-next
$ docker run -d --restart always --name PandoraNext -p 8181:80 -e PANDORA_NEXT_LICENSE="jwt content" pengzhile/pandora-next

```

* 容器内默认监听`80`端口，映射宿主机的`8181`端口，可自行修改。
* 你可以映射目录到容器内的`/data`目录，`config.json`和`tokens.json`放在其中。
