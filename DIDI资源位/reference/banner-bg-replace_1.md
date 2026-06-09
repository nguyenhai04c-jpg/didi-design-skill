# name: Banner 背景色替换 Skill
> 适用于滴滴司机端 Banner 背景色快速切换

## description:用途

将现有 Banner 的背景色替换为**白色**或**品牌橙色（#ff6400）**，图片主视觉区域保持原样不做虚化处理。文字色随背景色自动切换。

---

## 背景色选项

| 选项 | 色值 | 适用场景 |
|------|------|----------|
| 白色 | `(255, 255, 255)` / `#ffffff` | 浅色系、清爽风格 |
| 品牌橙 | `(255, 100, 0)` / `#ff6400` | 强品牌感、促销活动 |

## 背景色与文字色映射

| 背景色 | 文字色 | 说明 |
|--------|--------|------|
| 白色 `#ffffff` | 黑色 `(30, 30, 30)` | 深色文字保证白底可读性 |
| 品牌橙 `#ff6400` | 白色 `(255, 255, 255)` | 白色文字在橙底上对比清晰 |

---

## 执行流程

### Step 1：确认参数

询问或判断：
- 目标背景色：白色 / 橙色？
- 原始 Banner 尺寸（默认按实际尺寸输出，不缩放）
- 主视觉区左边界 `VZ_X`（即图片区域从哪个 x 坐标开始，默认 490px，750px 宽 Banner 适用）

### Step 2：安装依赖

```bash
pip install Pillow numpy --break-system-packages
```

### Step 3：执行替换

```python
from PIL import Image, ImageDraw, ImageFont
import numpy as np
import os

def replace_banner_bg(
    input_path: str,
    output_path: str,
    bg_color: str = "white",          # "white" 或 "orange"
    vz_x: int = 490,                  # 主视觉图区域左边界 x
    canvas_size: tuple = (750, 230),  # 目标画布尺寸
    orig_bg_color: tuple = (230, 234, 238),  # 原背景色近似值
    diff_sensitivity: float = 5.0,    # 文字提取灵敏度，推荐 4~6
    font_path: str = None,            # 字体路径（重绘文字时使用）
    title_text: str = None,           # 主标题文字（可选，留空则用蒙版复用原文字）
    sub_text: str = None,             # 副标题文字（可选）
):
    """
    将 Banner 背景色替换为白色或品牌橙色，文字色自动跟随背景切换。
    图片区域不做任何处理。

    背景色 → 文字色映射：
        white  → (30, 30, 30)    黑色
        orange → (255, 255, 255) 白色
    """
    BG_MAP = {
        "white":  (255, 255, 255),
        "orange": (255, 100,   0),
    }
    TEXT_MAP = {
        "white":  ( 30,  30,  30),   # 黑色文字
        "orange": (255, 255, 255),   # 白色文字
    }
    if bg_color not in BG_MAP:
        raise ValueError(f"bg_color 须为 'white' 或 'orange'，当前值：{bg_color}")

    target_bg   = BG_MAP[bg_color]
    target_text = TEXT_MAP[bg_color]   # 自动匹配文字色
    canvas_w, canvas_h = canvas_size

    # ── 1. 读取并缩放原始 Banner ──
    orig = Image.open(input_path).convert("RGB")
    orig = orig.resize((canvas_w, canvas_h), Image.LANCZOS)

    # ── 2. 分离主视觉图区域（右侧）和文字区域（左侧）──
    hero       = orig.crop((vz_x, 0, canvas_w, canvas_h))
    text_strip = orig.crop((0, 0, vz_x, canvas_h))

    # ── 3. 创建目标背景色画布 ──
    canvas = Image.new("RGB", (canvas_w, canvas_h), target_bg)

    # ── 4. 图片区域直接贴回（不虚化、不处理）──
    canvas.paste(hero, (vz_x, 0))

    # ── 5. 文字处理：优先重绘，否则用差异蒙版复用原文字 ──
    if title_text and font_path:
        # 方案A：重新绘制文字（文字色精准）
        draw = ImageDraw.Draw(canvas)
        font_title = ImageFont.truetype(font_path, 36)
        font_sub   = ImageFont.truetype(font_path.replace("70S", "50S"), 27)
        draw.text((35, 65), title_text, font=font_title, fill=target_text)
        if sub_text:
            title_h = draw.textbbox((0,0), title_text, font=font_title)[3]
            draw.text((35, 65 + title_h + 9), sub_text, font=font_sub, fill=target_text)
    else:
        # 方案B：差异蒙版提取原文字，着色为目标文字色后贴回
        orig_bg_arr = np.array(orig_bg_color, dtype=np.float32)
        text_arr    = np.array(text_strip.convert("RGB"), dtype=np.float32)
        diff        = np.abs(text_arr - orig_bg_arr).max(axis=2)
        mask        = np.clip(diff * diff_sensitivity, 0, 255).astype(np.uint8)

        # 用目标文字色填充文字形状
        text_colored = Image.new("RGB", (vz_x, canvas_h), target_text)
        text_rgba    = text_colored.convert("RGBA")
        text_rgba.putalpha(Image.fromarray(mask))
        canvas.paste(text_rgba, (0, 0), text_rgba.split()[3])

    # ── 6. 保存，压缩至 300KB 以内 ──
    canvas = canvas.convert("RGB")
    quality = 93
    while quality > 20:
        canvas.save(output_path, "JPEG", quality=quality, optimize=True)
        if os.path.getsize(output_path) <= 300 * 1024:
            break
        quality -= 5

    size_kb = os.path.getsize(output_path) / 1024
    print(f"✅ 背景色: {bg_color} {target_bg}  文字色: {target_text}")
    print(f"✅ 输出: {output_path}  {canvas.size}  {size_kb:.1f} KB")
    return output_path
```

### Step 4：调用示例

```python
FONT_DIR = "/mnt/skills/user/didi-driver-banner/fonts"

# 白色背景 → 黑色文字（自动）
replace_banner_bg(
    input_path  = "/mnt/user-data/uploads/banner_司机中心_750x230.jpg",
    output_path = "/mnt/user-data/outputs/banner_司机中心_750x230_white.jpg",
    bg_color    = "white",
    vz_x        = 490,
)

# 橙色背景 → 白色文字（自动）
replace_banner_bg(
    input_path  = "/mnt/user-data/uploads/banner_司机中心_750x230.jpg",
    output_path = "/mnt/user-data/outputs/banner_司机中心_750x230_orange.jpg",
    bg_color    = "orange",
    vz_x        = 490,
)
```

---

## 文字处理两种方案说明

| 方案 | 触发条件 | 效果 | 适用场景 |
|------|----------|------|----------|
| A：重绘文字 | 传入 `title_text` + `font_path` | 文字色精准，字体清晰 | 有原始文案和字体文件时 |
| B：蒙版着色 | 不传文字参数（默认） | 复用原文字形状，着色为目标色 | 快速替换，无需文案 |

---

## 参数调优说明

| 参数 | 默认值 | 调整时机 |
|------|--------|----------|
| `vz_x` | 490 | 图片区左边界不在 490px 时修改 |
| `orig_bg_color` | `(230, 234, 238)` | 原背景色偏差大时，用取色器重新采样 |
| `diff_sensitivity` | `5.0` | 文字提取不完整时调大（6~8）；有背景残留时调小（3~4） |

---

## 输出命名规范

```
banner_[类型]_[宽]x[高]_white.jpg
banner_[类型]_[宽]x[高]_orange.jpg
```
