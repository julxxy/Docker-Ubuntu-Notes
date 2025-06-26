# 1 Ubuntu开机自动执行shell

> 可用于开机后立即执行一些操作

## 1.1 创建 /etc/rc.local

```shell
RC_LOCAL="/etc/rc.local"
if [ ! -f "${RC_LOCAL}" ]; then
  touch $RC_LOCAL
fi
if [ ! -x "${RC_LOCAL}" ]; then
  sudo chmod 777 -vR ${RC_LOCAL}
fi

```

## 1.2 在 /etc/rc.local 文件里面添加启动脚本

- 添加到`exit 0`前, `#!/bin/bash`之间

```shell
#!/bin/bash
sudo bash ~/NetworkManager.sh
sudo bash ~/es_log_clean.sh
sudo bash ~/nginx_run.sh
sudo bash /usr/local/feige/run_feige_cn.sh
sudo bash /usr/local/feige/run_feige_hk.sh
exit 0

```
