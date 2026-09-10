# WorkBuddy Plugin for CLIProxyAPI (Fork)

> Fork of [Sliverkiss/cpa-plugin](https://github.com/Sliverkiss/cpa-plugin) (upstream archived; this repo is the maintained branch).

A [CLIProxyAPI (CPA)](https://github.com/router-for-me/CLIProxyAPI) plugin that
brings Tencent WorkBuddy (CodeBuddy) accounts into CPA, with support for **both
the China and the Global site**:

| Realm | Site |
|---|---|
| China (CN) | `copilot.tencent.com` / `codebuddy.cn` |
| Global | `www.workbuddy.ai` |

[中文文档 → README_CN.md](README_CN.md)

## What this fork changes

**1. Dual-realm login (CN / Global)** — upstream can only log in to the CN site.

- New `oauth_region` config field (`cn` default / `global`).
- CPA's **official** OAuth entry (`management.html#/oauth`) builds the
  authorisation URL for the configured realm.
- The WorkBuddy panel ships an "OAuth 区域：国内 / 国际" switch.
- Login and credential persistence stay with CPA core; the plugin adds no
  login flow of its own.

**2. Fix "model catalog unavailable" for Global accounts**

Global credentials carry `iss = https://www.workbuddy.ai/auth/realms/copilot`,
but upstream only matched `workbuddy.ai`. The host mismatch made those accounts
fail model-catalog discovery with `auth_invalid` ("模型目录不可用"). Fixed here.

## Install

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

## Login (CN / Global)

Login goes through CPA's **official** OAuth entry:

1. Open the WorkBuddy panel: `/v0/resource/plugins/workbuddy/panel`
2. Use the "OAuth 区域：国内 / 国际" switch to pick the realm
3. Open CPA's official OAuth page **`management.html#/oauth`** and click
   "开始 workbuddy 登录"
4. Complete the login in the browser — CPA core stores the credential

One login per account. Switching the realm **never touches existing accounts**;
it only affects the next login.

## Configuration

```yaml
plugins:
  configs:
    workbuddy:
      enabled: true

      # Realm used by the official OAuth login entry
      #   cn     -> copilot.tencent.com   (default)
      #   global -> www.workbuddy.ai
      oauth_region: "cn"

      # Everything below is inherited from upstream; use as needed
      checkin_auto: true      # daily check-in for CN accounts (09:00 / 21:00)
      lifecycle_auto: true    # disable CN / delete Global when credits run out
      scheduler_mode: "off"   # off: defer to CPA; credits: prefer panel-selected
      models: []              # non-empty = complete catalog, skips dynamic discovery
      proxy-url: ""           # plugin-level proxy
      usage_report_url: ""    # CPAMP usage forwarding (optional)
      usage_report_key: ""
      management_key: ""      # plugin-layer management auth (optional)
```

Model aliases / exclusions use CPA's native `oauth-model-alias` and
`oauth-excluded-models`.

## License

MIT — see [LICENSE](LICENSE).
