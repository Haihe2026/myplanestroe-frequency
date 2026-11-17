# 开发文档候选：跨境关键词排名助手 1.0.0

频次流水线（仓库 Haihe2026/myplanestroe-frequency，SOP docs/sop/sop-frequency-skill-auto.md）。

**文档状态：可评审设计候选，A1未通过，不得据本候选置spec_ready或宣称fast已可复用。** 本次实际完成范围是规格设计与资料核对。业务源码/资源/成本证据及实际logo仍缺；本稿把已定要求、待实现契约、待部署与未验证分开，不用SOP示例冒充现有后端。

- 技能ID：`cross-border-keyword-ranking`；中文名：跨境关键词排名助手；英文名：Cross-border Keyword Rank Tracker；目标包版本1.0.0；当前渠道仅`workbuddy-skill`。
- 固定任务：产品仓`9dfa8c99357c6b4bf31c0bf28bf7cb5019138fb1`的交接信；接手SOP/config/brief固定`5b6745fe6b35335bbc33ce24614dd0a48dda83ab`；产品认领`e55e0508245b583307ba5eb2c2596b3c0970b530`，当时`spec_in_progress/owner=codex`。
- 需求证据只采用该固定brief记载；其中SkillHub数值属于Muse于2026-11-16核验的转录，本次不冒称重查。WorkBuddy站内数字因登录墙为“未验到”。公开平台同名可用性未验证，以负责人手动上传反馈为准。
- JSON配套：`01-dev-spec.json`；所有`null`成本/路径表示缺件，不是0成本、0价格或已部署；JSON `tests.execution_status=NOT_RUN`不代表A2/A3通过。

## 1. 立项与范围（A1第1项）

目标用户为需要按天比较多个关键词排名的跨境卖家和独立站运营。输入用户粘贴/导入的关键词排名记录；后端公开数据分支只有在真实适配器和字段证据齐备后才启用。输出多关键词×多日表、原始观测、异常与缺口清单、可核首页变化/连续下降标记和可恢复任务标识。

核心差值是稳定成表、按同一口径比较、缺失不编造、同任务续接与真实计费；不是关键词挖掘、广告优化、Listing文案或排名提升服务。用户问“是我链接还是大盘”时只给观测变化及数据局限，不据排名表输出因果诊断。

技能name沿已建目录和固定请求`cross-border-keyword-ranking`，本频次仓完整树只有本产品目录，未发现同仓同ID；不为了补一般`mps-`前缀另造SKU。跨市场显示名唯一性未知，不宣称注册/审核已通过。核心词：关键词排名、排名变化；触发词和用户原话见下节。

## 2. WorkBuddy官方在线读取记录（A1第2项）

本候选开写前重新在线读取以下三页，未用仓库快照替代。技能页用于技能包根目录、references/scripts引用及frontmatter；专家页与概述用于明确渠道隔离和审核流程。本轮不制作专家包，不用专家的displayName字段代替技能display_name。


| 日期时间（北京时间） | URL | 结果 | 字节/SHA256 |
|---|---|---|---|
| 2026-11-17T10:02:23+08:00 | https://open.workbuddy.cn/docs/skill | HTTP 200 | 284943 / `c82b87f6ee638c564a28436cc76b7d0d7135655fa73adf215249c9b88af5210f` |
| 2026-11-17T10:02:23+08:00 | https://open.workbuddy.cn/docs/expert | HTTP 200 | 351247 / `f765906bf4c7565f9d6e640cc701408eff11bb02f8203b3d24f6c0e69b7580e3` |
| 2026-11-17T10:02:23+08:00 | https://open.workbuddy.cn/docs/what-is-open-platform | HTTP 200 | 259256 / `6e4f82f8dfbd3d1102fb941c60337395c052af61aebc8d02321c7fc8adec2f48` |


源码式可执行文件、API合同和文字样例分别放scripts/references，正文用`@references/...`引用；不硬编码MCP工具或密钥。frontmatter八必填字符串为name、display_name、display_name_en、description、description_zh、description_en、version、author；中英examples各3条，逐对同场景/输出要求。

## 3. 场景路由与用户原话（A1第3项）

账户/费用/充值/历史/工单优先于场景关键词；“继续”必须先读取原任务，不新建计划。域内请求一律调用本技能脚本，不凭记忆直接编表；超范围按已核相邻技能能力转介，未核slug不编调用命令。


| 场景key | 用户口语关键词 | 推荐参数/边界 | 输出 |
|---|---|---|---|
| daily_organic | 今天排名、昨天首页、自然排名 | {"rank_type": "organic", "window": "provided_dates"} | 关键词×日期名次、最近变化、已证首页状态、连跌次数 |
| launch_baseline | 新品三天、主词爬坡、一周基线 | {"rank_type": "organic", "window": "up_to_7_provided_dates"} | 提供的各日名次、中位数/最好/最差名次、有效观测数、缺口 |
| campaign_frontpage | 活动词、大促前、有没有进首页 | {"rank_type": "organic", "require_explicit_page_evidence": true} | 活动关键词、日期名次、来源给出的page_index、首页状态、证据说明 |
| drop_drilldown | 掉到第几页、连续下跌、排名掉了 | {"decline_run_min_transitions": 2, "require_adjacent_dates": true} | 日期序列、逐日变化、连续下降转换数、页位证据；不输出原因诊断 |
| competitor_compare | 竞品ASIN、我排第几、竞品排第几 | {"comparison": "same_keyword_same_market_same_metric"} | 关键词、日期、目标A名次、目标B名次、差值与缺口 |
| seo_weekly | SEO周报、谷歌排名、搜索位置 | {"platform": "google", "metric_grouping": "strict_separate"} | 自然名次周表；average_position另表原单位；interest_score仅输入说明 |
| ad_organic_compare | 广告停了、广告词、自然排名稳不稳 | {"rank_type": "separate_sponsored_and_organic"} | 同日期并列广告/自然记录、来源及缺口；无因果结论 |
| multi_market | 美国站、德国站、日本站、多站点 | {"group_by": ["market", "locale", "entity_id", "metric_kind"]} | 每站点独立名次序列与变化，不把异站排名拼成同一趋势 |
| quarter_review | 这个月整体、季度复盘、连续几天走势 | {"window": "provided_history_only"} | 指定期间有效日数、缺日、最好/最差、起末变化及红标 |
| new_word_baseline | 新词跑一周、新词基线、前三页哪天 | {"window": "up_to_7_provided_dates", "scheduled_collection": false} | 已提供基线日期、名次、已证页面与待补日期 |


