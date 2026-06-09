# name: Banner 类型映射规范
> 适用于滴滴司机端 Banner，依据 DiDi Brand Guidelines 2025 · B.2.14–B.2.17

## description:类型映射表

| Banner 类型 | 画布尺寸 | 按钮 | 主标题上限 | 副标题上限 | 画面位置 |
|------------|---------|------|-----------|-----------|----------|
| 司机中心 Banner | 750 × 230 px | 无 | ≤12 个字符 | ≤16 个字符 | 右侧 |
| 司机生活页 Banner | 1065 × 300 px | 有 | ≤8 个字符 | ≤12 个字符 | 右侧 |
| 车主圈 Banner 形式一 | 1065 × 300 px | 无 | ≤8 个字符 | ≤12 个字符 | 右侧 |
| 车主圈 Banner 形式二 | 1065 × 300 px | 无 | ≤8 个字符 | ≤12 个字符 | 左侧 |
| 司机部落首页 Banner | 702 × 240 px | 无 | ≤8 个字符 | ≤12 个字符 | 右侧 |

---

## 代码映射

```python
BANNER_TYPE_MAP = {
    "司机中心": {
        "canvas":        (750, 230),
        "has_button":    False,
        "title_max":     12,
        "sub_max":       16,
        "visual_side":   "right",
        "circle_diam":   180,
        "circle_cx":     610,   # 750 - 50 - 90
        "circle_cy":     115,   # 230 / 2
    },
    "司机生活页": {
        "canvas":        (1065, 300),
        "has_button":    True,
        "title_max":     8,
        "sub_max":       12,
        "visual_side":   "right",
        "circle_diam":   230,
        "circle_cx":     825,   # 640 + 185
        "circle_cy":     150,
    },
    "车主圈形式一": {
        "canvas":        (1065, 300),
        "has_button":    False,
        "title_max":     8,
        "sub_max":       12,
        "visual_side":   "right",
        "circle_diam":   230,
        "circle_cx":     880,   # 695 + 185
        "circle_cy":     150,
    },
    "车主圈形式二": {
        "canvas":        (1065, 300),
        "has_button":    False,
        "title_max":     8,
        "sub_max":       12,
        "visual_side":   "left",
        "circle_diam":   230,
        "circle_cx":     185,   # 0 + 185
        "circle_cy":     150,
    },
    "司机部落": {
        "canvas":        (702, 240),
        "has_button":    False,
        "title_max":     8,
        "sub_max":       12,
        "visual_side":   "right",
        "circle_diam":   200,   # 待确认
        "circle_cx":     602,   # 702 - 50 - 100
        "circle_cy":     120,
    },
}
```

---

## 校验函数

```python
def validate_banner_text(banner_type: str, title: str, sub: str = "") -> bool:
    """
    校验主标题和副标题是否符合该类型字符上限。
    1个中文 / 1个英文 / 1个数字 / 1个标点 均计为1个字符。
    """
    spec = BANNER_TYPE_MAP.get(banner_type)
    if not spec:
        raise ValueError(f"未知Banner类型：{banner_type}")

    title_len = len(title)
    sub_len   = len(sub)
    ok = True

    if title_len > spec["title_max"]:
        print(f"❌ 主标题超限：{title_len} 字符，上限 {spec['title_max']}")
        ok = False
    else:
        print(f"✅ 主标题：{title_len}/{spec['title_max']} 字符")

    if sub and sub_len > spec["sub_max"]:
        print(f"❌ 副标题超限：{sub_len} 字符，上限 {spec['sub_max']}")
        ok = False
    elif sub:
        print(f"✅ 副标题：{sub_len}/{spec['sub_max']} 字符")

    if spec["has_button"]:
        print(f"ℹ️  此类型需要按钮")
    else:
        print(f"ℹ️  此类型无需按钮")

    return ok
```

### 调用示例

```python
# 校验文案
validate_banner_text("司机中心", title="早高峰瓜分千万奖金", sub="跑赢早高峰，瓜分千万奖金")
# ✅ 主标题：9/12 字符
# ✅ 副标题：14/16 字符
# ℹ️  此类型无需按钮

validate_banner_text("司机生活页", title="早高峰奖励", sub="跑赢早高峰瓜分千万")
# ✅ 主标题：5/8 字符
# ✅ 副标题：9/12 字符
# ℹ️  此类型需要按钮

# 读取类型配置
spec = BANNER_TYPE_MAP["车主圈形式一"]
print(spec["canvas"])        # (1065, 300)
print(spec["visual_side"])   # right
print(spec["has_button"])    # False
```
