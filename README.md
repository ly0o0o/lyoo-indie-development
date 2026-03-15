# lyoo-indie-development

出海独立开发个人技术栈、MCP 组件与开发套装整理。

---

## 架构总览

```
Cloudflare (DNS + CDN + WAF)
│
┌────────┴────────┐
Frontend           Backend
(任意框架)          (Fastify API)
Vercel/Railway     Railway
│
┌─────────┼──────────┐
│         │          │
Postgres    Redis       R2
Railway  Upstash/     Cloudflare
         Railway
```

---

## 海外 + 国内双栈

```
国内用户
│
腾讯云 EdgeOne / 阿里云 CDN
（国内节点加速，反向代理）
│
海外 Railway Backend
(Cloudflare R2 / Supabase)
```

---

## CI/CD 流程

```
git push → GitHub Actions → 构建 → 自动部署
                                ├── Vercel（Frontend）
                                └── Railway（Backend）
```

---

## 技术栈

| 层级 | 技术 |
|------|------|
| DNS / CDN / WAF | Cloudflare |
| 国内加速 | 腾讯云 EdgeOne / 阿里云 CDN |
| Frontend | 任意框架，部署到 Vercel / Railway |
| Backend | Fastify，部署到 Railway |
| 数据库 | PostgreSQL（Railway） |
| 缓存 | Redis（Upstash / Railway） |
| 对象存储 | Cloudflare R2 |
| BaaS 备选 | Supabase |
| CI/CD | GitHub Actions |

---

## 月度成本估算

| 服务 | 费用 |
|------|------|
| Cloudflare | 免费 / 按量 |
| Vercel | 免费起步 |
| Railway | ~$5/月起 |
| Upstash Redis | 免费起步 |
| Cloudflare R2 | 免费 10GB |
| Supabase | 免费起步 |

---

## MCP 工具集

| 工具 | 用途 |
|------|------|
| [Supabase MCP](https://supabase.com) | 数据库 / Auth / Storage |
| [Strapi MCP](https://strapi.io) | Headless CMS |
| [Postman MCP](https://www.postman.com) | API 测试与管理 |
| [Railway CLI](https://docs.railway.app/develop/cli) | 后端部署与管理 |

---

## 目录结构

```
lyoo-indie-development/
├── README.md         # 本文件
├── stack/            # 技术栈配置模板
├── mcp/              # MCP 相关配置与使用说明
└── ci-cd/            # GitHub Actions 工作流模板
```