用户原话路由（其中拒绝/账户分支也必须测试）：
| ID | 用户原话 | 路由 |
|---|---|---|
| U01 | 我这个词昨天还在首页，今天怎么找不到了 | daily_organic |
| U02 | 帮我看下这20个词这周的排名变化 | daily_organic |
| U03 | 新品上了三天，主词爬到第几页了 | launch_baseline |
| U04 | 大促前这几个活动词有没有进首页 | campaign_frontpage |
| U05 | 这个词掉了三天，是我链接还是大盘出问题 | drop_drilldown |
| U06 | 竞品在这个词上第几，我排第几 | competitor_compare |
| U07 | 帮我出张横着日期竖着词的表 | daily_organic |
| U08 | 掉出首页的标出来 | campaign_frontpage |
| U09 | 广告停了之后自然排名稳不稳 | ad_organic_compare |
| U10 | 美国和德国站同一个词差多少 | multi_market |
| U11 | 这个月排名整体涨还是跌 | quarter_review |
| U12 | 新词先看一周基线 | new_word_baseline |
| U13 | 不要只有一个数字，我要连续几天走势 | daily_organic |
| U14 | 排名掉了自动告诉我 | OUT_OF_SCOPE_AUTO_ALERT |
| U15 | 我的词太多，一个一个查太费时间 | daily_organic |
| U16 | 这个词有没有进前三页，哪天进去的 | new_word_baseline |
| U17 | 做谷歌SEO这周的位置表 | seo_weekly |
| U18 | 充值，别重新介绍，继续我上次的表 | ACCOUNT_THEN_RESUME |
| U19 | 帮我找几个新关键词 | OUT_OF_SCOPE_KEYWORD_MINING |
| U20 | 查一下这次到底扣了多少，不要猜 | ACCOUNT_HISTORY |


## 4. 输入输出表结构、计算与文字样例（A1第4项）

### 4.1 归一化输入与来源口径

两种输入模式必须可见区分：`user_records`仅处理用户提供记录、不接外部供应商；`backend_public`仅接受已登记、已绑定真实接口的服务端公开观测，当前未启用。不能把一份Trends兴趣数据换名当亚马逊/谷歌实际名次。


| 字段 | 类型 | 必须遵守 |
|---|---|---|
| source_kind | enum | user_records/backend_public/synthetic；不混称服务端采集 |
| source_id | string | 稳定采集来源/口径ID；原文件SHA及行号另存evidence_ref，不用每个文件hash作来源分组ID |
| platform | enum | amazon/google/other_declared，不推断 |
| market | string | 用户或来源明确站点 |
| locale | string_or_null | 来源给出，不自行翻译合并关键词 |
| entity_id | string | 目标ASIN或公开页面标识，不含卖家凭证 |
| keyword | string | 原始关键词；保留Unicode，导出防公式注入 |
| rank_type | enum | organic/sponsored/declared_other，严格分组 |
| observed_on | date | 来源声明观测日；未知日期先补，不用文件修改日替代 |
| observed_at | datetime_or_null | 带时区采集时间；缺失不编造 |
| source_timezone | string_or_null | 来源日界线；日历比较前确认 |
| metric_kind | enum | rank_ordinal/average_position/interest_score，永不混为一列 |
| rank_value | positive_integer_or_null | 仅rank_ordinal用；bool、0、负值非有效名次 |
| metric_value | finite_number_or_null | 平均位置/兴趣保持原单位，不转整数名次 |
| observation_status | enum | observed/not_found/missing/capped/ambiguous/invalid |
| page_index | positive_integer_or_null | 来源显式页面；无证据不填 |
| page_size | positive_integer_or_null | 仅来源明确且验证相同分页语义时可推页，不设平台常数 |
| coverage | object_or_null | 搜索深度、过滤器、采样范围及有效期；未找到必须给覆盖说明 |
| evidence_ref | string_or_null | 原记录行或后端来源证据；不输出含密钥链接 |


source_id表示稳定采集口径（同来源、相同排序/过滤/采样规则），不是逐个文件hash或逐条证据ID。多日从不同文件导入时，用户确认同采集口径后使用同source_id，原文件SHA及行号分别保留在evidence_ref；不同来源不能靠同名关键词自动拼接。分组键包含source_id/platform/market/locale/entity_id/keyword/rank_type/metric_kind/source_timezone；不同渠道、站点、对象、来源口径不跨组计算趋势。同一观测key的完全相同记录去重，重复行不增加有效单位。相同key值冲突先进入异常清单，用户明确选择/更正后才冻结计划；不得按文件顺序或最新文件静默覆盖。日期不完整或日界线不明先补齐，不用文件修改时间当观测日。

