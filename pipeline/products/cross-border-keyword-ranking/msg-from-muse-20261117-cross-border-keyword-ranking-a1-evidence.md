频次流水线（仓库 Haihe2026/myplanestroe-frequency，SOP docs/sop/sop-frequency-skill-auto.md）。

Codex，A1 缺件已补，恢复重跑。

负责人 2026-11-17 定方案 A：首个需要新写后端的新品类，A1 不要求已有后端源码与已测补丁；书面合同＋已有结算证据＋有来源的成本模型＋已登记 logo 四项齐全即视为 A1 通过；后端补丁与 Mock 测试改列开发阶段交付物，部署前拦停。该例外已写进 SOP 正文（A1 清单后）与定稿记录第 7 项，提案已记 pipeline/sop-proposals.md。

本次补的证据（pipeline/products/cross-border-keyword-ranking/01-evidence/）：
- b01-settlement-evidence.md：listing 支付页 2026-11-17 10:21–10:22 在线核验通过（3 档套餐＋0.30元/次）＋已有账单链路真实跑通过（2026-11-13、11-16 两笔真实订单已到账发放）。注意：页面注明“微信扫码关联后，首次免费体验1次”，与 SOP 的 0 次免费试用口径须在规格决策记录补一句写明（技能侧不自行发免费额度）。
- b02-adapter-contract.md：排名适配器 ranking-public-adapter 书面合同（Trends 只出 interest_score；亚马逊只出可核正整数名次，page_size 永不设常数；有效单位＝去重后可核观测行；部分结算；幂等）。附诚实未知：亚马逊分页语义未验、Trends 库已归档只读。
- b03-cost-model.md：有来源的成本模型。单价 0.30 元/有效单位，毛利阈值 0.6，成本上限 0.12 元；采集单次费用为 0（有来源），计算存储为已有服务器边际成本；最坏情况毛利恰好 0.6，实际高于阈值；开发阶段部署实测复核。
- b04-logo-registration.md＋logo.png：已登记 logo 资产，512×512 PNG，184,365 字节，SHA256 见登记文件，源文件 logo-source.webp 同目录。

状态已恢复 brief_ready（resume_to）。请按修改后的 SOP 重跑 A1：通过则置 spec_ready（规格决策记录须补首次免费口径那句）；仍有缺件则写清具体缺什么，不要只写笼统结论。后端补丁仍是开发阶段的活，本轮不写补丁、不部署。
