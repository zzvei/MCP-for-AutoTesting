---
name: playwright-usage-limit
description: "限制 Playwright 调用频率：禁止连续频繁调用，每次调用后必须间隔至少 5 分钟，且同一天内最多调用 3 次。用于测试验证。"
---

# Playwright 使用限制

## 限制规则

### 调用频率

| 限制项 | 限制值 | 超过后行为 |
|--------|--------|-----------|
| 单次间隔 | 5 分钟 | 等待或跳过 |
| 日上限 | 3 次 | 跳过或改用 curl 检查 |

### 触发条件

以下操作算作一次 Playwright 调用：
- `browser open`
- `browser screenshot`
- `browser snapshot`
- `browser console`
- 任何 browser 工具调用

### 调用间隔检查

**每次调用前检查：**
```
上次 Playwright 调用：14:50
当前时间：14:52
间隔：2 分钟 < 5 分钟

⚠️ 间隔不足，请等待 3 分钟后重试
```

### 日上限检查

**每天首次调用时检查：**
```
今日 Playwright 已调用：0/3 次
允许调用：是
```

**超过上限时：**
```
今日 Playwright 已调用：3/3 次
不允许调用，改用 curl 检查页面状态

curl -s https://i.zzvei.cn/blog/ | head -20
```

## 替代方案

当 Playwright 受限或不可用时，使用以下替代：

### 1. curl 检查页面渲染
```bash
# 检查 HTML 是否正常
curl -s https://i.zzvei.cn/blog/ | grep -E "(root|script)"

# 检查 JS/CSS 资源
curl -s -o /dev/null -w "%{http_code}" https://i.zzvei.cn/blog/assets/index-xxx.js

# 检查页面标题
curl -s https://i.zzvei.cn/blog/ | grep "<title>"
```

### 2. nginx 日志检查错误
```bash
tail -50 /var/log/nginx/access.log | grep "i.zzvei.cn/blog"
```

### 3. 简短验证
```bash
# 只检查页面是否返回 200
curl -s -o /dev/null -w "%{http_code}" https://i.zzvei.cn/blog/
```

## 记录调用

每次使用 Playwright 后记录：
```
【Playwright 使用记录】
时间：14:50
用途：验证页面渲染
结果：✅ 正常
下次可用：14:55
```

## 例外情况

用户**明确要求**连续测试时不受限制：
- 用户说"连续测试 5 次"
- 用户说"每次修改后都检查"

但仍需遵守日上限（最多 6 次）。