`rank_ordinal`仅为有来源的正整数名次；bool、0、负数、NaN、无穷、字符串“未找到”不能变成名次。`average_position`是聚合平均位置，保留原浮点单位独立展示，不当某次SERP第几名；`interest_score`是相对兴趣，独立说明，不进入名次差/首页/连跌计算，也不能因兴趣下降推断排名下降。参考：Google Trends FAQ https://support.google.com/trends/answer/4365533 ，Search Console performance说明 https://support.google.com/webmasters/answer/7042828?hl=en 。

六态定义：`observed`为该metric有可核数据；`not_found`为来源明确查询过且在声明覆盖内未找到；`missing`为未提供/未获取；`capped`为覆盖或额度截断；`ambiguous`为冲突/口径不清；`invalid`为格式或业务校验失败。后五态不编造rank=0、最大名次或最后一页。没有有效名次的任务不发付费提交；非ordinal指标是否独立收费必须有对应已登记合同，当前不能自行推定。

### 4.2 表格、标红与去重规则

- 主表：关键词/对象/站点/渠道/来源口径为行，日期为列；cell包含名次或明确状态。附最近变化、连续下降转换数、首页状态和highlight_reason。
- 差值=`previous_rank-current_rank`：正数改善、负数下降；只有同组可比有效观测计算。连续下降默认至少两次相邻日际下降（至少3日）；缺日、无效状态或持平中断，不能把3个零散日期叫连续三天。
- 首页：来源明确page_index=1，或分页语义已核且page_size明确时才能计算。无此证据标“未获取”，不默认Amazon48或Google10。掉出首页必须有前后可核状态，不能把当前missing自动当掉出首页。来源的有界not_found可显示其覆盖说明，仍不冒充真实名次。
- HTML表对“已证掉出首页”和“达到连续下降阈值”的单元格标红，并给文字原因/图例，不能只靠颜色。缺失、未找到、受限、冲突用中性缺口标记，不伪装确定排名。
- CSV为UTF-8 BOM，无法承载单元格颜色，保留highlight_reason列；HTML负责可见红标。数值右对齐、日期/站点稳定格式、关键词左对齐；不可让不同指标共享一个无单位的“排名”列。
- 导出`rank-table.html`、`rank-table.csv`、`observations.csv`、`exceptions.csv`、`manifest.json`；摘要注明有效行、缺失、冲突、覆盖及真实已知费用。每份文件记录SHA/字节；原始记录与导出不覆盖已有文件。CSV中以`= + - @`等开头的文本按文本转义，防公式执行。
- 输出到显式可见结果目录`cross-border-keyword-ranking-results/<task_id>/`；Markdown路径正斜杠，空格和#百分号编码，读取后仍指向实际文件。凭据和内部状态不能作为可下载结果。

### 4.3 每场景至少一条文字样例

以下均为“模拟：”文字fixture，标is_test=true；不是真实市场查询，不产生收费，不生成示例图。源码实施时保存到references/samples.md。


| 场景 | 输入文字 | 期望输出 |
|---|---|---|
| daily_organic | 模拟：同一关键词同站点同目标，D1名次3/page_index=1，D2名次7/page_index=2。 | 两日列分别3、7，变化-4，明确掉出首页标红；两个page_index为模拟输入，不是平台默认页容量。 |
| launch_baseline | 模拟：D1=30、D2=22、D3=18，其余4日缺失。 | 仅展示3日观测，中位数22，最好18、最差30，4日未获取；不补历史、不声称已跟踪7日。 |
| campaign_frontpage | 模拟：词A rank=8且page_index=1；词B rank=8但无page信息。 | A首页=是；B首页=未获取，不能用默认10/48个每页代推。 |
| drop_drilldown | 模拟：相邻三日名次5→8→12，来源未提供页面。 | 连续下降转换数2标红；页位未知；不能据此认定链接故障或大盘下跌。 |
| competitor_compare | 模拟：同词同日同站目标A=6、目标B=11。 | A/B分别6/11，A比B靠前5位；缺少来源时不自动抓卖家后台。 |
| seo_weekly | 模拟：SERP ordinal=8；另有Search Console average_position=8.4和Trends interest_score=80。 | 8进入名次表；8.4独立标平均搜索位置；80标相对兴趣，不当第80名。 |
| ad_organic_compare | 模拟：D1 sponsored=2、organic=20；D2仅organic=18。 | 自然改善2位，广告D2未获取；不声称停广告导致自然改善。 |
| multi_market | 模拟：同词US=10、DE=4，分别有同日记录。 | 展示两个站点组；不计算US→DE的跨日改善6位。 |
| quarter_review | 模拟：季度起日=40、末日=25，中间只有5个有效日。 | 起末改善15位，披露总共7个有效日；不伪造完整季度曲线。 |
| new_word_baseline | 模拟：新词已有D1=90、D2=70，用户要求后续7天。 | 只整理已有两日并列缺日；首版不安排自动采集/下跌提醒，不宣称已跑后5日。 |


## 5. 主题logo方向及真实状态（A1第5项）

方向：深靛蓝底、琥珀色关键词格点、青绿色多日趋势线与有纵深的表格页，表现时间序列和缺口辨识；小尺寸清晰，不是平面ico，不复制平台商标，不出现供应商/模型/域名。拟输出`02-package/dist/logo.png`，512×512 PNG、≤500000B，每技能1张。

