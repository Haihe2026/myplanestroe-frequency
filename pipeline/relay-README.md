# 中转说明

本仓库不设 `pipeline/relay/` 目录。频次线的消息中转在独立沟通仓
`Haihe2026/myplanestroe-frequency-relay`，与生图线 `myplanestroe` 内
`pipeline/relay/` 完全分开（LI 2026-11-16 定）。启动配置白名单里的
沟通仓库、消费者绑定与投递目标一律指向该仓。
