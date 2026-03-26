# Cloudflare Temp Email 部署指南

## 📋 前提条件

1. **Cloudflare 账号** - 免费注册：https://dash.cloudflare.com/sign-up
2. **GitHub 账号** - 已登录 `dreamhunter2333`

---

## 🔑 第一步：获取 Cloudflare API Token

### 1. 登录 Cloudflare Dashboard
访问：https://dash.cloudflare.com/profile/api-tokens

### 2. 创建 API Token
点击 **"Create Token"** → 选择 **"Edit Cloudflare Workers"** 模板

### 3. 配置权限
- **Permissions**: `Workers Scripts` → `Edit`
- **Account Resources**: `All accounts` (或选择特定账号)
- **Zone Resources**: `All zones` (可选)

### 4. 生成并复制 Token
- 点击 **"Continue to summary"**
- 点击 **"Create Token"**
- **立即复制 Token**（只显示一次！）

### 5. 获取 Account ID
访问：https://dash.cloudflare.com/
- 右侧边栏找到 **Account ID**（32 位字符串）
- 或访问：https://api.cloudflare.com/client/v4/user/tokens/verify

---

## 🔐 第二步：配置 GitHub Secrets

### 1. 打开仓库设置
访问：https://github.com/dreamhunter2333/cloudflare_temp_email/settings/secrets/actions

### 2. 添加 Secrets

#### 添加 `CLOUDFLARE_ACCOUNT_ID`
- **Name**: `CLOUDFLARE_ACCOUNT_ID`
- **Value**: 你的 Cloudflare Account ID（32 位字符串）
- 点击 **"Add secret"**

#### 添加 `CLOUDFLARE_API_TOKEN`
- **Name**: `CLOUDFLARE_API_TOKEN`
- **Value**: 刚才创建的 API Token
- 点击 **"Add secret"**

---

## 🚀 第三步：配置 wrangler.toml

### 1. 编辑配置文件
文件路径：`worker/wrangler.toml`

### 2. 修改以下字段：

```toml
name = "your-worker-name"  # 自定义 Worker 名称（全局唯一）

[vars]
PREFIX = "tmp"  # 邮箱前缀
JWT_SECRET = "your-random-secret-key"  # 随机密钥（至少 32 字符）
DEFAULT_DOMAINS = ["your-domain.com"]  # 自定义域名（可选）
DOMAINS = ["your-domain.com"]  # 所有域名

[[d1_databases]]
binding = "DB"
database_name = "email_db"  # 数据库名称
# database_id 会在首次部署后自动填充
```

### 3. 创建 D1 数据库（可选但推荐）
访问：https://dash.cloudflare.com/?to=/:account/workers/d1/new
- 输入数据库名称：`email_db`
- 点击 **"Create database"**
- 复制 **Database ID** 填入 `wrangler.toml`

---

## ⚡ 第四步：触发自动部署

### 方式 A：推送代码自动部署
```bash
# 如果推送失败，手动推送到 GitHub
git -C C:\Users\EDY\.openclaw\workspace\AI-Account-Toolkit\cloudflare_temp_email push origin main
```

### 方式 B：GitHub Actions 手动触发
1. 访问：https://github.com/dreamhunter2333/cloudflare_temp_email/actions
2. 选择 **"Deploy Worker"** 工作流
3. 点击 **"Run workflow"**
4. 选择分支 `main`
5. 点击 **"Run workflow"**

---

## 📊 第五步：验证部署

### 1. 查看部署状态
访问：https://github.com/dreamhunter2333/cloudflare_temp_email/actions

### 2. 访问 Worker URL
部署成功后，访问：
```
https://your-worker-name.your-subdomain.workers.dev
```

### 3. 绑定自定义域名（可选）
1. 访问 Cloudflare Dashboard → Workers & Pages
2. 选择你的 Worker
3. 点击 **"Add domain"**
4. 输入你的域名

---

## 🔧 配置说明（wrangler.toml）

### 必填配置：
```toml
[vars]
JWT_SECRET = "random-secret-key-min-32-chars"  # 必须修改！
DEFAULT_DOMAINS = ["example.com"]  # 至少一个域名
DOMAINS = ["example.com"]
```

### 可选配置：
```toml
[vars]
# 语言设置
DEFAULT_LANG = "zh"
TITLE = "我的临时邮箱"
ANNOUNCEMENT = "欢迎访问"

# 安全设置
PASSWORDS = ["your-admin-password"]
ADMIN_PASSWORDS = ["your-admin-password"]

# 功能开关
ENABLE_USER_CREATE_EMAIL = true
ENABLE_USER_DELETE_EMAIL = true
ENABLE_AUTO_REPLY = false
ENABLE_ADDRESS_PASSWORD = false

# Telegram Bot 集成
TG_MAX_ADDRESS = 5
# TG_BOT_INFO = "{}"

# 转发配置
FORWARD_ADDRESS_LIST = ["forward@example.com"]
```

---

## ⚠️ 常见问题

### Q1: 部署失败 "Permission denied"
**解决**: 检查 GitHub Secrets 是否正确配置

### Q2: Worker 返回 500 错误
**解决**: 检查 D1 数据库是否正确绑定

### Q3: 无法创建邮箱
**解决**: 检查 `DEFAULT_DOMAINS` 和 `DOMAINS` 配置

### Q4: JWT_SECRET 未修改
**警告**: 必须修改为随机字符串，否则有安全风险！

---

## 📞 快速检查清单

- [ ] Cloudflare API Token 已创建
- [ ] Account ID 已复制到 GitHub Secrets
- [ ] wrangler.toml 已配置（JWT_SECRET、DOMAINS）
- [ ] D1 数据库已创建（可选）
- [ ] GitHub Actions 部署成功
- [ ] Worker 可正常访问

---

**部署时间**: 2026-03-26
**小小怪下士**: 配置完成！🫡