当前实际状态：未生成、文件不存在、hash/provenance/audit为空。本聊天已有每技能1张登记上游长期授权，无需再次询问授权；但本单未提供可用登记资产ID、固定资源来源、安全调用方式和成本，不能把通用CLI安装当资源登记，不能调用未登记fallback或从其他流水线补资源。授权不是资源已可调用的证据。本项A1尚不能通过。

## 6. 支付页面与后端接入（A1第6项）

### 6.1 唯一支付入口与钱包

频次支付入口逐字固定为 **https://myplanestore.com/membership/listing/**。关联/充值链接必须由本技能实际服务端按skill签发，客户端校验HTTPS、允许的用户行动路径、host和时效后原样复制，不能自行拼签名参数或复用聊天旧链接。页面档位来自已核服务端plans；业务单位与单价从已核status/pricing读取，两者不能混为一件事。本次未访问真实支付页/下单/读取实时plans，实际档位与当前业务配置均未验证。

本技能独立人民币钱包，金额以非bool的非负整数“分”表示，充值按1:1记账，0次免费试用。不能说跨技能共享余额，也不向用户把人民币换算成生图次数。对客不硬写金额；内部已定价格要求仅写在决策D02，不能当服务端实测配置。

按用户能理解的有效结果收费，不按HTTP调用计费。候选单位为原任务去重后、来源可核、口径有效的ordinal观测；实际后端计费单位/部分结果规则必须与固定合同绑定，不在合同未核时先请求扣费。一条有效结果都没有不收费；零结果、档案缺失、上游失败、字段映射失败、额度保护五类逐项0费。无外部数据分支也必须有服务端核验和结算，不能客户端生成表后自报扣费成功。

### 6.2 鉴权、关联与原账户恢复

客户端只保存本技能安装身份及该服务颁发的运行凭据，不持有上游Key。具体身份长度、token格式、Authorization方案、挂载前缀、恢复响应均等待源码，不能照其他产品猜字段。账号方案A：脚本取得一次性服务端关联链接，浏览器原账户确认，新对话读取同一原身份续接；禁止在聊天索要手机号、验证码、密码、支付信息或让模型读凭据文件。

无账户的status/balance/topup要通过已核quick-entry给真实关联出路；已绑定账号再取本skill的listing充值链接。401只清除被确认失效的运行凭据，保留原稳定身份、原task和原幂等键再走原账户恢复；持续失效如实等待，不新建钱包来绕过恢复。凭据/状态原子私密写入，POSIX目录700/文件600，Windows同用户/SYSTEM等价ACL与非跟随路径保护；这些是待实现要求，不是本次完成了客户端安全测试。

### 6.3 订单、回调、账本、幂等与失败续接

部署方必须提供实际回调路径、支付方签名算法/公钥引用与验签源码。验签后核订单、金额、币种、账号与skill；不匹配拒绝，不以浏览器跳转或用户说“付好了”当到账。支付方事件/交易号/订单按实际唯一约束幂等入账，钱包更新与流水同一事务，重复回调不重复充值，余额不为负。客户端只读取真实服务端状态，本规格不实施核款、退款或财务迁移。

每次可能收费动作先冻结task_id、标准化输入SHA、场景、有效单位合同、quote版本/有效期、用户同意的预算和operation_key。幂等至少绑定本skill、已认证账户、原task、动作与同一参数；具体索引/命名空间按固定实现确定，不据本稿直接改表。原任务同key重放先还原原结果/账单，新增费用0；查询结果cache只在另经两次确认的新任务中才可按已核合同形成新收费，不能盖过原任务幂等。

费用上限属于原任务累计费用，续接、重试、并发与充值不重置。报价/范围增加或过期后尚未提交的收费动作重新复述并明确同意；已提交但结果未知先查原操作，不能换key重做。已收到上游HTTP200仍须映射和有效结果/五类0费判定，不能立刻扣费。部分成功仅按实际合同有效单位与原账单结算，其余保留缺项与原因；预算不足停在原任务并导出可用部分。

网络超时/5xx应答丢失不等于未受理：优先查询原操作。只有固定接口证明同key安全重放才可有界重试，宿主和脚本共享次数，不能再叠加“同命令随便重跑”。用户“继续/已充值”恢复原task和原key，不覆盖原输入或重新付费提交。费用只读取原账户/skill/task的真实消费账单；账单缺失/null、错任务、bool/负数/非法金额保持未知，不用余额差或本地结果行数冒充扣费证据。

### 6.4 现行接口证据与待绑定清单

本次两频次仓完整Git树无业务后端源码。下表路径只来自SOP文字约定，**不是已确认存在的部署接口**；实际主机/前缀/字段/超时/错误语义仍为空。接口绑定前不能以fast“复用即可”进入实现。


| operation | 方法/路径（SOP文字） | 来源位置 | 当前状态 |
|---|---|---|---|
| auth_start | POST /auth/start | SOP L440 | 需求存在；实现/部署/实际挂载未核 |
| auth_poll | POST /auth/poll | SOP L440 | 需求存在；实现/部署/实际挂载未核 |
| pricing | GET /pricing | SOP L441 | 需求存在；实现/部署/实际挂载未核 |
| account_status | GET /me | SOP L441 | 需求存在；实现/部署/实际挂载未核 |
| quick_entry | POST /quick-entry | SOP L930–937 | 需求存在；实现/部署/实际挂载未核 |
| membership_link | POST /membership-link | SOP L930–937 | 需求存在；实现/部署/实际挂载未核 |
| first_dialogue | POST /platform-api/telemetry/v1/first-dialogue | SOP L178、L344 | 需求存在；实现/部署/实际挂载未核 |
| first_dialogue_link | POST /platform-api/telemetry/v1/first-dialogue/link | SOP L347、L568 | 需求存在；实现/部署/实际挂载未核 |


