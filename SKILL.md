---
name: buddy-roller
description: Roll a buddy pet with desired attributes (species, rarity, eye, hat, shiny), find a matching userId, and display it with ASCII art sprite and stats table. Also supports querying any userId to see its pet. Use when the user wants to find, roll, change, or query a buddy/companion pet.
argument-hint: "<query:userId | --species <sp> --rarity <r> --eye <e> --hat <h> --shiny>"
---

# Buddy Roller

You are a buddy pet roller. Given the user's desired pet attributes, find a matching userId using the search script and present the result with a stats table and ASCII art sprite.

## How to parse user input

The user will describe the pet they want in natural language (Chinese or English). Parse it into these flags:

| Attribute | Flag | Values |
|-----------|------|--------|
| Species | `--species` | duck, goose, blob, cat, dragon, octopus, owl, penguin, turtle, snail, ghost, axolotl, capybara, cactus, robot, rabbit, mushroom, chonk |
| Rarity | `--rarity` | common, uncommon, rare, epic, legendary |
| Eye | `--eye` | ·, ✦, ×, ◉, @, ° |
| Hat | `--hat` | none, crown, tophat, propeller, halo, wizard, beanie, tinyduck |
| Shiny | `--shiny` | add flag if shiny is requested |

**Chinese translations for reference:**
- Species: 鸭子duck, 鹅goose, 史莱姆blob, 猫cat, 龙dragon, 章鱼octopus, 猫头鹰owl, 企鹅penguin, 乌龟turtle, 蜗牛snail, 幽灵ghost, 美西螈axolotl, 水豚capybara, 仙人掌cactus, 机器人robot, 兔子rabbit, 蘑菇mushroom, 胖猫chonk
- Rarity: 普通common, 非凡uncommon, 稀有rare, 史诗epic, 传说legendary
- Hat: 无none, 皇冠crown, 高礼帽tophat, 螺旋帽propeller, 光环halo, 巫师帽wizard, 毛线帽beanie, 小鸭子tinyduck

If the user doesn't specify all attributes, only include the ones they specified. At least one filter is required.

## Step 0: Show current pet

Before doing anything, **always** read the user's current pet first.

1. Read `~/.claude.json` and extract the `"userID"` field
2. Query what pet that userID rolls by running:
   ```bash
   <bun|node> ${CLAUDE_SKILL_DIR}/find-buddy.js --query "<userID>"
   ```
3. Render the current pet using the same sprite/table format from Step 3, with a heading like:

> **你当前的宠物：**

4. Ask the user: "是否要更换宠物？如果满意当前宠物可以告诉我。" If they want to change, proceed to Step 1.

## Step 1: Detect runtime

First, check which runtime the user has. Run:
```bash
which bun 2>/dev/null && echo "BUN" || echo "NODE"
```

- If **BUN**: the user installed Claude Code natively → use `bun` to run the script (uses `Bun.hash` for correct hash)
- If **NODE**: the user installed via npm → use `node` to run the script (uses FNV-1a fallback)

**Important:** The hash algorithm differs between Bun and Node. A userId found with `bun` will NOT produce the same pet with `node`, and vice versa. Always match the runtime to the user's actual Claude Code installation method.

## Step 2: Run the search script

Run the search script located at `${CLAUDE_SKILL_DIR}/find-buddy.js` with the detected runtime:

```bash
<bun|node> ${CLAUDE_SKILL_DIR}/find-buddy.js --species <sp> --rarity <r> --eye <e> --hat <h> --shiny --mode sequential --prefix u --start 0 --limit 50000000 --count 1
```

Use the Bash tool with `timeout: 600000` and `run_in_background: true` for rare combinations (legendary/shiny). For common combinations you can use a shorter timeout.

## Step 3: Render the result

Once you get the JSON result, parse it and present the pet in this format:

### Rarity stars mapping
- common: ★
- uncommon: ★★
- rare: ★★★
- epic: ★★★★
- legendary: ★★★★★

### Hat ASCII art mapping
```
none:      (no hat line)
crown:        \^^^/
tophat:       [___]
propeller:      -+-
halo:        (   )
wizard:        /^\
beanie:      (___)
tinyduck:       ,>
```

### Sprite rendering

Render the ASCII sprite by taking the body frames from below and:
1. Replace `{E}` with the eye character
2. If hat is not "none" and frame 0 is blank, replace line 0 with the hat art
3. Drop the first line if it's blank and ALL frames have blank line 0

Here are the body frames (frame 0, the idle state) for each species:

```
duck:
            (blank → hat slot)
    __
  <({E} )___
   (  ._>
    `--´

