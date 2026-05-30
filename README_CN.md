# Xray on Cloudflare Workers (中文文档)

Xray 是一个基于 Cloudflare Workers 构建的轻量级 Serverless V2Ray 隧道，专为印度尼西亚网络环境优化，支持现代主流代理协议。无需传统 VPS，即可实现快速、安全且高扩展性的部署。

🌐 **Web UI 演示:** [free-cf.benxx.dpdns.org](https://free-cf.benxx.dpdns.org)
*(您可以根据需要自定义 Web UI)*

---

## 🌐 语言 / Language
* [English Version](./README.md) | **简体中文**

---

## 🔧 功能特性
- ✅ **多协议支持**：VMess, VLESS, Trojan, Shadowsocks (SS)
- ✅ **DNS over HTTPS (DoH)**：加密 DNS 查询，提升隐私与安全性。

## 🌐 路由端点 (Endpoints)
| 端点 | 描述 |
| -------- | --------------------------------- |
| `/`      |  简单的静态 "Hello World" 页面  |
| `/link`  |  生成 VMess, VLESS, Trojan 及 SS 节点分享链接 
| `/sub`   |  简单的静态 "Subscription" 页面 |

---

## 1️⃣ Cloudflare 账户准备
1. 确保您拥有一个 [Cloudflare](https://cloudflare.com) 帐户（免费）。  
2. 获取您的 **账户 ID (Account ID)**：  
   - 登录到 [Cloudflare 控制台](https://dash.cloudflare.com/)。  
   - 在控制台首页，滚动至右下角，找到 **账户 ID**。  
   - 复制并保存该 ID，稍后将用于 `wrangler.toml` 和 GitHub Secrets。

---

## 🚀 部署指南 (GitHub Actions)

### 2️⃣ 创建 KV 命名空间
该 Worker 需要使用 Cloudflare KV 来存储数据。
1. 在 Cloudflare 控制面板中，前往 **Workers & Pages** → **KV**。  
2. 点击 **创建命名空间 (Create namespace)**，填入名称：`YUMI`。  
3. 创建完成后，点击该空间名称，复制其 **ID**（例如：`8ff1913fa986482d967e0a5c7e2b0791`）。保存此 ID 以备后用。

### 3️⃣ 创建 Cloudflare API 令牌
此令牌用于授权 GitHub Actions 部署脚本访问您的 Cloudflare 账户。
1. 前往 [Cloudflare API 令牌页面](https://dash.cloudflare.com/profile/api-tokens)。  
2. 点击 **创建令牌 (Create Token)** → 选择 **编辑 Cloudflare Workers** 模板。  
3. 点击 **继续以查看摘要** → **创建令牌**。  

### 4️⃣ GitHub 仓库配置
1. **添加 Secret**：前往 GitHub → 您的项目仓库 → **Settings** → **Secrets and variables** → **Actions**。
2. 添加一个新的 Repository Secret：
   - **Name**: `CLOUDFLARE_API_TOKEN`
   - **Value**: 填入刚才生成的 Cloudflare API 令牌。
3. **分叉/克隆仓库**：将本仓库 Fork 或 Clone 到您的 GitHub 账户。
4. **修改 `wrangler.toml`**：在您的 GitHub 仓库中打开 `wrangler.toml` 文件，并修改以下配置项：

```toml
name = "xray"
main = "build/worker/shim.mjs"
compatibility_date = "2024-05-23"
minify = true

# 替换为步骤 1 中获取的 Account ID
account_id = "ea1aab81dc8e22018131832d124a54fc"
workers_dev = true

[[kv_namespaces]]
binding = "YUMI"
# 替换为步骤 2 中获取的 KV Namespace ID
id = "8ff1913fa986482d967e0a5c7e2b0791"

[build]
command = "cargo install worker-build && worker-build --release"

[env.dev]
build = { command = "cargo install worker-build && worker-build --dev" }

[vars]
UUID = "2bcfbfba-b446-4ad5-93ad-72af9e008f61"   # 可自定义修改您的 UUID
