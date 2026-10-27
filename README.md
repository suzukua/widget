# 华天兴能燃气费小组件（Scriptable）

基于 **Scriptable** 的 iOS 桌面小组件，用于展示 **华天兴能燃气公司**燃气账单与用量信息。  
脚本文件：`huatianxingneng_ranqi.js`

## 截图展示

![华天兴能燃气费小组件截图](https://raw.githubusercontent.com/suzukua/widget/refs/heads/main/huatianxingneng.png)

## 功能特性

- 自动登录并拉取燃气账单数据
- 展示最近表数、欠费状态、近几期账单、年度用量
- 失败时自动读取本地缓存，降低空白小组件概率
- 使用本地 Keychain 存储敏感信息（密码/会话/密钥）
- 支持小号/中号组件展示

## 环境要求

- iOS 14+
- [Scriptable](https://scriptable.app/) 最新版

## 快速开始

1. 安装并打开 Scriptable  
2. 新建脚本，命名为：`huatianxingneng_ranqi`（名称可自定义）  
3. 将仓库里的 `huatianxingneng_ranqi.js` 全部内容复制到脚本  
4. 修改配置（见下方“配置说明”）  
5. 在 Scriptable 内先手动运行一次（用于初始化 Keychain 和会话）  
6. 回到桌面添加 Scriptable 小组件并绑定该脚本

---

## 配置说明

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

### 关于密码不生效（重要）

脚本会将密码写入 Scriptable Keychain（键名：`huatian_gas_widget_password_v1`）。  
如果你之前运行过脚本，只改 `CONFIG.password` 可能不会立即生效。

可用以下任一方式处理：

- 方式 A：删除 Keychain 中旧值后重跑脚本
- 方式 B：将 `PASSWORD_KEY` 改成新值（如 `huatian_gas_widget_password_v2`）再运行

---

## 数据与展示说明

脚本会请求以下类型信息（具体字段以接口返回为准）：

- 账户信息（欠费/户名等）
- 账单记录（月份、用量、费用、表数）
- 最近抄表信息（若接口可用）

组件典型展示：

- 最近表数（m³）
- 欠费状态（无/金额/待确认）
- 本期用量 + 年累计用量
- 最近几期账单（月份、用量、费用、表数变化）

---

## 缓存与刷新机制

- 本地缓存文件：`huatian_gas_bill_cache_v3.json`
- 组件刷新周期由 `refreshMinutes` 指定（默认 1440 分钟）
- iOS 小组件最终刷新时机仍受系统策略控制，不保证绝对实时

---

## 常见问题（FAQ）

### 1. 组件显示“等待账单数据”或“暂时无法连接燃气服务”
- 先在 Scriptable 里手动运行脚本查看是否报错
- 检查账号密码是否正确
- 检查网络是否可访问 `wx.huatianxn.qjcode.com`
- 如会话过期，脚本会尝试自动重新登录

### 2. 登录失败，提示验证码相关
部分场景网站可能触发图形验证码。  
请先在网页端完成验证后，再回 Scriptable 重试。

### 3. 修改了密码但还是旧密码
见上文“关于密码不生效（重要）”，清理 Keychain 或升级 `PASSWORD_KEY`。

### 4. 数据与官方页面不一致
以官方渠道显示为准。接口字段变更可能导致解析异常，需要更新脚本。

---

## 安全说明

- 请勿将真实账号、密码、Token、Cookie 提交到公开仓库
- 建议仓库中仅保留示例配置，敏感值在本地 Scriptable 中填写
- 若怀疑泄露，请立即修改密码并清理会话

---

## 免责声明

本项目为个人脚本工具，与华天兴能燃气公司官方产品无直接关联。  
仅供学习与个人效率使用，风险由使用者自行承担。

---

## License

脚本中包含 `node-forge`（BSD-3-Clause）相关代码，请遵循对应许可证条款。  
你也可以为本仓库补充整体 LICENSE（如 MIT）。
