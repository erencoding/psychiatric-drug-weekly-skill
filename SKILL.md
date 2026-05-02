---
name: psychiatric-drug-weekly
description: 每周收集全球最新获批/上市的精神类药物审批动态，包括FDA、NMPA、EMA等主流药监机构的优先审批与新药批准信息，并整理成飞书文档推送。
version: "1.0"
author: Judy (朱迪)
license: MIT
---

# 🌈 每周精神药物审批速递 Skill (v1.0)

## 目标

收集全球主要药监机构（FDA / NMPA / EMA）近一周内获批/上市的精神类药物，覆盖：
- 抗抑郁药（Antidepressants）
- 抗精神病药（Antipsychotics）
- 抗焦虑药（Anxiolytics）
- 心境稳定剂（Mood Stabilizers）
- 镇静催眠药（Hypnotics/Sedatives）
- ADHD 药物
- 抗痴呆/认知药物（针对阿尔茨海默等）
- 镇痛类精神药物（含精神科适应症者）

---

## ⚠️ 绝对禁止事项

1. **禁止虚假信息**：药物名称、适应症、审批机构、日期、获批状态均为事实性信息，不得编造。
2. **禁止来源不明**：每条药物信息必须标注可靠来源。
3. **禁止遗漏关键数据**：本 Skill 核心价值在于全面性，不得跳过任何可检索到的精神药物信息。
4. **禁止猜测**：无法确认真实性时，标注"待确认"而非捏造。

---

## 执行流程

### Step 1 · 计算时间范围

```bash
NOW=$(date '+%Y-%m-%d')
WEEK_AGO=$(date -d "7 days ago" '+%Y-%m-%d')
echo "时间范围: $WEEK_AGO ~ $NOW"
```

---

### Step 2 · 多源检索（必须全部执行）

#### 2A · FDA 新药审批

**优先审批（Priority Review / Breakthrough / NMPA优先）**

URL: `https://www.fda.gov/drugs/novel-drug-approvals-fda/novel-drug-approvals-2026`

抓取页面，筛选以下关键词药物：
- depression / antidepressant
- schizophrenia / antipsychotic
- anxiety / anxiolytic
- bipolar / mood stabilizer
- ADHD / methylphenidate / stimulant
- insomnia / sleep
- Alzheimer / dementia / cognitive
- psychiatric / psychotropic
- OCD / PTSD / PTSD
- autism / ASD

**具体步骤**：
```bash
curl -s -L --max-time 20 "https://www.fda.gov/drugs/novel-drug-approvals-fda/novel-drug-approvals-2026" | \
  grep -i "approval\|novel\|drug\|psychiatric\|depression\|schizophrenia\|anxiety\|bipolar\|ADHD\|insomnia\|Alzheimer" | \
  head -200
```

#### 2B · FDA 生物制品审批（CBER）

URL: `https://www.fda.gov/vaccines-blood-biologics/biologics-approved-products`

同样筛选精神相关关键词。

#### 2C · NMPA 优先审批公示

URL: `https://www.nmpa.gov.cn/yaowen/ypjg/ypzgzjgz/index.html`（优先审批品种公示）

关键词：精神 / 抑郁 / 抗精神病 / 焦虑 / 失眠 / 阿尔茨海默 / ADHD

#### 2D · EMA 新药审批

URL: `https://www.ema.europa.eu/en/human-medicines`

筛选 human medicines approved recently 页面，筛选精神类关键词。

#### 2E · 行业媒体补充（确保全面）

以下来源作为补充，不可跳过：

| 来源 | 搜索关键词 |
|------|-----------|
| BioPharma Dive | psychiatric drug approval 2026 |
| Endpoints News | psychiatry / antidepressant / antipsychotic |
| Drugs.com new drug approvals | psychiatric |

```bash
# 搜索 BioPharma Dive
curl -s -L --max-time 15 "https://www.biopharmadive.com/" | \
  grep -i "psychiatric\|antidepressant\|schizophrenia\|drug approval" | head -50

# 搜索 Endpoints News  
curl -s -L --max-time 15 "https://endpointsnews.com/" | \
  grep -i "psychiatric\|depression\|drug\|FDA approval" | head -50
```

