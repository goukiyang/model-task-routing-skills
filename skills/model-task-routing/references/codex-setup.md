# Codex 安装与角色配置

这份说明把 `model-task-routing` 配成可供 Codex 实际派工的角色集合。样例只提供通用职责；它们不会自动安装、改写现有配置、选择可用模型或赋予权限。

## 先保留两个技能的相对位置

把 `model-task-routing` 和 `landing-execution` 两个完整技能目录安装到同一个 Codex 技能目录，作为彼此的同级目录。保留各自的 `SKILL.md` 和 references 等附属文件；`landing-execution/SKILL.md` 会用 `../model-task-routing/SKILL.md` 找到路由技能。只装其中一个会丢失这条依赖。

按当前 Codex 客户端实际使用的 `CODEX_HOME` 定位用户级配置与角色定义；未设置时，默认位置是 `~/.codex`。技能发现目录由客户端的技能范围决定：当前官方指南列出用户级 `~/.agents/skills`、仓库级 `.agents/skills` 和机器级 `/etc/codex/skills`。无论采用哪种技能范围，都把两个完整技能目录放在同一个实际发现目录中，确保它们是同级目录；不要只凭 `CODEX_HOME` 推断技能目录。如果使用项目级 `.codex/config.toml`，依照当前客户端的项目级加载与信任规则安装，并从项目配置位置重新核对所有相对路径。

## 按需登记角色，不覆盖整份配置

当前官方指南支持把独立角色 TOML 放入用户级 `~/.codex/agents/` 或项目级 `.codex/agents/`，由客户端发现；自定义角色文件需要 `name`、`description` 和 `developer_instructions`，其中 `name` 才是角色身份，文件名相同只是便于维护的约定。先确认接收端实际采用这一发现方式，还是通过配置中的 `config_file` 加载角色层。按其支持的方式接入即可，不必同时重复登记；文件已复制不等于首次运行已验证。

`examples/agents-config.toml` 展示角色登记语法；它是待合并的片段，不是完整配置文件。先备份并检查实际生效的 `config.toml` 以及已有角色定义，再手动挑选需要的 `[agents.<role>]` 小节合并。不要用样例替换整份配置，也不要覆盖已有的同名角色文件；有同名定义时保留原内容，逐项比较后只合并确实需要的职责。

每个登记项用 `description` 说明何时选择该角色，用 `config_file` 指向职责文件。Codex 配置文档规定，相对 `config_file` 路径以声明这个角色的小节所在的配置文件为基准。因此，样例中的 `agents/lead.toml` 是相对 `examples/agents-config.toml` 的；将登记项合并到用户级配置后，应把角色文件放到该配置文件旁的 `agents/` 目录，或同步改成适合目标位置的路径。不要把样例中的相对路径原样搬到不同位置后假设它仍然有效。

样例给 `lead`、`planner`、`implementer`、`complex_implementer`、`reviewer`、`specialist`、`escalation`、`prompt_writer`、`impeccable_asset_producer` 和 `explorer` 分别提供职责文件。只登记当前客户端没有合适原生角色、且本次确实会用到的角色。当前官方 Codex 文档列出的内建角色包括 `default`、`worker` 和 `explorer`；其中 `default` 与 `worker` 可直接作为通用兼容入口，不需要另建重复样例。`explorer` 样例会成为同名自定义角色；官方文档说明同名自定义角色会优先于内建角色，因此想保留原生 `explorer` 时，省略它的登记项和职责文件。其他客户端或工具可能提供不同角色：首次派工前，检查当前工具实际支持的入口、同名自定义定义是否覆盖原生入口，以及执行后返回的真实角色信息。角色名字相同不足以证明它使用了预期配置。

## 模型、档位和权限由接收端确认

样例职责文件刻意不填 `model` 或 `model_reasoning_effort`，也不设置并发上限。选择适用模型和档位前，接收端应先确认当前客户端/工具允许的选项、自己的账号或工作区可用范围、`[agents]` 现有默认值，以及首次派工返回的实际模型与思考档位。Codex 会按客户端规则综合派工时显式值、`[agents]` 默认值、父级会话配置与角色文件中的显式覆盖；省略两个字段不会给出固定型号承诺。若需要指定值，只能在本地核实该型号和档位后，将它们加入所选角色文件，并在派工后用运行信息复核；未知就保持未知。

派工能力还受当前工具真实空闲槽位、整棵任务树中仍运行的角色、用户授权、项目规则、费用和专项上限共同约束。工具没有报告容量时降低并发或顺序处理，不猜测为零或无限。角色描述、技能和配置样例都不会授予额外权限、费用额度、项目访问或外发授权；子角色应遵守本次主会话及项目实际约束。

合并后的新文件不会切换已运行会话，也不能证明角色已经生效。按当前客户端所需方式重新加载配置后，先做一次低影响派工，并核对实际角色、有效模型/档位和可用工具；核验不可见时标为未知，不冒称已匹配。

## 官方参考

- [Codex 子代理与自定义角色](https://developers.openai.com/codex/subagents/)：内建角色、同名角色覆盖、角色文件必需字段、模型继承和子代理权限边界。
- [Codex 配置参考](https://developers.openai.com/codex/config-reference/)：`[agents.<role>]`、`description`、`config_file` 及相对路径基准。
- [Codex 技能指南](https://learn.chatgpt.com/docs/build-skills)：技能发现范围与同一技能目录的文件结构。

上述官方页面于 2026-10-08 查阅。客户端能力和配置行为可能随版本变化，接收端第一次配置时应再次核对当前文档和真实运行信息。
