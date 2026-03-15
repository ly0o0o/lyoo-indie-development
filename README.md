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

## 主力技术栈

| 层级 | 技术 | 备注 |
|------|------|------|
| DNS / CDN / WAF | Cloudflare | 免费计划够用 |
| 国内加速 | 腾讯云 EdgeOne / 阿里云 CDN | 反向代理海外后端 |
| Frontend | 任意框架 | 部署到 Vercel / Railway |
| Backend | Fastify | 部署到 Railway |
| 数据库 | PostgreSQL | Railway 托管 |
| 缓存 | Redis | Upstash / Railway |
| 对象存储 | Cloudflare R2 | 免费 10GB，无出口费 |
| BaaS 备选 | Supabase | Auth / DB / Storage 一体 |
| CI/CD | GitHub Actions | 自动构建部署 |

---

## 备选方案：Cloudflare 全家桶

如果想全部跑在 Cloudflare 生态里，可以用以下组合：

| 层级 | Cloudflare 产品 | 说明 |
|------|-----------------|------|
| 前端托管 | Cloudflare Pages | 类似 Vercel，支持 SSR |
| 后端计算 | Cloudflare Workers | 边缘 Serverless 函数 |
| 数据库 | D1 (SQLite) | 边缘分布式 SQLite |
| KV 存储 | Workers KV | 全球分布式键值存储 |
| 对象存储 | R2 | S3 兼容，无出口费 |
| 队列 | Queues | 消息队列 |
| 定时任务 | Cron Triggers | Workers 定时执行 |
| AI | Workers AI | 边缘推理 |
| 实时通信 | Durable Objects | 有状态 WebSocket |

### 优点

- 全球边缘部署，延迟极低
- 免费额度慷慨（Workers 每天 10 万次请求免费）
- 无出口流量费（R2 是杀手级优势）
- 一站式管理，DNS / CDN / 计算 / 存储全在一个面板
- Pages 部署体验接近 Vercel，支持 Git 集成
- 冷启动几乎为零（V8 Isolates，不是容器）

### 缺点与限制

- **Workers 运行时限制**：免费版 CPU 时间 10ms/请求，付费版 30s。不适合重计算任务
- **Workers 内存限制**：128MB，无法处理大文件或大数据集
- **D1 局限性**：SQLite 内核，不支持 JOIN 性能优化、无存储过程、单库 10GB 上限。复杂查询场景不如 PostgreSQL
- **Workers KV 最终一致性**：写入后全球同步有延迟（通常 60s 内），不适合强一致性场景
- **不支持 TCP/UDP**：Workers 只能处理 HTTP，无法直连传统数据库（需要 Hyperdrive 代理）
- **生态锁定**：深度使用后迁移成本高，API 和运行时不完全兼容 Node.js
- **Node.js 兼容性**：Workers 运行时不是完整 Node.js，部分 npm 包无法直接使用
- **Durable Objects 价格**：有状态场景费用上升快，$0.15/百万请求 + 存储费
- **调试体验一般**：本地开发用 wrangler dev，但和线上行为偶有差异

### 适用场景建议

| 场景 | 是否推荐 CF 全家桶 |
|------|-------------------|
| 轻量 API / BFF | ✅ 非常适合 |
| 静态站 + SSR | ✅ Pages 很好用 |
| 简单 CRUD 应用 | ✅ D1 + Workers 够用 |
| 复杂业务逻辑 / 重查询 | ❌ 用 Railway + PostgreSQL |
| 长时间运行任务 | ❌ CPU 时间限制 |
| 需要完整 Node.js 生态 | ❌ 兼容性问题 |
| 实时 WebSocket | ⚠️ Durable Objects 可以但贵 |

> 个人建议：Cloudflare 做 DNS + CDN + R2 存储，计算层还是用 Railway 更灵活。等 D1 和 Workers 生态更成熟了再考虑全量迁移。

---

## 月度成本估算

| 服务 | 费用 |
|------|------|
| Cloudflare (DNS/CDN/R2) | 免费 / 按量 |
| Vercel | 免费起步 |
| Railway | ~$5/月起 |
| Upstash Redis | 免费起步 |
| Supabase | 免费起步 |
| **CF 全家桶方案** | Workers $5/月起（付费版），其余按量 |

---

## MCP 工具集

| 工具 | 用途 |
|------|------|
| [Supabase MCP](https://supabase.com) | 数据库 / Auth / Storage |
| [Stripe MCP](https://stripe.com) | 支付处理 |
| [Postman MCP](https://www.postman.com) | API 测试与管理 |
| [Railway CLI](https://docs.railway.app/develop/cli) | 后端部署与管理 |