其他必须绑定的操作为quote_rank_table、submit_rank_table、get_rank_table、consumption_history、create_support_ticket、get_support_ticket、payment_callback。本稿只定义动作语义与数据合同，实际path=null，不能自行发明一个/ranking路由并声称已存在。每个动作的源码路径、固定提交、实际URL、入出参白名单、鉴权、错误、超时与合成测试都须补齐。

## 7. 后端上游资产接入、数据来源与成本（A1第7项）

### 7.1 两分支与登记边界

`user_records`明确“只处理用户粘贴的记录，不接供应商”，仍需服务端计费/账单证据；不能把不接外部供应商解释为运行成本必为0。`backend_public`的方向是后端公开采集并整理；本候选不接卖家后台、不收取登录Cookie、不抓需登录私有数据。实际公开采集合法范围、授权/条款、限额和字段能力须由对应部署证据支持，不因SOP提到某平台即假定存在可用API。

当前：资产ID=null、资源目录固定提交=null、适配器源码路径/提交=null、调用参数映射=null、限额/成本=null、已部署版本=null、实际验证=false。可用材料只是产品仓文档与沟通程序，后者GitHubBackend不是业务后端。没有资源/adapter证据时后端取数分支禁用，不回退旧资源或未登记来源。

排名数据采集未必需要AI模型；若实际实现不用模型，模型映射明确“不适用”，不为了填章节造模型名。若未来需要模型，必须先给登记asset_id、支持的模型别名到实际适配器的固定映射、请求/结果协议及成本；资源目录写着模型名不等于adapter支持调用。logo为单独登记生图资产，不与排名采集混为一个供应商。

### 7.2 适配器与参数合同（待实现/绑定）

拟议适配器按platform/market/locale/entity_id/keyword/rank_type/date_range与已核公开覆盖取数，返回第4节观测schema及证据，不返回上游密钥给客户端。未知参数在提交前拦截，不把未经服务端确认的范围承诺给用户。`observed_at/source_timezone/coverage/page_index/metric_kind`不可缺失后由AI猜补。抓取受限、额度保护、无数据和映射失败要保留独立状态，不自动切换到不同口径来源冒充同一序列。

本技能无参考图输入/输出协议：排名任务不上传参考图、不生成示例图。唯一logo的参考图策略默认为文字方向、无用户肖像或商标；如果登记资源要求参考图，必须先绑定其协议、授权、尺寸及hash，未绑定不传。所有参数、单次调用边界、预算和重试上限由登记适配器合同决定；Key仅服务端/安全本机受控引用，不入包、前端、日志或公共仓库。

### 7.3 成本和毛利

实际单次采集/处理/存储成本、固定费、计量单位、credits/requests双限、缓存成本、毛利均未知，JSON用null，不写0或100%。配置阈值为毛利率≥0.6；若同一有效单位收入确为内部已定30分，则代数成本上限为12分，这只是算式，不是实际成本已核。必须取得对应真实单位的成本来源和固定证据后再算，单位不一致时不凑数字；无月费备用只留设计边界，不新增供应商或自动试调用。

## 8. 车道、实施和部署状态（A1第8项）

建议车道为`deploy`：原因是缺少可核业务能力，不能确认fast前提。**没有完成后端补丁、没有跑补丁测试，不能把本状态写成“补丁已测好仅待部署”。** 下一步先补B01–B04证据；若证明已有接口可覆盖全部核心保障，依据固定源码再修订为fast；若需要实现后端，单独在本频次产品目录交付补丁/Mock测试/README，再按部署前拦停规则处理。后端共享与隔离架构不重新作为逐单拍板门槛；缺的是客户端必须绑定的具体接口与资源证据。

已实现：本次无Skill或业务后端实现。待实现：输入/表格/本地导出、账号/任务/账单适配、Mock及测试。待部署：只有未来经测试的后端变更才属此状态，本轮尚无该产物。未验证：真实业务接口、plans/单价配置、回调与账本、公开采集器、资源成本及logo调用。

## 9. 具体契约与客户端流程（A1第9项）

### 9.1 规范操作与字段

下列为待后端实现/绑定的逻辑合同，不是现有API返回样例：

1. `quote_rank_table`：输入`skill_id, scene, source_mode, input_sha256, normalized_observations, metric_kind, filters, budget_fen`；输出须给`quote_id/version/expires_at, billable_unit_definition, unit_price_fen, maximum_units, estimated_fee_fen/maximum_fee_fen, validation_issues`，全部待核。无有效单位/无合同先返回校验缺件，不收费。
2. `submit_rank_table`：在两次确认后提交同`quote_id, input_sha256, task_id, operation_key, budget_fen, consent_digest`，同一固定幂等键放在实际合同规定的位置。返回`task_id,state,accepted_input_sha256`及可核账单关联；未确认不能提交。
3. `get_rank_table`：只凭已认证原账户与原task/operation取`pending/partial/succeeded/failed/unknown`、观测、缺项、原结果文件或本地导出所需数据、`ledger_ref`。未知/断网不说“已完成/未扣费”；服务端404究竟未受理还是暂不可见需源码判定，不能自动新收费提交。
4. `consumption_history`：原账户、本skill、原task关联的已确认`charged_fen, balance_fen, status, operation_key`及分页；其他账户记录404，不允许靠客户端传任意account_id读取。实际字段未给之前不猜费用。
5. `create_support_ticket/get_support_ticket`：仅本skill原账户工单、提交幂等、最小故障证据和状态；不上传原始关键词以外的敏感会话/凭据，不自定处理时限。待固定合同后再开放对客功能。

