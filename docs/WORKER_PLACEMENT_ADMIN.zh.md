# Worker Placement 页面调度

`/admin` 的 **Worker 执行区域** 面板保存的是期望区域，并通过 GitHub Actions 发布新的 Cloudflare Worker Version。Placement 是部署期配置；KV 或 `config.json` 本身不能即时改变执行区域。

## 一次性配置

1. 把本目录的修改推送到你拥有的 GitHub 仓库。
2. 在该仓库的 Actions secrets 创建 `CLOUDFLARE_API_TOKEN`，权限至少包括目标账户的 Workers Scripts 编辑、Workers Routes 编辑和 KV 编辑。
3. 在 Cloudflare Worker 的 Variables/Secrets 设置：

| 名称 | 类型 | 示例 |
| --- | --- | --- |
| `GITHUB_DEPLOY_REPOSITORY` | 普通变量 | `OWNER/edgetunnel-private` |
| `GITHUB_DEPLOY_REF` | 可选普通变量 | `main` |
| `GITHUB_DEPLOY_WORKFLOW` | 可选普通变量 | `deploy-placement.yml` |
| `DEPLOY_GITHUB_TOKEN` | Secret | GitHub fine-grained token，仓库 Actions: Read and write |

`DEPLOY_GITHUB_TOKEN` 仅保存在 Worker Secret，页面、KV 和日志均不返回它。

## 使用方式

进入 `https://ed.dys.qzz.io/admin`，在 **Worker 执行区域** 选择东京、新加坡、首尔、香港、孟买、悉尼、法兰克福、伦敦、俄勒冈、弗吉尼亚或默认就近执行，点击 **应用并发布**。

Worker 会调用仓库的 `workflow_dispatch`；workflow 仅接受预定义区域映射，渲染临时 `wrangler.toml` 并执行 `wrangler deploy`，不会把选择写回仓库。部署结果、Version ID 和失败日志以 GitHub Actions 为准。

## 回滚

在 GitHub Actions 重新选择上一地区即可发布新的回滚版本；也可在 Cloudflare Workers 部署历史中回滚到旧 Version。