---

### Step 3 · 药物信息结构化

每条药物记录必须包含以下字段：

| 字段 | 说明 |
|------|------|
| 药物名称 | 通用名（英文）+ 中文（可查则附） |
| 商品名/研发代号 | 如有 |
| 适应症 | 获批的具体精神类适应症 |
| 审批机构 | FDA / NMPA / EMA / 其他 |
| 审批类型 | New Drug Approval / Priority Review / Biological License / 优先审批 |
| 审批日期 | 具体日期 |
| 申报企业 | 药企名称 |
| 药物类型 | 化学药 / 生物制品 / 疫苗（精神适应症相关） |
| 来源链接 | 必须可访问 |

---

### Step 4 · 数据验证

对每条来源链接进行验证：

```bash
for url in "${links[@]}"; do
  code=$(curl -s -o /dev/null -w "%{http_code}" -L --max-time 10 "$url")
  echo "$code $url"
done
```

| 状态码 | 处理方式 |
|--------|---------|
| 200 OK | ✅ 采用 |
| 301/302 重定向 | 👉 跟随重定向后验证 |
| 403 Forbidden | ⚠️ 替换为官方 PDF 或 PDF 存档 |
| 404 Not Found | ❌ 删除该条，注明"来源失效" |

---

### Step 5 · 输出格式

#### 5A · 飞书文档

**标题**：`🌈 每周精神药物审批速递 · {YYYY-MM-DD}`

**文档结构**：
```
📅 时间范围：{WEEK_AGO} ~ {NOW}

🇺🇸 FDA 新药审批
  [表格：药物名 | 适应症 | 审批类型 | 日期 | 企业 | 链接]

🇨🇳 NMPA 优先审批
  [表格：药物名 | 适应症 | 审批类型 | 日期 | 企业 | 链接]

🇪🇺 EMA 新药审批
  [表格：药物名 | 适应症 | 审批类型 | 日期 | 企业 | 链接]

🌐 行业补充（BioPharma/Endpoints等）
  [表格：药物名 | 来源 | 适应症 | 日期 | 链接]

📊 数据统计
  - FDA 审批：X 条
  - NMPA 审批：X 条
  - EMA 审批：X 条
  - 行业补充：X 条
  - 合计：X 条

⚠️ 备注与来源验证
```

创建文档（v1 API）并授予曹俊权限：

```bash
# Step 1: 创建文档
lark-cli docs +create \
  --title "🌈 每周精神药物审批速递 · {YYYY-MM-DD}" \
  --markdown @content.md \
  --doc-format markdown

# Step 2: 授予权限
lark-cli drive permission.members create \
  --params '{"token":"DOC_ID","type":"docx"}' \
  --data '{"member_id":"ou_13198dff86df39f443084c8dc460d89f","member_type":"openid","perm":"full_access","type":"user"}' \
  --as bot
```

#### 5B · 直接回复

每条药物：药物名 | 适应症 | 审批机构 | 日期 | 一句话简评

---

### Step 6 · 补充说明

- **FDA 页面可能存在 JavaScript 动态渲染**，若 curl 无法获取内容，使用 Playwright 替代：
  ```bash
  node -e "
  const { chromium } = require('/usr/lib/node_modules/playwright');
  (async () => {
    const browser = await chromium.launch({ headless: true });
    const page = await browser.newPage();
    await page.goto('URL', { waitUntil: 'networkidle', timeout: 30000 });
    const content = await page.content();
    console.log(content);
    await browser.close();
  })();
  "
  ```

- **全文检索原则**：宁可多检索，不可漏检。关键词覆盖要全面。
- **时区注意**：FDA 以美国东部时间为准；NMPA/EMA 以当地时间为准。
- **用户 open_id**：`ou_13198dff86df39f443084c8dc460d89f`（曹俊，固定值）

---

## 质量要求

1. **全面性**：每个数据源必须全部检索，不得提前终止
2. **准确性**：所有字段必须是已确认的事实，不得使用"可能"、"估计"等词汇
3. **时效性**：仅收录近 7 天内发布的信息
4. **可追溯性**：所有药物必须有原始来源链接
5. **双输出**：飞书文档 + 直接回复