请求/响应字段一旦由真实源码固定，须逐项建立“字段→源码行/固定commit→Mock断言”映射；与本候选不一致的地方显式修改文档，不让客户端吞掉差异。

### 9.2 脚本与信号（均为待实现的本技能CLI）

拟提供`preview`（文字样例）、`import`（仅校验导入）、`plan/quote`（冻结方案与报价）、`run`（显式同意后提交）、`resume`（原task续查）、`export`、`status/auth/balance/topup/paid/history/support`。这是拟议CLI，不声称脚本已存在。`regenerate`不当免费续接别名；改条件或新日期取数形成新任务时重新确认。

stdout输出单一JSON对象，用户话术位于`message`字段；stderr仅进度。逻辑信号拟为`needsInput/needsAccount/needsQuote/needsConsent/needsFunds/pending/partial/completed/failed/unknown`，实际实现需契约测试锁住。退出0为本次命令确定完成，2为等待用户动作，3为已知错误；不能把pending/partial退出0解释成完整任务成功。所有动作返回同task_id/operation_key或明确未建任务，方便同一状态机处理。

本机可提出HTTP上限30秒、5秒轮询/150秒窗口、外层180秒的候选测试profile，但实际接口无源码证据，JSON正式timeouts仍null；只有固定实测/合同后才把正数同时写入命令表与scenes，禁止0秒占位或用候选数承诺生产等待。

### 9.3 首用四步、两确认与接管

欢迎句逐字为“欢迎使用WorkBuddy官方认证技能 跨境关键词排名助手。”；随后用法介绍+2–3个文字例子、从真实status读取计费单位/单价/五类0费，再在用户要继续付费时给真实关联入口。拒绝开通只说“没问题。等你需要的时候随时叫我。”后结束，不追推链接。授权/充值不是付费查询同意。

方案确认先固定对象、站点、关键词、来源、日期、metric与可用页面口径；提交前第二次复述查什么、范围、预计有效数量、实时单价和费用上限，用户明确同意后才执行。无报价、缺计费合同或输入冲突时不得把认可方向/沉默当同意。账户词直接走账户路由，不重放首次介绍；“继续上次表”读原task，不能索要重新贴全数据来另建收费任务。

只按脚本message转述，禁止自编故障、到账或收费。网络未知、认证失效、余额不足、限流、已知服务错误各有真实动作；没有原账单时不说“已扣/没扣”。用户已得结果部分可交付实际链接、hash、缺项和已知费用，不能把缺格/无效/unknown任务称整套成功。

### 9.4 收藏

仅在第一次成功交付后或用户主动要求时询问一次；本对话最多一次，拒绝/失败/充值过程不问。同意后宿主若支持则写仅本skill偏好的稳定标记`【技能偏好：cross-border-keyword-ranking收藏】`及“排名记录成表与本技能账户事务优先调用本技能”，不附个人信息。已有标记不重复问。取消只删本skill记录，写入/删除失败或宿主不支持如实说明；A2用Mock，真实跨聊天命中不冒称通过。

## 10. 漏斗步骤与数据缺口（A1第10项）

首版需求为8字段200、2秒、每安装一次attempt；ID及attempt先持久化再POST，失败不重试，开关off不生成标识；绑定后按已核link合同幂等接回原账户。当前部署未验证，不能把第六章二期9字段/204/machine_id/新表当已支持或对fast强塞。缺机器口径时L0新设备等写“不可得”，不写0、不捏造转化率。


| 步骤 | 目标口径 | 本候选证据/真实状态 |
|---|---|---|
| L0 | 首次开口/可核安装；新设备需二期能力 | 后端来源未提供，UNAVAILABLE，不冒称已上报/首充 |
| L1 | 关联页打开 | 后端来源未提供，UNAVAILABLE，不冒称已上报/首充 |
| L2 | 原账户关联完成 | 后端来源未提供，UNAVAILABLE，不冒称已上报/首充 |
| L3 | listing充值页到达 | 后端来源未提供，UNAVAILABLE，不冒称已上报/首充 |
| L4 | 本skill钱包首笔已核入账 | 后端来源未提供，UNAVAILABLE，不冒称已上报/首充 |


遥测八字段固定event_id、installation_id、skill_id、skill_name、skill_version、channel、client_occurred_at、is_test；不含用户原话/关键词、账户、手机号或machine_id。开发测试is_test=true，全部Mock，不真实走首充。D1/D7/D14/D30快照需真正上线时间与可得接口支持，否则标不可得；不把“接口取不到”当正式0触发回滚，也不自动上传旧包。

## 11. 相邻自家技能与分界（A1第11项）

- 否定词/广告投放：转介“跨境广告否定词助手”方向，但brief只称框架未开发，不能声称可安装或编slug。
- Listing文案：brief称“Listing优化大师”已上线；具体slug/当前能力未提供，只能说明本品不做改写，不虚构工具调用。
- 本品只做排名观测成表；“趋势兴趣”“广告因果”“找新词”“价格/促销/新品全监控”不能靠拓展触发词吃进来。已知范围内从脚本接待，范围外给明确边界。

## 12. 不承接清单（A1第12项）

