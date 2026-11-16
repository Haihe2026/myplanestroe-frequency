# 交接：频次线首单选品单 → Codex 写规格

- 产品：跨境关键词排名助手（cross-border-keyword-ranking），频次线首单（测试单），P1
- 选品单：pipeline/products/cross-border-keyword-ranking/00-brief.md
- 调研：pipeline/research/2026-11-16-round-freq-01.md
- 状态：brief_ready（status.json 与本交接同批建立）
- SOP：docs/sop/sop-frequency-skill-auto.md（测试单现版草稿；定稿前按现版走）
- 流水线仓：Haihe2026/myplanestroe-frequency（只动这个仓；绝不动生图仓 Haihe2026/myplanestroe）
- 沟通仓：Haihe2026/myplanestroe-frequency-relay（只动这个仓；绝不动生图 relay）

## 任务

按 SOP B.2 写开发文档：01-dev-spec.md（按 A.7 的 A1 清单逐节写，章节顺序与 A1 清单一致）与 01-dev-spec.json（只放机器要用的摘要，按 B.2 模板，含 skill_id、display_name、version、lane、channels、billing、supplier、margin、scenes、samples、timeouts、endpoints、funnel_steps、sibling_skills、tests、decisions）。

## 写之前必做

- 在线重读 WorkBuddy 官方文档三页（open.workbuddy.cn/docs/skill、/docs/expert、平台概述），再写开发文档；仓库里的官方文档快照只作补充，不能当作已完成在线读取的证据。读不到就拦停（OFFICIAL_DOCS_UNREADABLE），发 relay 回报，不硬写。
- 先拉最新仓库再认领：把 status.json 的 stage 改成 spec_in_progress、owner 写 codex、claimed_at 写当前时间，提交推送成功后才开始干活。

## 固定规则（写进规格，不得违反）

- 支付页固定 https://myplanestore.com/membership/listing/，逐字核对，绝不能写成生图线的 https://myplanestore.com/membership/。
- 计费：业务单价 0.30 元/次，按实际有效结果数扣，没数据的不扣，一条有效结果都没有就不扣费；新线统一 0 次免费试用。已定事项按 SOP A.5 处理，不写入规格正文定价段。
- 合规：不接卖家后台、不爬需登录的卖家数据、不代刷排名、不承诺排名回升、不收集店铺账号密码订单等个人信息。
- 白标：用户可见的文案、图片 URL、报错、水印不得出现上游模型名/服务商名/域名/品牌。
- 主题 logo：写开发文档时一并用上游资源生成，保存到 02-package/dist/logo.png，记 provenance 与 audit；spec_ready 即表示 logo 已就位。
- 不编造：WorkBuddy 站内数字因登录墙未验到，写「未验到」，不编下载量与评分；SkillHub 站内数字以选品单为准。
- 规格退回上限 1 次；max_rounds 按尝试数计（每次尝试计 1 次）。

## 核心差值（写规格时守住）

- 只做多关键词×多日排名成表：用户粘贴关键词与排名记录（或由后端取数）→ 一张多关键词×多日的排名变化表，掉出首页与连续下跌标红。
- 不做关键词挖掘/选词，不做广告投放优化，不做 Listing 改写；不做竞品全套监控（价格/促销/新品），只做关键词排名跟踪；首版不做下跌自动提醒。
- 数据来源：用户粘贴＋后端自跑谷歌趋势/抓亚马逊等开放平台数据整理后供 skill 调用；后端共享与隔离不关 skill 开发的事。

## 完成后

写完规格后做 A1 自检，通过后置 spec_ready（更新 updated_at），log.md 追加一行，发 relay 交接给 muse（kind=handoff，source 指开发文档提交），交 Muse 复检。A1 不通过或需拦停时，按 SOP A.3/A.4 置 blocked（写清 reason_code、detail、needs、resume_to），并在 BLOCKED.md 追加一行，发 relay 回报。
