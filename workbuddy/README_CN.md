# WorkBuddy CPA 插件（Fork）

> Fork 自 [Sliverkiss/cpa-plugin](https://github.com/Sliverkiss/cpa-plugin)（上游已归档，本仓库为维护分支）。

[CLIProxyAPI (CPA)](https://github.com/router-for-me/CLIProxyAPI) 插件，把腾讯
WorkBuddy（CodeBuddy）账号接入 CPA，**同时支持国内站与海外站登录**：

| 区域 | 站点 |
|---|---|
| 国内 | `copilot.tencent.com` / `codebuddy.cn` |
| 海外 | `www.workbuddy.ai` |

[English → README.md](README.md)

## 本 Fork 的改动

**1. 支持国内 / 海外双区域登录**（上游只能登国内）

- 新增 `oauth_region` 配置项（`cn` 默认 / `global`）。
- CPA **官方** OAuth 登录入口（`management.html#/oauth`）按该配置生成对应区域的授权链接。
- WorkBuddy 面板提供「OAuth 区域：国内 / 国际」切换。
- 登录流程与凭据保存仍由 CPA 核心负责，插件不自带登录流程。

**2. 修复海外账号「模型目录不可用」**

海外账号的 JWT issuer 是 `https://www.workbuddy.ai/auth/realms/copilot`，上游只匹配
`workbuddy.ai`，导致主机名不匹配、模型目录拉取失败（`auth_invalid`，面板显示
「模型目录不可用」）。本 fork 已修复。

## 安装

```bash
cp workbuddy.so /path/to/cliproxyapi/plugins/
```

```yaml
plugins:
  enabled: true
  dir: plugins
  configs:
    workbuddy:
      enabled: true
```

## 登录（国内 / 海外）

登录走 CPA **官方** OAuth 入口：

1. 打开 WorkBuddy 面板 `/v0/resource/plugins/workbuddy/panel`
2. 点「OAuth 区域：国内 / 国际」，选择要登录的区域
3. 打开 CPA 官方 OAuth 页面 **`management.html#/oauth`**，点「开始 workbuddy 登录」
4. 在浏览器完成登录，凭据由 CPA 保存

每个账号登录一次。切换区域**不影响已有账号**，只影响下一次登录。

## 配置项

```yaml
plugins:
  configs:
    workbuddy:
      enabled: true

      # 官方 OAuth 登录入口使用的区域
      #   cn     → copilot.tencent.com（默认）
      #   global → www.workbuddy.ai
      oauth_region: "cn"

      # 以下均为上游原有配置，按需使用
      checkin_auto: true      # CN 账号每日签到（09:00 / 21:00）
      lifecycle_auto: true    # 积分耗尽时禁用 CN / 删除 Global
      scheduler_mode: "off"   # off：交给 CPA 调度；credits：面板选中账号优先
      models: []              # 非空则作为完整模型列表，跳过动态发现
      proxy-url: ""           # 插件级代理
      usage_report_url: ""    # CPAMP usage 上报（可选）
      usage_report_key: ""
      management_key: ""      # 插件层管理鉴权（可选）
```

模型 alias / 排除用 CPA 原生的 `oauth-model-alias` 与 `oauth-excluded-models`。

## License

MIT — 见 [LICENSE](LICENSE)。
