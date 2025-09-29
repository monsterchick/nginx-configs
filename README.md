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

