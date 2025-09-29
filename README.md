# 用法

## 目录

```linux
reverse-proxy-config/
│
├── nginx/
│   ├── nginx.conf                # 主配置文件
│   ├── sites-available/
│   │   └── mc-kyler.space.conf   # 某个域名的反代配置
│   └── conf.d/                   # 按需放置额外配置
│
├── ssl/
│   ├── README.md                 # 证书申请
│
└── README.md
```

## 证书申请 + 自动续签

```linux
# 安装 acme.sh
curl https://get.acme.sh | sh -s email=<my@example.com>

# 让当前这个 shell 立刻读取刚被安装脚本写入 
source ~/.bashrc 2>/dev/null || true

# 修改默认 CA
acme.sh --set-default-ca --server letsencrypt

# 用 cloudflare 自动验证（DNS API）
# export CF_Token="<Zone-ID>"
# export CF_Account_ID="<Acount-ID>"

# 签发通配符证书
./acme.sh --issue --dns dns_cf -d <example.com> -d '*.<example.com>'
```

## 连接文件夹到 nginx 根目录 

```linux
# ln -s 使目标路径的文件不会被覆盖,但内容会更新
sudo ln -s ./nginx/sites-available/mc-kyler.space.conf /etc/nginx/sites-enabled/

sites-available/ = 配置仓库（放所有站点配置）
sites-enabled/ = 实际启用（通过软链接挑选要加载的站点）
```

## 踩过的坑

组网策略: tailscale + 公网服务器 + 域名

1. 🙅 不要用 域名+路径后缀 的形式管理不同服务
   - 目标服务可能因为静态路径等原因无法重定向页面
2. 👍 用子域名方式替代
   - 浏览器输入和再搜索有良好的便利性. e.g. 打 dav 直接出完整网址
   - 服务器端找文件方便. e.g. 打 dav + tab 直接出文件名
3. nginx 默认配置里的 sites-enabled 是存放已经可以使用的配置文件 (默认导入)
4. 尽量用软连接 `ln -s <path-to-move> <target-directory/>`
   1. 🔥 直接 `git clone` + `ln -s` 把文件推过去, 方便快速迁移
5. 🔥 nginx 反代 + 子域名 能解决大部分由路径反代引发的静态路径坑!
6. 👍 nginx 的 stream 适合用来做流量中转 (TCP中继转发)