goose:
            (blank → hat slot)
     ({E}>
     ||
   _(__)_
    ^^^^

blob:
            (blank → hat slot)
   .----.
  ( {E}  {E} )
  (      )
   `----´

cat:
            (blank → hat slot)
   /\_/\
  ( {E}   {E})
  (  ω  )
  (")_(")

dragon:
            (blank → hat slot)
  /^\  /^\
 <  {E}  {E}  >
 (   ~~   )
  `-vvvv-´

octopus:
            (blank → hat slot)
   .----.
  ( {E}  {E} )
  (______)
  /\/\/\/\

owl:
            (blank → hat slot)
   /\  /\
  (({E})({E}))
  (  ><  )
   `----´

penguin:
            (blank → hat slot)
  .---.
  ({E}>{E})
 /(   )\
  `---´

turtle:
            (blank → hat slot)
   _,--._
  ( {E}  {E} )
 /[______]\
  ``    ``

snail:
            (blank → hat slot)
 {E}    .--.
  \  ( @ )
   \_`--´
  ~~~~~~~

ghost:
            (blank → hat slot)
   .----.
  / {E}  {E} \
  |      |
  ~`~``~`~

axolotl:
}~(______)~{
}~({E} .. {E})~{
  ( .--. )
  (_/  \_)

capybara:
            (blank → hat slot)
  n______n
 ( {E}    {E} )
 (   oo   )
  `------´

cactus:
            (blank → hat slot)
 n  ____  n
 | |{E}  {E}| |
 |_|    |_|
   |    |

robot:
            (blank → hat slot)
   .[||].
  [ {E}  {E} ]
  [ ==== ]
  `------´

rabbit:
            (blank → hat slot)
   (\/__)
  ( {E}  {E} )
 =(  ..  )=
  (")__(")

mushroom:
            (blank → hat slot)
 .-o-OO-o-.
(__________)
   |{E}  {E}|
   |____|

chonk:
            (blank → hat slot)
  /\    /\
 ( {E}    {E} )
 (   ..   )
  `------´
```

### Output format

Present the result as:

```
**Title: [rarity] [species] · [hat] · [eye]眼 · [shiny]** — userId: `userId`

| 属性 | 值 |
|------|-----|
| **userId** | `userId` |
| **物种** | species 中文 |
| **稀有度** | ★★★★★ legendary |
| **眼睛** | eye |
| **帽子** | hat 中文 |
| **闪光** | shiny / 非shiny |
| **属性** | DEBUGGING xx / PATIENCE xx / CHAOS xx / WISDOM xx / SNARK xx |

```
ASCII sprite here
```

把 userId 换成 `userId` 即可。
```

Highlight any stat that is 100 in bold.

## Step 4: Auto-apply the userId (always do this after rendering)

After presenting the pet, **automatically modify `~/.claude.json`** for the user — do NOT just tell them to do it manually.

### Steps:

1. **Read** `~/.claude.json` first
2. **Edit** the file:
   - Replace the value of `"userID"` with the new userId from the search result
   - Delete the entire `"companion"` block if it exists (including its nested `name`, `personality`, `hatchedAt` fields)
3. **Confirm** the changes by showing a brief diff or summary of what was changed

Example of what the edit looks like:

Before:
```jsonc
"userID": "ab54093b...",
"companion": {
  "name": "Pricklebait",
  "personality": "...",
  "hatchedAt": 1775006380441
}
```

After:
```jsonc
"userID": "<new-userId>",
```

The `"companion"` block is removed entirely so that Claude Code regenerates a fresh name/personality for the new pet on next launch.

### After applying, tell the user:

> 已自动修改 `~/.claude.json`：
> - `userID` → 新值
> - `companion` → 已删除
>
> 请**重启 Claude Code**，新宠物即可生效。重启后输入 `/buddy` 查看你的新宠物。

---

## Query mode: 查询任意 userId 的宠物

When the user provides a userId directly (or says something like "查一下这个id是什么宠物", "看看 xxx 的宠物"), enter **query mode** instead of rolling.

### How to detect

The user's input is a query mode if:
- `$ARGUMENTS` is a plain string that looks like a userId (no `--` flags), e.g. `/buddy-roller query:ab54093b`
- The user says "查一下", "看看", "query", "what pet is", "是什么宠物" etc. followed by a userId

### How to execute

1. Detect runtime (same as Step 1 above)
2. Run:
   ```bash
   <bun|node> ${CLAUDE_SKILL_DIR}/find-buddy.js --query "<userId>"
   ```
3. Render the result using the same sprite/table format from Step 3

No modifications to `~/.claude.json` are made in query mode — this is read-only.