不做关键词挖掘/选词、广告优化、Listing改写、卖家后台/需登录私有数据、刷排名或排名恢复保证、竞品全套监控、自动下跌提醒、虚构历史、未知费用推定、客户财务改动、生产部署/重启、真实付费测试和正式上传。本单不通过模型编造缺失名次/目标或个人信息，不因为Google Trends数据可用就承诺实际SERP排名。

## 13. A2/A3验收用例（A1第13项）

以下均为计划，当前NOT_RUN。A2须从同一固定ZIP干净解包，在Windows与Linux用同一冻结测试源、合成账户、Mock业务/宿主跑实际case；失败记录和局部复测真实保留，不把Mock当生产实测。A3只读该固定ZIP/AST/文本/hash，不重跑脚本/上游/浏览器/真机。


| ID | 执行方/阶段 | 内容 | 当前结果 |
|---|---|---|---|
| T01 | A2 | 八必填frontmatter、双语三对同场景、22触发词、固定欢迎句、当前仅技能包；无示例图/demos | NOT_RUN |
| T02 | A2 | 20条口语含账户优先/续接/不做请求，所有路由锁到本skill脚本 | NOT_RUN |
| T03 | A2 | 日期、时区、站点/目标、Unicode关键词、正整数rank、bool/NaN/0/负值、输入文件编码与边界 | NOT_RUN |
| T04 | A2 | ordinal/average_position/Trends兴趣严格分表；0–100兴趣不可充当名次 | NOT_RUN |
| T05 | A2 | observed/not_found/missing/capped/ambiguous/invalid六态；无有效名次不收费，不补0或最后一页 | NOT_RUN |
| T06 | A2 | 同观测去重、同key值冲突必须明确处理，不静默以最新文件覆盖；不重复计费 | NOT_RUN |
| T07 | A2 | 跨站点、目标、渠道、来源、metric不串组，不伪造跨组涨跌 | NOT_RUN |
| T08 | A2 | delta=previous_rank-current_rank；同值/缺日断开连跌；两次连续日际下降需至少3日 | NOT_RUN |
| T09 | A2 | 来源page_index或已核page_size才计算首页；无分页证据为unknown，不设Amazon48/Google10默认 | NOT_RUN |
| T10 | A2 | HTML真红色样式及说明，CSV UTF8 BOM、防公式注入，原始/宽表/异常清单/manifest，Markdown路径空格和#安全编码 | NOT_RUN |
| T11 | A2 | 原身份持久化/新对话恢复，401只清失效运行凭据不重建钱包，身份无法恢复如实等待 | NOT_RUN |
| T12 | A2 | 无账户quick-entry、listing域/path/时效校验、拒绝用户info/重定向/旧链接；不手拼已签URL | NOT_RUN |
| T13 | A2 | 首用四步/拒绝结束、方案确认+报价复述同意；认可/扫码/沉默不算付费同意 | NOT_RUN |
| T14 | A2 | 零结果、档案缺失、上游失败、映射失败、额度保护五类逐个断言charged=0；上游200业务无效同样0 | NOT_RUN |
| T15 | A2 | 任务累计预算与报价版本、部分结果、并发/续接/重试共享上限；超预算停并保已有结果 | NOT_RUN |
| T16 | A2 | 原任务原key原输入原quote replay无额外扣费；应答丢失/5xx先查原操作，不另起key | NOT_RUN |
| T17 | A2 | 同key幂等优先结果cache；仅另经两次确认的新任务可命中cache按已核规则收费 | NOT_RUN |
| T18 | A2 | 中途402保存原task/已得结果/累计费；充值后同一任务续接，充值不重置预算 | NOT_RUN |
| T19 | A2 | 费用只取本账户本skill原task账单；null/错job/bool/非整数/负数保持未知，不用余额差推费 | NOT_RUN |
| T20 | A2 | 模拟签名错/金额币种skill账户错/重复回调/并发到账/账本事务；不改真实余额或真实支付 | NOT_RUN |
| T21 | A2 | 所有HTTP有界/无重定向/429与5xx临错次数可见/未知只续查；脚本+宿主共享重试预算 | NOT_RUN |
| T22 | A2 | 实际可用文件/缺项/原账单/manifest部分交付，空结果不留旧下载链接、不叫完整成功 | NOT_RUN |
| T23 | A2 | 日志/仓库/zip无token/key/个人数据；本机原子私有文件、POSIX700/600和Windows等价ACL隔离 | NOT_RUN |
| T24 | A2 | 首次8字段200/2秒/attempt先持久化、失败不重试/关闭开关/损坏只读/前置返回/link幂等，全Mock | NOT_RUN |
| T25 | A2 | 只成功交付后一次、明确同意写两层偏好、拒绝/失败/不支持/取消只本skill；不冒充真实宿主 | NOT_RUN |
| T26 | A2 | 消费记录和工单原账户归属、分页、提交幂等、跨账户404；无源码前只作待实现Mock要求 | NOT_RUN |
| T27 | A2 | 无已登记adapter不调后端自采、不回退旧资源、不把GoogleTrends当rank、不读需登录卖家数据 | NOT_RUN |
| T28 | A2 | 同固定ZIP干净解包Windows/Linux各跑实际case；源码/日志SHA及平台skip逐项记录，禁止假称全过 | NOT_RUN |
| S01 | A3 | 固定ZIP/hash/<=3MB/单根最多一层子目录/八字段/双语样例/路径/禁用词/无assets图与密钥/契约逐项静态核 | NOT_RUN |
| S02 | A3 | 外置PNG512×512≤500KB、源hash/provenance/audit；无logo或假登记不通过 | NOT_RUN |
| S03 | A3 | 固定源码/规格/资源/同ZIP A2证据一致，收费与原任务续接只读AST核，不运行脚本/上游 | NOT_RUN |
| H01 | owner_reference | 真实WorkBuddy加载/记忆/浏览器/附录D/D.6按长期规则SKIPPED，不作为已通过 | SKIPPED_STANDING_RULE_REFERENCE_ONLY |


