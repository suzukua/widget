# Scriptable 生活缴费小组件合集

基于 [Scriptable](https://scriptable.app/) 的 iOS 桌面小组件，用于查看水、燃气等生活缴费信息。

| 小组件 | 脚本 | 说明 |
| --- | --- | --- |
| 成都供水 | `ChengduWaterWidget.js` | 成都自来水欠费、抄表记录、阶梯水量 |
| 华天兴能燃气 | `huatianxingneng_ranqi.js` | 华天兴能燃气账单与用量 |

## 通用环境要求

- iOS 14+
- [Scriptable](https://scriptable.app/) 最新版

## 通用使用步骤

1. 在 Scriptable 中新建脚本，将对应 `.js` 文件的全部内容复制进去
2. 按下文配置账号信息
3. 先在 Scriptable 内手动运行一次（初始化 Keychain 与会话）
4. 回到桌面添加 Scriptable 小组件并绑定该脚本（建议中号）

---

## 一、成都供水（ChengduWaterWidget）

![成都供水小组件截图](https://raw.githubusercontent.com/suzukua/widget/refs/heads/main/ChengduWaterWidget.png)

### 功能特性

- 展示欠费合计，以及水费、违约金明细
- 展示最近 3 期抄表记录（抄表日、上次→本次读数、用量、水费）
- **阶梯水量横线进度条**：
  - 三档等宽、拉满整行，档位分界为长刻度，每档内含小刻度
  - 已用水量按档位着色（绿 / 黄 / 红），白点标出当前所处位置
  - 刻度下方标注 0 / 216 / 300 分界值，每个区间下方显示档位单价
- 每日仅请求一次接口，其余时间读取缓存；刷新失败时回退到缓存并在标题栏提示
- 登录会话与缓存存放于 Keychain

### 配置

在脚本中设置水务账户号（户号）：

- 首次运行会把默认户号写入 Keychain（键 `cdwater.accountId.v1`），请将 `makeWidget` 中的默认户号 `860081076` 改为你自己的户号
- 或在 Scriptable 中直接修改该 Keychain 值；账户变更后缓存会自动按账户区分

### 阶梯规则

| 档位 | 年用水量（m³） | 单价 |
| --- | --- | --- |
| 一阶 | 0–216 | 接口返回 |
| 二阶 | 217–300 | 接口返回 |
| 三阶 | 301+ | 接口返回 |

档位水量与单价来自接口，实际以官方为准。

### 缓存与刷新

- 缓存键：`cdwater.dailyCache.v2`（Keychain），按天更新
- 小组件在次日 00:05 后请求刷新，实际时机仍受 iOS 系统策略控制

### 常见问题

- **显示登录或查询失败**：点击组件重新运行，会话过期时会自动重新登录；检查网络能否访问 `weixin.cdwater.com.cn`
- **内容超出组件高度**：请使用中号组件
- **想强制刷新**：在 Scriptable 中手动运行脚本；如需清缓存，删除 Keychain 中 `cdwater.dailyCache.v2`

---

## 二、华天兴能燃气（huatianxingneng_ranqi）

![华天兴能燃气费小组件截图](https://raw.githubusercontent.com/suzukua/widget/refs/heads/main/huatianxingneng_ranqi.png)

### 功能特性

- 自动登录并拉取燃气账单数据
- 展示最近表数、欠费状态、近几期账单、年度用量
- 失败时自动读取本地缓存，降低空白小组件概率
- 使用本地 Keychain 存储敏感信息（密码/会话/密钥）
- 支持小号/中号组件展示

### 配置

在脚本 `CONFIG` 中填写账号信息：

```javascript
const CONFIG = {
  baseURL: "http://wx.huatianxn.qjcode.com/api",
  account: "你的登录手机号",
  password: "你的登录密码",
  consNo: "", // 可选：指定燃气卡号；留空时尝试自动读取默认户号
  cacheKey: "huatian_gas_bill_cache_v3",
  color: "#51B6FF",
  refreshMinutes: 1440,
};
```

**密码不生效**：脚本会将密码写入 Keychain（键 `huatian_gas_widget_password_v1`）。只改 `CONFIG.password` 可能不生效，请删除 Keychain 旧值后重跑，或将 `PASSWORD_KEY` 改为新值（如 `huatian_gas_widget_password_v2`）。

### 数据与展示

- 数据：账户信息（欠费/户名）、账单记录（月份、用量、费用、表数）、最近抄表信息，字段以接口返回为准
- 展示：最近表数、欠费状态、本期与年累计用量、最近几期账单

### 缓存与刷新

- 本地缓存文件：`huatian_gas_bill_cache_v3.json`
- 刷新周期由 `refreshMinutes` 指定（默认 1440 分钟），实际受系统策略控制

### 常见问题

- **显示“等待账单数据”或“暂时无法连接燃气服务”**：先手动运行脚本看报错；检查账号密码与网络（`wx.huatianxn.qjcode.com`）；会话过期会自动重新登录
- **提示验证码**：先在网页端完成验证，再回 Scriptable 重试
- **数据与官方不一致**：以官方渠道为准，接口字段变更可能导致解析异常

---

## 安全说明

- 请勿将真实账号、密码、Token、Cookie 提交到公开仓库，仓库中仅保留示例配置
- 敏感值请在本地 Scriptable 中填写；若怀疑泄露，请立即修改密码并清理会话

## 免责声明

本项目为个人脚本工具，与成都供水、华天兴能燃气公司官方产品无直接关联，仅供学习与个人效率使用，风险由使用者自行承担。

## License

`huatianxingneng_ranqi.js` 中包含 `node-forge`（BSD-3-Clause）相关代码，请遵循对应许可证条款。你也可以为本仓库补充整体 LICENSE（如 MIT）。
