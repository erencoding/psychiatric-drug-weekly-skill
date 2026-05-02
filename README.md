# 🌈 每周精神药物审批速递 Skill

🌐 自动收集全球主要药监机构（FDA / NMPA / EMA）近一周内获批/上市的精神类药物审批动态，整理成飞书文档推送。

## 🚫 禁止事项

1. **禁止虚假信息**：药物名称、适应症、审批机构、日期、获批状态不得编造。
2. **禁止来源不明**：每条药物信息必须标注可靠来源。
3. **禁止遗漏关键数据**：本 Skill 核心价值在于全面性，不得跳过任何可检索到的精神药物信息。
4. **禁止猜测**：无法确认真实性时，标注"待确认"而非捏造。

## 核心功能

- **多源检索**：FDA / NMPA / EMA / 行业媒体（BioPharma Dive、Endpoints News）
- **药物分类覆盖**：抗抑郁药、抗精神病药、抗焦虑药、心境稳定剂、ADHD 药物、镇静催眠药、阿尔茨海默/认知药物、OCD/PTSD 药物
- **链接 100% 验证**：输出前验证所有来源，404 删除，403 替换为 DOI
- **双输出**：同时生成飞书文档 + 直接回复 Markdown

## 药物分类

| 分类 | 英文 |
|------|------|
| 抗抑郁药 | Antidepressants |
| 抗精神病药 | Antipsychotics |
| 抗焦虑药 | Anxiolytics |
| 心境稳定剂 | Mood Stabilizers |
| 镇静催眠药 | Hypnotics/Sedatives |
| ADHD 药物 | ADHD Medications |
| 抗痴呆/认知药物 | Anti-dementia/Cognitive Drugs |
| OCD/PTSD 药物 | OCD/PTSD Medications |

## 数据来源

| 来源 | 地址 |
|------|------|
| 🇺🇸 FDA 新药审批 | fda.gov/drugs/novel-drug-approvals-fda |
| 🇺🇸 FDA 生物制品 | fda.gov/vaccines-blood-biologics |
| 🇨🇳 NMPA 优先审批 | nmpa.gov.cn |
| 🇪🇺 EMA 新药审批 | ema.europa.eu/en/human-medicines |
| 🌐 BioPharma Dive | biopharmadive.com |
| 🌐 Endpoints News | endpointsnews.com |

## 使用方式

在 OpenClaw 中触发：
- "给我本周精神药物审批动态"
- "精神类药物最新获批"
- "每周精神药物速递"

## 输出格式

每条药物记录包含：
- 药物名称（通用名 + 中文）
- 适应症
- 审批机构与审批类型
- 审批日期
- 申报企业
- 来源链接

## 技术说明

- 检索方式：curl 静态抓取 + Playwright（当 JS 渲染不可用时）
- 链接验证：`curl -s -o /dev/null -w "%{http_code}" -L --max-time 10`
- 用户 open_id：`ou_13198dff86df39f443084c8dc460d89f`（曹俊）

## License

MIT