## 14. 决策记录（A1第14项）


| ID/事项 | 采用 | 依据 |
|---|---|---|
| D01 / skill_id | 沿已建目录和固定请求cross-border-keyword-ranking；不另造mps条目 | 固定请求、status及用户指定路径；与SOP一般命名前缀不同处显式留痕 |
| D02 / price_internal_only | 本单已定意图30分/有效单位；对客与执行仍读取已核实时status，不硬编码 | brief备注与Muse固定交接；当前后端配置未核 |
| D03 / metrics | 三类指标严格区分；ordinal仅正整数，平均位置不是单次排名，相对兴趣不是名次 | Google Trends/Search Console官方说明与事实不编造 |
| D04 / consecutive_decline | 连续下降默认至少两次相邻日际变化，即至少3日；用户可选择明确更长阈值 | 保守、可解释的拟议成表规则，缺日或持平断开，不推断原因 |
| D05 / first_page | 只用源page_index或已核分页协议，缺证据unknown | 不推定平台每页数或搜索范围 |
| D06 / effective_unit | 建议以原task去重后可核ordinal观测为候选单位；实际后端单位/部分成功合同待绑定，不先收费 | 按实际有效结果数扣；原请求未提供可核业务计费schema |
| D07 / supplier | 粘贴分支不接外部供应商；后端取数分支只在已登记adapter/接口就绪后启用 | 不回退、不伪造现有资源；不是重新要求后端共享隔离拍板 |
| D08 / lane | 建议deploy；无已核backend、未有补丁/测试，不宣称deploy完成或仅待上线 | SOP L263/L425及频次仓完整树证据 |
| D09 / logo | 已有1张长期授权，但缺本单登记资源；不以通用图片CLI或未登记fallback替代 | SOP logo要求与资源登记边界是不同条件 |
| D10 / official | 本次三页在线重读并记录日期/hash；快照只补充 | 本聊天用户直接要求和固定交接 |
| D11 / idempotency | 原任务同key优先于查询cache；未知先核原操作，明确新任务才新确认 | 防原响应丢失后重收费；不把HTTP错误不缓存当操作不存在 |
| D12 / outbound_scope | 不改变客户余额/套餐/免费政策，不部署，不接真实付费查询，不上传 | 只写规格；任何后端实施另按SOP授权及部署前停 |
| D13 / sample_counts | 3对frontmatter样例；每场景1文字sample，无demos/示例图 | WorkBuddy结构与频次SOP B2 |
| D14 / attempt_limits | 规格退回上限1次、其他max_rounds按尝试数；本候选不伪计A1通过 | 最新config/已定事项；状态由root按真实关口写回 |


## 15. MD/JSON一致性、关口及交接缺件（A1第15项）

JSON与本稿使用同一skill_id/版本/channel、场景key/文字sample、22触发词/三对例子、支付URL、metric/schema、候选计费单位/预算/幂等规则、缺件列表、未执行状态及边界。成本/正式超时/实际路由/资源映射用null而非0占位；拟议字段不会被标记成已有实现。

### 15.1 A1实际判断

文档设计项已写齐；以下是仍不能通过的实质证据项，不把它们写成“自查全部通过”：


| 缺件ID | 缺什么 | 影响A1 |
|---|---|---|
| B01 | 实际账户恢复、定价/余额/plans、签发listing链接、业务任务/续接、原账单、回调验证的固定源码/路径/部署证据；不含key或真实客户数据 | A1_6、A1_8、A1_9 |
| B02 | 来源/metric/页面/覆盖协议与适配器固定实现、有效结果单位及部分结果结算/任务预算/幂等证据 | A1_7、A1_8、A1_9 |
| B03 | 实际采集/计算/存储或供应商成本、计量与限额；毛利达到0.6的可核计算，不以未知当0 | A1_7 |
| B04 | 本单可用登记生图资产ID/固定来源/安全调用方式/成本及audit落点；按已有长期授权生成1张并验证文件 | A1_5 |


当前A1结论：`NOT_PASSED_EVIDENCE_MISSING`，不得置spec_ready。它不是等待负责人重新恢复自动化或再授权logo；所缺是已授权流程实际要调用的接口/字段/资源/成本证据，以及真实logo文件。不能把“后端共享/隔离不关Skill开发”误写为“客户端无需知道真实接口”。

如果资料证明已有实现，可直接按固定来源补齐并继续，不逐单重新问架构选择；如需实现，按deploy车道交真实补丁和合成测试后在部署前停。根代理按SOP以实际缺件向Muse交回并决定状态；本候选自身不改status，不宣称已部署或已交产品包。

### 15.2 本次真实执行

已完成：三页官方在线重读、固定需求与频次资料核对、完整规格候选MD/JSON。未完成/未执行：Skill开发、业务后端补丁与测试、logo生成、A2、A3、真实宿主、实际取数、真实支付、生产部署、重启、财务写入、上传。所有真实上游调用为0。本候选仅交评审和具体补件，不覆盖Muse选品单/其他角色产物，不访问其他流水线仓库。
