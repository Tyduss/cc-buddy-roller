# buddy-roller

[English](README.en.md) | **中文**

Claude Code 自定义 Skill，帮你**查询、筛选、更换** Claude Code 的 Buddy 宠物。

支持自然语言输入（中英文均可），自动暴力搜索匹配的 userId，渲染 ASCII 宠物形象和属性面板，并自动写入配置。

**同时支持 Bun（native 安装）和 Node.js（npm 安装）两种运行环境**，自动检测并使用对应的 hash 算法，无需手动配置。

## 功能

| 功能 | 用法 | 说明 |
|------|------|------|
| 查看当前宠物 | `/buddy-roller` | 读取你的 `~/.claude.json`，展示当前宠物 |
| 查询任意 userId | `/buddy-roller query:abc123` | 查看任意 userId 对应的宠物 |
| 筛选并更换宠物 | `/buddy-roller 传说级企鹅，戴皇冠，闪光` | 搜索匹配的 userId 并自动替换 |

## 安装

```bash
# 克隆仓库
git clone https://github.com/Tyduss/cc-buddy-roller.git
# 复制到 Claude Code skills 目录
cp -r cc-buddy-roller ~/.claude/skills/
```

重启 Claude Code 后即可使用。

## 使用示例

### 查看当前宠物

```
/buddy-roller
```

自动读取 `~/.claude.json` 中的 userID，展示当前宠物形象和属性。

### 查询任意 userId

```
/buddy-roller query:ab54093b-xxxx-xxxx-xxxx
```

只读查询，不会修改任何配置。

### 更换宠物

支持中英文自然语言描述：

```
/buddy-roller 传说级企鹅，戴皇冠，闪光，✦眼
/buddy-roller legendary dragon wizard shiny
/buddy-roller --species cat --rarity epic --hat tophat
```

Skill 会：
1. 先展示你当前的宠物
2. 确认后开始搜索匹配的 userId
3. 找到后展示新宠物的 ASCII 形象 + 属性表格
4. 自动修改 `~/.claude.json`（替换 userID，删除 companion 块）
5. 提示你重启 Claude Code 使新宠物生效

### 可选物种

```
鸭子 duck        鹅 goose         史莱姆 blob
 <(· )___        (·>              .----.
  (  ._>          ||             ( ·  · )
   `--´         _(__)_            `----´

猫 cat           龙 dragon        章鱼 octopus
  /\_/\          /^\  /^\          .----.
 ( ·   ·)       <  ·  ·  >       ( ·  · )
 (  ω  )        (   ~~   )       (______)
 (")_(")         `-vvvv-´        /\/\/\/\

猫头鹰 owl       企鹅 penguin     乌龟 turtle
  /\  /\        .---.            _,--._
 ((·)(·))       (·>·)           ( ·  · )
 (  ><  )      /(   )\         /[______]\
  `----´        `---´           ``    ``

蜗牛 snail       幽灵 ghost       美西螈 axolotl
 ·    .--.       .----.        }~(______)~{
  \  ( @ )      / ·  · \       }~(· .. ·)~{
   \_`--´       |      |         ( .--. )
  ~~~~~~~       ~`~``~`~         (_/  \_)

水豚 capybara    仙人掌 cactus    机器人 robot
 n______n     n  ____  n          .[||].
( ·    · )    | |·  ·| |         [ ·  · ]
(   oo   )    |_|    |_|         [ ==== ]
 `------´       |    |           `------´

兔子 rabbit      蘑菇 mushroom    胖猫 chonk
   (\/__)      .-o-OO-o-.        /\    /\
  ( ·  · )    (__________)      ( ·    · )
 =(  ..  )=      |·  ·|         (   ..   )
  (")__(")       |____|          `------´
```

### 其他属性

| 属性 | 可选值 |
|------|--------|
| **稀有度** | common (★), uncommon (★★), rare (★★★), epic (★★★★), legendary (★★★★★) |
| **眼睛** | ·, ✦, ×, ◉, @, ° |
| **帽子** | none, crown, tophat, propeller, halo, wizard, beanie, tinyduck |
| **闪光** | shiny |

不需要指定所有属性，只写你关心的即可。

## 输出示例

### Legendary 水豚 · tinyduck 帽 · ◉眼

| 属性 | 值 |
|------|-----|
| **稀有度** | ★★★★★ legendary |
| **物种** | 水豚 |
| **眼睛** | ◉ |
| **帽子** | tinyduck 小鸭子 |
| **闪光** | 非shiny |
| **属性** | DEBUGGING 63 / PATIENCE **100** / CHAOS 69 / WISDOM 88 / SNARK 52 |

```
    ,>
  n______n
 ( ◉    ◉ )
 (   oo   )
  `------´
```

### Epic 龙 · crown 帽 · ·眼

| 属性 | 值 |
|------|-----|
| **稀有度** | ★★★★ epic |
| **物种** | 龙 |
| **眼睛** | · |
| **帽子** | crown 皇冠 |
| **闪光** | 非shiny |
| **属性** | DEBUGGING 57 / PATIENCE 51 / CHAOS 50 / WISDOM **100** / SNARK 39 |

```
   \^^^/
  /^\  /^\
 <  ·  ·  >
 (   ~~   )
  `-vvvv-´
```

### Common 企鹅 · ✦眼 · shiny 闪光

| 属性 | 值 |
|------|-----|
| **稀有度** | ★ common |
| **物种** | 企鹅 |
| **眼睛** | ✦ |
| **帽子** | 无 |
| **闪光** | shiny |
| **属性** | DEBUGGING 76 / PATIENCE 6 / CHAOS 8 / WISDOM 33 / SNARK 10 |

```
  .---.
  (✦>✦)
 /(   )\
  `---´
```

## 兼容性

| 安装方式 | 运行时 | 说明 |
|----------|--------|------|
| npm install | Node.js | 使用 FNV-1a hash |
| Native install | Bun | 使用 Bun.hash |

脚本自动检测运行环境，选择正确的 hash 算法。**注意：** Bun 和 Node 的 hash 结果不同，请确保运行时与你的 Claude Code 安装方式一致。

## 文件结构

```
buddy-roller/
├── README.md        # 本文件
├── SKILL.md         # Skill 定义（Claude Code 读取）
└── find-buddy.js    # 搜索/查询脚本（兼容 Node.js 和 Bun）
```

## 工作原理

Claude Code 的 Buddy 宠物由 `~/.claude.json` 中的 `userID` 字段通过确定性伪随机算法生成。本 Skill 通过暴力搜索找到满足指定属性组合的 userId，然后替换配置文件中的值，从而获得想要的宠物。

## 注意事项

- 传说级 + 闪光的组合概率极低（约 1/864 万），搜索可能需要数分钟
- 搜索过程中可以随时等待，结果会在后台计算完成后返回
- 替换 userId 后需要重启 Claude Code 才能生效
- `companion` 块会被删除以重新生成宠物名字和性格

## License

Apache-2.0
