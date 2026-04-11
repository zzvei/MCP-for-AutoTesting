---
name: playwright-token-guard
description: "限制 Playwright 重型观测操作，防止 token 过度消耗。禁止默认 full-page，禁止同轮多次截图，snapshot/screenshot 二选一，先轻后重检查。"
---

# Playwright Token 守卫

## 硬规则（四禁止）

### 规则一：禁止默认 full-page

```bash
# ❌ 禁止
browser screenshot --full-page

# ✅ 允许（仅当用户明确要求时）
browser screenshot  # 默认首屏
browser screenshot --viewport  # 指定区域
```

### 规则二：同一轮禁止多次 screenshot

```bash
# ❌ 禁止
browser screenshot  # 第一次
修改代码
browser screenshot  # 第二次 ← 禁止
修改代码  
browser screenshot  # 第三次 ← 禁止

# ✅ 允许（每轮最多一次）
browser screenshot
修改代码
```

### 规则三：snapshot 和 screenshot 二选一

```bash
# ❌ 禁止
browser snapshot
browser screenshot  # 不能同时用

# ✅ 允许（各一次，或只用一个）
browser snapshot --compact
# 或
browser screenshot
```

### 规则四：先轻后重

**检查优先级固定为：**

```
优先级 1（最轻）：curl / headless check
  ↓ 能判断就停止
优先级 2：DOM 片段检查 / console 日志
  ↓ 能判断就停止  
优先级 3（最重）：compact snapshot
  ↓ 只有布局/视觉问题才继续
优先级 4（最重）：screenshot
```

## 具体操作

### 轻量检查（优先使用）

```bash
# 检查页面是否 200
curl -s -o /dev/null -w "%{http_code}" https://i.zzvei.cn/blog/

# 检查 HTML 结构
curl -s https://i.zzvei.cn/blog/ | grep -E "(root|script)"

# 检查关键元素是否存在
curl -s https://i.zzvei.cn/blog/ | grep -q "Vue 与 React" && echo "文章存在"
```

### 中量检查（必要时使用）

```bash
# 检查控制台错误
browser console

# 检查特定 DOM 元素
browser snapshot --compact
```

### 重型检查（仅最终验收）

```bash
# 视觉验收
browser screenshot

# 布局诊断
browser screenshot --full-page  # 仅当用户要求
```

## 检查流程

每次使用 Playwright 前必须：

```
1. 预估检查类型：
   - 页面是否正常？ → 用 curl
   - 控制台有错？ → 用 browser console
   - 布局问题？ → 用 screenshot
   - 视觉验收？ → 用 screenshot

2. 如果上一轮已用过重型检查 → 跳过本轮

3. 如果 curl 能判断 → 直接返回，不继续
```

## 违规示例

```bash
# ❌ 违规案例
迭代 1:
  browser snapshot         # 2万 token
  browser screenshot      # 10万 token ← 禁止
  edit file
  browser screenshot      # 又 10万 token ← 禁止
  → 本轮总计: 22万 token

# ✅ 合规案例
迭代 1:
  curl 检查 (100 token)  ← 轻量
  → 本轮总计: 100 token
  
迭代 2:
  browser console (500)   ← 中量
  → 本轮总计: 500 token

迭代 3:
  browser screenshot      ← 仅最终验收
  → 本轮总计: 10万 token
```

## Token 消耗对比

| 检查方式 | Token 消耗 | 适用场景 |
|----------|------------|----------|
| curl | 100 | 页面是否 200 |
| grep HTML | 500 | 元素是否存在 |
| browser console | 500 | JS 错误检查 |
| snapshot --compact | 5千 | DOM 结构 |
| snapshot | 3万 | 完整 DOM |
| screenshot | 10万 | 视觉验收 |
| screenshot --full-page | 15万 | **禁用** |

**节省：99%+**
