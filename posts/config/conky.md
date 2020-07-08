　　conky是个系统监视工具，下图右半边就是一个运行中的conky：
![conky](https://raw.githubusercontent.com/murongxixi/Arch/master/img/with-conky.png)
图里的各种圆环是用cairo画的，extra/conky编译时没开启对cairo的支持，必须用aur/conky-lua-nv才行，当然如果不画圆环的话就没必要用这个了。

<br>

　　conky全部配置好有三个文件，首先是启动脚本：

```shell
┌─[murongxixi@murongxixi-xps] - [~] - [五 8月 09, 02:56]
└─[$] <> cat .config/conky/start_conky
#!/bin/bash
conky -c ~/.config/conky/conkyrc &
```

也可以把`conky -c ~/.config/conky/conkyrc`直接写到awesome的autorun.sh里，这样可以少一个文件。

<br>

　　主文件

```lua
conky.config = {
  update_interval = 1,
  double_buffer = true,
  own_window_class = Conky,
  no_buffers = true,
  own_window = true,
  own_window_type = 'override',
  own_window_transparent = true,
  draw_shades = false,
  minimum_width = 740,
  minimum_height = 2160,
  alignment = 'top_right',
  gap_x = 30,
  gap_y = 40,
  border_inner_margin = 0,
  border_outer_margin = 0,
  default_color = 'DDDDFF',
  lua_load = '~/.config/conky/conky_murongxixi.lua',
  lua_draw_hook_pre = 'main',
}
conky.text = [[
  ${voffset 410}${alignr}${cpugraph cpu1 60,100 DDDDFF}${cpugraph cpu2 60,100 DDDDFF}${cpugraph cpu3 60,100 DDDDFF}${cpugraph cpu4 60,100 DDDDFF}
  ${voffset -10}${alignr}${cpugraph cpu5 60,100 DDDDFF}${cpugraph cpu6 60,100 DDDDFF}${cpugraph cpu7 60,100 DDDDFF}${cpugraph cpu8 60,100 DDDDFF}
  ${voffset -10}${alignr}${cpugraph cpu9 60,100 DDDDFF}${cpugraph cpu10 60,100 DDDDFF}${cpugraph cpu11 60,100 DDDDFF}${cpugraph cpu12 60,100 DDDDFF}
  ${voffset -10}${alignr}${cpugraph cpu13 60,100 DDDDFF}${cpugraph cpu14 60,100 DDDDFF}${cpugraph cpu15 60,100 DDDDFF}${cpugraph cpu16 60,100 DDDDFF}
  ${voffset 1125}
  ${goto 0}${upspeedgraph enp0s20f0u2u3 50,220 DDDDFF DDDDFF 12500 -t -l}${alignr}${upspeedgraph wlp59s0 50,220 DDDDFF DDDDFF 12500 -t -l}
  ${goto 0}${voffset 85}${downspeedgraph enp0s20f0u2u3 50,220 DDDDFF DDDDFF 12500 -t -l}${alignr}${downspeedgraph wlp59s0 50,220 DDDDFF DDDDFF 12500 -t -l}
]]
```

分为两部分：

- conky.config中是一些全局配置，比如画布的宽度、高度、对齐方法、与屏幕边缘的间隔、默认颜色等；
- conky.text是用来在画布上绘图的，`cpugraph`将cpu的运行情况画成图，`upspeedgraph`和`downspeedgraph`将网络的上传/下载情况画成图。
  
<br>

　　上图中除了cpu和网络的运行图外，剩下都是由lua脚本conky_murongxixi.lua绘制的，其结构如下：

```lua
require "cairo"

rings_table = {
	{
    name = "time", -- 命令名称
    arg = "%I.%M", -- 命令参数
    max = 12, -- 最大值
    bg_color = default_bg_color, -- 前景
    bg_alpha = clock_alpha[1]["bg_alpha"], -- 前景透明度
    fg_color = default_fg_color, -- 背景
    fg_alpha = clock_alpha[1]["fg_alpha"], -- 背景透明度
    x = clock_x, -- 圆心横坐标
    y = clock_y, -- 圆心纵坐标
    radius = hour_min_sec_radius[1], -- 半径
    thickness = hour_min_sec_thickness[1], -- 圆环厚度
    start_angle = 0, -- 起始角度
    end_angle = 360, -- 结束角度
    graduated = true -- 是否分格
  },
  {
    其他环的各种参数
  },
  ……
}

function draw_ring(cr, value, pt)
	local w, h = conky_window.width, conky_window.height

	-- 读取圆环的所有参数
  local xc, yc, ring_r, ring_w, sa, ea, max, graduated =
    pt["x"],
    pt["y"],
    pt["radius"],
    pt["thickness"],
    pt["start_angle"],
    pt["end_angle"],
    pt["max"],
    pt["graduated"]

  local bgc, bga, fgc, fga = pt["bg_color"], pt["bg_alpha"], pt["fg_color"], pt["fg_alpha"]

  local angle_0 = sa * (2 * math.pi / 360) - math.pi / 2 -- 起始弧度
  local angle_f = ea * (2 * math.pi / 360) - math.pi / 2 -- 结束弧度

  cairo_set_line_width(cr, ring_w) -- 设置画笔为圆环厚度

  if value > max then
    value = value - max
  end

  -- 先画背景环
  if graduated then -- 如果是分格的
    for i = 1, max do -- 背景环与max值有关
      local angle_start = angle_0 + (i - 1) * (ea - sa) * 2 * math.pi / (max * 360)
      local angle_end = angle_0 + (i * (ea - sa) / max - clock_gap) * 2 * math.pi / 360
      cairo_arc(cr, xc, yc, ring_r, angle_start, angle_end)
      cairo_set_source_rgba(cr, rgb_to_r_g_b(bgc, bga))
      cairo_stroke(cr)
    end
  else -- 不分格 直接根据起始/结束弧度画
    cairo_arc(cr, xc, yc, ring_r, angle_0, angle_f)
    cairo_set_source_rgba(cr, rgb_to_r_g_b(bgc, bga))
    cairo_stroke(cr)
  end

  -- 再画前景环
  if graduated then
    for i = 1, value do -- 前景环与命令的返回值有关
      local angle_start = angle_0 + (i - 1) * (ea - sa) * 2 * math.pi / (max * 360)
      local angle_end = angle_0 + (i * (ea - sa) / max - clock_gap) * 2 * math.pi / 360
      cairo_arc(cr, xc, yc, ring_r, angle_start, angle_end)
      cairo_set_source_rgba(cr, rgb_to_r_g_b(fgc, fga))
      cairo_stroke(cr)
    end
  else
    -- 获得百分比
    if max == 12500 then --如果是网络 采用对数拉伸
      if value < 1 then
        t = 0
      else
        t = math.log10(value) / math.log10(max)
      end
    else
      t = value / max -- 获取前景的百分比
    end
    local t_arc = t * (angle_f - angle_0) -- 百分比 * 2 pi
    cairo_arc(cr, xc, yc, ring_r, angle_0, angle_0 + t_arc)
    cairo_set_source_rgba(cr, rgb_to_r_g_b(fgc, fga))
    cairo_stroke(cr)
  end
end

function conky_main()
  local function setup_rings(cr, pt) -- pt是一个table
    local str = ""
    local value = 0

    -- 执行一条conky指令 例如 time %I.%M
    str = string.format("${%s %s}", pt["name"], pt["arg"])
    value = tonumber(conky_parse(str))

    -- 根据命令的结果画圆环
    draw_ring(cr, value, pt)
  end

  -- 获取画布
  if conky_window == nil then
    return
  end

  local cs =
    cairo_xlib_surface_create(
    conky_window.display,
    conky_window.drawable,
    conky_window.visual,
    conky_window.width,
    conky_window.height
  )

  local cr = cairo_create(cs)
  cairo_surface_destroy(cs)

  local updates = conky_parse("${updates}")
  update_num = tonumber(updates)

  -- Conky已运行5秒 依次处理rings_table中的每个圆环
  if update_num > 5 then
    for i in pairs(rings_table) do
      setup_rings(cr, rings_table[i])
    end
  end

  cairo_destroy(cr)
end
```

<br>

　　除了用`cairo_arc`绘制圆环外，还可以绘制直线：

```lua
cairo_move_to(cr, xc_start, yc_start) -- 起点横纵坐标
cairo_line_to(cr, xc_end, yc_end) -- 终点横纵坐标
cairo_set_line_width(cr, width) -- 宽度
cairo_stroke(cr)
```

以及写字：

```lua
cairo_select_font_face(cr, font_name, CAIRO_FONT_SLANT_NORMAL, CAIRO_FONT_WEIGHT) -- 字体
cairo_set_font_size(cr, font_size) -- 字号
cairo_move_to(cr, xc, yc) -- 起点横纵坐标
cairo_set_source_rgba(cr, rgb_to_r_g_b(default_fg_color, 1.0)) -- 颜色 透明度
cairo_show_text(cr, str)
```

<br>

　　用cairo写字，默认是左对齐的，要想居中对齐，得先算出文本的宽度和高度：

```lua
local extents = cairo_text_extents_t:create()
tolua.takeownership(extents)
local x, y
cairo_select_font_face(cr, font_name, CAIRO_FONT_SLANT_NORMAL, CAIRO_FONT_WEIGHT)
cairo_set_font_size(cr, font_size)
cairo_text_extents(cr, str, extents) -- 计算str的长度
x = xc - (extents.width / 2 + extents.x_bearing) -- 向左移动半个字符串的长度
y = yc - (extents.height / 2 + extents.y_bearing) -- 向上移动半个字符串的高度
cairo_move_to(cr, x, y)
cairo_set_source_rgba(cr, rgb_to_r_g_b(default_fg_color, 1.0))
cairo_show_text(cr, str)
```

<br>

　　右对齐也是类似的处理方法，不过计算宽度时空格不算进去，对于"a b"这样的字符串，还得先将空格替换掉：

```lua
str = str:gsub(string.rep(" ", start_space_len), string.rep("a", start_space_len))
local extents = cairo_text_extents_t:create()
tolua.takeownership(extents)
local x, y
cairo_select_font_face(cr, font_name, CAIRO_FONT_SLANT_NORMAL, CAIRO_FONT_WEIGHT)
cairo_set_font_size(cr, font_size)
cairo_text_extents(cr, str, extents)
x = conky_window.width - (extents.width + 2 * extents.x_bearing) -- 向左移动整个字符串长度
y = yc
cairo_move_to(cr, x, y)
cairo_set_source_rgba(cr, rgb_to_r_g_b(default_fg_color, 1.0))
str = str:gsub(string.rep("a", start_space_len), string.rep(" ", start_space_len))
cairo_show_text(cr, str)
```
