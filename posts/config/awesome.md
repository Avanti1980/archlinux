　　awesome是个平铺式窗口管理器，跟i3相似，但是它的配置文件是用lua写的，更加灵活，连右键菜单都可以私人定制，因此可以把系统折腾成任意你想要的样子。

<br>

awesome装好后在/etc/xdg/awesome/下有默认的配置文件rc.lua，在/usr/share/awesome/themes下有5个备选的主题：

```shell
┌─[murongxixi@murongxixi-xps] - [~] - [五 8月 09, 22:24]
└─[$] <> ls /usr/share/awesome/themes 
default  gtk  sky  xresources  zenburn
```

如果用户自己不配置的话，或者用户自己的配置文件出问题时，系统都会滚回默认的配置。

<br>

　　用户自己配置的话，只需`mkdir ~/.config/awesome`，然后将默认的rc.lua和主题文件夹都复制过来，慢慢定制就行了：

```shell
┌─[murongxixi@murongxixi-xps] - [~] - [五 8月 09, 22:27]
└─[$] <> ls .config/awesome 
autorun.sh  rc.lua  themes
```

<br>

　　首先定义全局变量、导入标准库、处理启动/运行错误，这些都不用改

```lua
local awesome, client, mouse, screen, tag = awesome, client, mouse, screen, tag
local ipairs, string, os, table, tostring, tonumber, type = ipairs, string, os, table, tostring, tonumber, type

-- awesome标准库
local gears, awful, wibox, vicious = require("gears"), require("awful"), require("wibox"), require("vicious")
require("awful.autofocus")
local beautiful, naughty, menubar = require("beautiful"), require("naughty"), require("menubar")

-- awesome启动错误处理
if awesome.startup_errors then
  同默认配置文件 不需做改动
end

-- awesome运行错误处理
do
  local in_error = false
  同默认配置文件 不需做改动
end
```

<br>

　　导入当前目录下的themes/murongxixi/theme.lua，主要是壁纸和配色，后面单独说：

```lua
beautiful.init(awful.util.getdir("config") .. "/themes/murongxixi/theme.lua")
```

<br>

　　配置默认的终端、编辑器、浏览器、文件管理器、mod键：

```lua
terminal, editor, browser, filemanager, modkey = "urxvt", "code", "chromium", "pcmanfm", "Mod4"
editor_cmd = terminal .. " -e " .. editor
```

<br>

　　定制工作区的标签，键盘上正好有10个数字键可以当快捷键，就用了天干：

```lua
awful.util.tagnames = {"甲", "乙", "丙", "丁", "戊", "己", "庚", "辛", "壬", "癸"}
```

<br>

　　awesome布局，我只用环绕、最大化、浮动三种布局：

```lua
awful.layout.layouts = {awful.layout.suit.corner.nw, awful.layout.suit.max, awful.layout.suit.floating}
```

<br>

　　定制右键菜单：

```lua
myinputmenu = {  -- 二级菜单 三种输入法可以自由切换
  {"英  文", "fcitx-remote -s fcitx-keyboard-us"},
  {"中  文", "fcitx-remote -s sogoupinyin"},
  {"日  文", "fcitx-remote -s mozc"}
}

mywifimenu = {  -- 二级菜单
  {"关  闭", "nmcli device disconnect wlp3s0"},  -- 断开当前无线网络
  {"热  点", "nmcli connection up Hotspot"}      -- 创建热点
}

myawesomemenu = {  -- 二级菜单 awesome相关
  {"配  置", string.format("%s %s", editor, awesome.conffile)},
  {"主  题", string.format("%s %s/.config/awesome/themes/murongxixi/theme.lua", editor, os.getenv("HOME"))},
  {"启  动", string.format("%s %s/.config/awesome/autorun.sh", editor, os.getenv("HOME"))},
  {"重  启", awesome.restart},
  {
    "退  出",
    function()
      awesome.quit()
    end
  }
}

myconkymenu = {  -- 二级菜单 conky相关
  {"编  辑", string.format("%s %s/.config/conky/conky_murongxixi.lua", editor, os.getenv("HOME"))},
  {"编  辑", string.format("%s %s/.config/conky/conkyrc", editor, os.getenv("HOME"))},
  {"启  动", string.format("conky -c %s/.config/conky/conkyrc", os.getenv("HOME"))},
  {"退  出", "killall conky"}
}

myexitmenu = {  -- 二级菜单
  {
    "登  出",
    function()
      awesome.quit()
    end
  },
  {"待  机", "systemctl suspend"},
  {"休  眠", "systemctl hibernate"},
  {"重  启", "systemctl reboot"},
  {"关  机", "poweroff"}
}

mymainmenu =  -- 一级菜单 都是常用功能 不够可以继续加 非常爽
  awful.menu(
  {
    items = {
      {"终  端", terminal},
      {"网  络", browser},
      {"编  辑", editor},
      {"文  件", filemanager},
      {"微  信", "/usr/bin/electronic-wechat"},
      {"音  乐", "/usr/bin/audacious"},
      {"下  载", "/usr/bin/deluge"},
      {"输  入", myinputmenu},   -- 前面已定义
      {"无  线", mywifimenu},    -- 前面已定义
      {"桌  面", myawesomemenu}, -- 前面已定义
      {"监  视", myconkymenu},   -- 前面已定义
      {"退  出", myexitmenu}     -- 前面已定义
    }
  }
)
```

<br>

　　键盘布局不用改，之后定制任务栏，任务栏我只保留了三部分，从左到右依次是taglist、tasklist、widget，首先是taglist上鼠标动作定义(与默认配置一样，无需改动)：

```lua
local taglist_buttons =
  gears.table.join(
  awful.button( -- 1代表左键单击 查看这个tag
    {},
    1,
    function(t)
      t:view_only()
    end
  ),
  awful.button( -- 3代表右键单击 将当前这个client移到点击的tag中
    {},
    3,
    function(t)
      if client.focus then
        client.focus:move_to_tag(t)
      end
    end
  ),
  awful.button( -- 4代表中键上滚 切换到下一个tag
    {},
    4,
    function(t)
      awful.tag.viewnext(t.screen)
    end
  ),
  awful.button( -- 5代表中键下滚 切换到上一个tag
    {},
    5,
    function(t)
      awful.tag.viewprev(t.screen)
    end
  )
)
```

tasklist上鼠标动作定义跟taglist类似(与默认配置一样，无需改动)，只是左键单击变成了最小化，右键单击变成了显示所有正在运行的任务，就不贴了。

<br>

　　为每块屏幕设置壁纸，注意倒数第三行默认是`gears.wallpaper.maximized(wallpaper, s, true)`，参数true表示将壁纸拉伸到屏幕分辨率时不保持宽高比，当两者比例不匹配时会导致壁纸被拉长或压扁，故将其去掉：

```lua
local function set_wallpaper(s)
  if beautiful.wallpaper then
    local wallpaper = beautiful.wallpaper
    if type(wallpaper) == "function" then -- 如果wallpaper是一个函数
      wallpaper = wallpaper(s)
    end
    gears.wallpaper.maximized(wallpaper, s)
  end
end

-- 当一个屏的分辨率改变时 壁纸也跟着调整
screen.connect_signal("property::geometry", set_wallpaper)
```

<br>

　　设置cpu的widget，用到了vicious包：

```lua
cpuwidget =
  wibox.widget {
  forced_width = 60,
  border_width = 1,
  border_color = beautiful.fg_normal,
  color = beautiful.fg_normal,
  background_color = beautiful.bg_normal,
  widget = wibox.widget.graph  -- 用graph显示变化情况
}
cpuwidget_text = awful.tooltip({objects = {cpuwidget}})  -- 鼠标悬停时显示文本
vicious.register(
  cpuwidget,
  vicious.widgets.cpu,
  function(widget, args)
    cpuwidget_text:set_text(
      string.format("处理器总使用率：%s%%\n核心使用率：%s%%, %s%%, %s%%, %s%%", args[1], args[2], args[3], args[4], args[5])
    )
    return args[1]
  end,
  1
)

-- 镜像反转 从右向左走 同时控制间隔
local cpuwidget = wibox.container.margin(wibox.container.mirror(cpuwidget, {horizontal = true}), 5, 10, 4, 4)
```

<br>

　　设置内存的widget：

```lua
membar =
  wibox.widget {
  border_color = beautiful.fg_normal,
  bg = "#DDDDFF30",  -- 通过30%的透明度来表示未使用的部分
  color = beautiful.fg_normal,
  border_width = 1,
  forced_width = 32,
  forced_height = 32,
  min_value = 0,
  max_value = 1,
  thickness = 3,
  widget = wibox.container.arcchart  -- 用角度环显示使用率
}
memwidget_text = awful.tooltip({objects = {membar}})
awful.widget.watch(  -- 通过watch监控
  [[bash -c ""]],
  10,  -- 每10秒更新一次
  function(widget, stdout)
    mem_now = {}
    for line in io.lines("/proc/meminfo") do
      for k, v in string.gmatch(line, "([%a]+):[%s]+([%d]+).+") do
        if k == "MemTotal" then
          mem_now.total = v
        elseif k == "MemAvailable" then
          mem_now.ava = v
        end
      end
    end

    mem_now.used = mem_now.total - mem_now.ava
    used_perc = mem_now.used / mem_now.total
    widget.value = used_perc
    memwidget_text:set_text(
      string.format("内存消耗：%sMiB\n / %sMiB", math.floor(mem_now.used / 1024), math.floor(mem_now.total / 1024))
    )
  end,
  membar
)
local memwidget = wibox.container.margin(wibox.container.mirror(membar, {horizontal = true}), 0, 10, 4, 4)

```

磁盘、电池、温度、亮度、声音都是用的arcchart，与内存类似，不一一贴了。

<br>

　　日期和时间：

```lua
day_name = {"月", "火", "水", "木", "金", "土", "日"}
big_name = {"壹", "贰", "叁", "肆", "伍", "陆", "柒", "捌", "玖", "拾", "廿", "卅"}

function to_large(num)  -- 数字转大写 例如14会变成拾肆
  if num <= 10 then
    return big_name[num]
  elseif num < 20 then
    return big_name[10] .. big_name[num - 10]
  elseif num == 20 then
    return big_name[11]
  elseif num < 30 then
    return big_name[11] .. big_name[num - 20]
  elseif num == 30 then
    return big_name[12]
  else
    return big_name[12] .. big_name[num - 30]
  end
end

-- 设置日期
local date =
  awful.widget.watch(
  "date +'%m%d'",
  60,
  function(widget, stdout)
    widget:set_markup(
      to_large(tonumber(string.sub(stdout, 1, 2))) .. "·" .. to_large(tonumber(string.sub(stdout, 3, 4)))
    )
    widget:set_font(beautiful.font)
  end
)
local datewidget = wibox.container.margin(date, 5, 5, 2, 2)

-- 设置星期
local wday =
  awful.widget.watch(
  "date +'%w'",
  60,
  function(widget, stdout)
    local w = tonumber(stdout)
    if w == 0 then -- date + %w 返回的是0~6 0表示周日
        w = w + 7
    end
    widget:set_markup(day_name[w])  -- 转成七曜
    widget:set_font(beautiful.font)
  end
)
local wdaywidget = wibox.container.margin(wday, 5, 5, 2, 2)

-- 设置时间
local clock =
  awful.widget.watch(
  "date +'%R'",  -- 只显示时和分
  1,
  function(widget, stdout)
    widget:set_markup(stdout)
    widget:set_font("murongxixi 14")
  end
)
local clockwidget = wibox.container.margin(clock, 5, 10, 2, 2)
```

<br>

　　为每块屏幕执行如下代码：

```lua
awful.screen.connect_for_each_screen(
  function(s)
    set_wallpaper(s)  -- 设置壁纸
    awful.tag(  -- 添加tag
      awful.util.tagnames,
      s,
      {
        awful.layout.layouts[1],  -- 这里可以为每个tag选择不同的默认布局
        awful.layout.layouts[1],
        awful.layout.layouts[1],
        awful.layout.layouts[1],
        awful.layout.layouts[1],
        awful.layout.layouts[1],
        awful.layout.layouts[1],
        awful.layout.layouts[1],
        awful.layout.layouts[1],
        awful.layout.layouts[1]
      }
    ) --
    s.mypromptbox = awful.widget.prompt()
    s.mytaglist =
      awful.widget.taglist {screen = s, filter = awful.widget.taglist.filter.all, buttons = taglist_buttons}
    s.mytasklist =  -- 设置task
      wibox.container.margin(
      awful.widget.tasklist {
        screen = s,
        filter = awful.widget.tasklist.filter.currenttags,
        buttons = tasklist_buttons,
        style = {
          font = "murongxixi 14"  -- task字体 这是我自己手动将苏新诗柳楷和fira code合并的字体
        },
        layout = {
          spacing = 20,
          layout = wibox.layout.flex.horizontal
        },
        widget_template = {
          {
            {
              {
                {
                  id = "icon_role",
                  widget = wibox.widget.imagebox
                },
                margins = 2,
                widget = wibox.container.margin  -- 控制图标的间隔
              },
              {
                id = "text_role",
                widget = wibox.widget.textbox
              },
              layout = wibox.layout.fixed.horizontal
            },
            left = 10,
            right = 10,
            widget = wibox.container.margin  -- 控制文本的间隔
          },
          id = "background_role",
          widget = wibox.container.background
        }
      },
      10,
      10,
      2,
      2
    )
    s.mywibox = awful.wibar({position = "top", height = 40, screen = s})  -- 任务栏位置和高度
    s.mywibox:setup {
      layout = wibox.layout.align.horizontal,  -- 水平方向布局
      {layout = wibox.layout.fixed.horizontal, s.mytaglist, s.mypromptbox},  -- 左
      s.mytasklist,  -- 中
      {
        layout = wibox.layout.fixed.horizontal,
        wibox.widget.textbox("核"),
        cpuwidget,
        wibox.widget.textbox("存"),
        memwidget,
        wibox.widget.textbox("磁"),
        diskrootwidget,
        diskhomewidget,
        wibox.widget.textbox("电"),
        batwidget,
        wibox.widget.textbox("温"),
        tempwidget,
        wibox.widget.textbox("亮"),
        brightnesswidget,
        wibox.widget.textbox("声"),
        volumewidget,
        -- wibox.widget.systray(),  -- 我不用系统托盘
        datewidget,
        wdaywidget,
        clockwidget
      }  -- 右
    }
  end
)
```

<br>

　　右键调出主菜单(与默认配置一样，无需改动)：

```lua
root.buttons(
  gears.table.join(
    awful.button(
      {},
      3,
      function()
        mymainmenu:toggle()
      end
    ),
    awful.button({}, 4, awful.tag.viewnext),
    awful.button({}, 5, awful.tag.viewprev)
  )
)
```

<br>

　　全局快捷键，我自己加了一些：

```lua
globalkeys =
	gears.table.join(
	awful.key(  -- mod + p 深度截图
    {modkey},
    "p",
    function()
      awful.spawn("/usr/bin/deepin-screenshot")
    end
  ),
  awful.key(  -- mod + r 启动rofi
    {modkey},
    "r",
    function()
      awful.spawn("/usr/bin/rofi -show drun -modi drun")
    end
  ),
  awful.key(  -- 功能键 减小屏幕亮度 需要装上xorg-xbacklight
    {},
    "XF86MonBrightnessDown",
    function()
      awful.util.spawn_with_shell("xbacklight -dec 5")
    end
  ),
  awful.key(  -- 功能键 增大屏幕亮度
    {},
    "XF86MonBrightnessUp",
    function()
      awful.util.spawn_with_shell("xbacklight -inc 5")
    end
  ),
  awful.key(  -- 功能键 减小键盘亮度 需要先将kb-light.py放在/usr/local/bin/目录下
    {},
    "XF86KbdBrightnessDown",
    function()
      awful.util.spawn_with_shell("kb-light.py - 10")
    end
  ),
  awful.key(  -- 功能键 增大键盘亮度
    {},
    "XF86KbdBrightnessUp",
    function()
      awful.util.spawn_with_shell("kb-light.py + 10")
    end
  ),
  awful.key(  -- 功能键 减小音量
    {},
    "XF86AudioLowerVolume",
    function()
      awful.util.spawn_with_shell("amixer set Master 5%-")
    end
  ),
  awful.key(  -- 功能键 增大音量
    {},
    "XF86AudioRaiseVolume",
    function()
      awful.util.spawn_with_shell("amixer set Master 5%+")
    end
  ),
  awful.key(  -- 静音
    {},
    "XF86AudioMute",
    function()
      awful.util.spawn_with_shell("amixer set Master toggle")
    end
  )
)
```

非全局快捷键也改了些，按个人喜好，不一一贴了，另外每个快捷键默认都有一个description说明功能的，这个会在帮助手册中归类显示，我用不着就全删了。

<br>

　　将10个数字键绑到10个tag上：

```lua
for i = 0, 9 do
  if i == 0 then  -- 默认只绑1到9 0需要自己处理
    i = 10
  end
  globalkeys =
    gears.table.join(
    globalkeys,
    awful.key(
      {modkey},
      "#" .. i + 9,
      function()
        local screen = awful.screen.focused()
        local tag = screen.tags[i]
        if tag then
          tag:view_only()
        end
      end
    ),
    awful.key(
      {modkey, "Control"},
      "#" .. i + 9,
      function()
        local screen = awful.screen.focused()
        local tag = screen.tags[i]
        if tag then
          awful.tag.viewtoggle(tag)
        end
      end
    ),
    awful.key(  -- mod + shift + #  将当前任务移至tag#
      {modkey, "Shift"},
      "#" .. i + 9,
      function()
        if client.focus then
          local tag = client.focus.screen.tags[i]
          if tag then
            client.focus:move_to_tag(tag)
          end
        end
      end
    ),
    awful.key(
      {modkey, "Control", "Shift"},
      "#" .. i + 9,
      function()
        if client.focus then
          local tag = client.focus.screen.tags[i]
          if tag then
              client.focus:toggle_tag(tag)
          end
        end
      end
    )
  )
end
```

<br>

　　规则：

```lua
if screen.count() == 1 then  -- 如果只连了一块屏幕
  awful.rules.rules = {
    -- 通用规则
    {
      rule = {},
      properties = {
        focus = awful.client.focus.filter,
        raise = true,
        keys = clientkeys,
        buttons = clientbuttons,
        screen = awful.screen.preferred,
        placement = awful.placement.no_overlap + awful.placement.no_offscreen,
        size_hints_honor = false -- 去掉终端间的间隙
      }
    },
    -- 浮动的应用
    {
      rule_any = {
        instance = {
          "DTA", -- Firefox addon DownThemAll.
          "copyq", -- Includes session name in class.
          "pinentry"
        },
        class = {
          "Arandr",
          "Blueman-manager",
          "Gpick",
          "Kruler",
          "MessageWin", -- kalarm.
          "Sxiv",
          "Tor Browser", -- Needs a fixed window size to avoid fingerprinting by screen size.
          "Wpa_gui",
          "veromix",
          "xtightvncviewer",
          "mpv",  -- 播放器
          "deepin-movie",  -- 播放器
          "Guake",  -- 顶部终端
          "Tilda",  -- 顶部终端
          "Matplotlib"  -- python画图
        },
        -- Note that the name property shown in xprop might be set slightly after creation of the client
        -- and the name shown there might not match defined rules here.
        name = {
          "Event Tester" -- xev.
        },
        role = {
          "AlarmWindow", -- Thunderbird's calendar.
          "ConfigManager", -- Thunderbird's about:config.
          "pop-up" -- e.g. Google Chrome's (detached) Developer Tools.
        }
      },
      properties = {floating = true}
    },
    -- 以下是每个软件的默认打开tag 比如vscode默认在tag3
    -- 第1个工作区
    {
      rule = {class = "URxvt"},
      properties = {screen = 1, tag = awful.util.tagnames[1]}
    },
    -- 第2个工作区
    {
      rule = {class = "Chromium"},
      properties = {screen = 1, tag = awful.util.tagnames[2]}
    },
    -- 第3个工作区
    {
      rule = {class = "code-oss"},
      properties = {screen = 1, tag = awful.util.tagnames[3]}
    },
    {
      rule = {class = "matplotlib"},
      properties = {screen = 1, tag = awful.util.tagnames[3]}
    },
    -- 第4个工作区 文件管理器
    {
      rule = {class = "Pcmanfm"},
      properties = {screen = 1, tag = awful.util.tagnames[4]}
    },
    -- 第5个工作区 pdf
    {
      rule = {class = "Evince"},
      properties = {screen = 1, tag = awful.util.tagnames[5]}
    },
    {
      rule = {class = "Master PDF Editor"},
      properties = {screen = 1, tag = awful.util.tagnames[5]}
    },
    -- 第6个工作区 wps
    {
      rule = {class = "Wps"},
      properties = {screen = 1, tag = awful.util.tagnames[6]}
    },
    {
      rule = {class = "Wpp"},
      properties = {screen = 1, tag = awful.util.tagnames[6]}
    },
    {
      rule = {class = "Et"},
      properties = {screen = 1, tag = awful.util.tagnames[6]}
    },
    -- 第7个工作区 微信
    {
      rule = {class = "electronic-wechat"},
      properties = {screen = 1, tag = awful.util.tagnames[7]}
    },
    {
      rule = {class = "Wine"},
      properties = {screen = 1, tag = awful.util.tagnames[7]}
    },
    -- 第8个工作区 视频
    {
      rule = {class = "vsedit"},
      properties = {screen = 1, tag = awful.util.tagnames[8]}
    },
    -- 第9个工作区 音乐
    {
      rule = {class = "Audacious"},
      properties = {screen = 1, tag = awful.util.tagnames[9]}
    },
    -- 第10个工作区 下载 翻墙
    {
      rule = {class = "Deluge"},
      properties = {screen = 1, tag = awful.util.tagnames[10]}
    },
    {
      rule = {class = "transmission"},
      properties = {screen = 1, tag = awful.util.tagnames[10]}
    },
    {
      rule = {class = "shadowsocks-qt5"},
      properties = {screen = 1, tag = awful.util.tagnames[10]}
    }
  }
else  -- 如果连了多块屏幕
	每个软件可以默认在其他屏上打开 不一一贴了
end
```

<br>

　　还有些信号处理函数，例如焦点跟随鼠标，无需改动，最后是自启动脚本：

```lua
awful.spawn.with_shell("~/.config/awesome/autorun.sh")
```

其内容如下：

```shell
#!/usr/bin/env bash

function run() {
  if ! pgrep $1; then
    $@ &
  fi
}

run /usr/lib/mate-polkit/polkit-mate-authentication-agent-1 &
run compton --config ~/.config/compton.conf
run udiskie -ans
run fcitx
run guake
run /home/murongxixi/.config/conky/start_conky
run /home/murongxixi/Softwares/XX-Net/start
```

<br>

　　主题文件 theme.lua：

```lua
--  "murongxixi" awesome theme By murongxixi

local themes_path = os.getenv("HOME") .. "/.config/awesome/themes/"  -- 主题文件夹目录

local theme = {}
theme.wallpaper = themes_path .. "murongxixi/wallpaper.jpg"  -- 壁纸

theme.font = "murongxixi 16"  -- 默认字体

theme.fg_normal = "#DDDDFF"
theme.fg_focus = "#00D700"
theme.fg_urgent = "#DCDCCC"
theme.bg_normal = "#3F3F3F00"
theme.bg_focus = "#1E232000"
theme.bg_urgent = "#3F3F3F00"

theme.border_width = 0

-- 下面这些变量会覆盖上面的变量
-- [taglist|tasklist]_[bg|fg]_[focus|urgent|occupied|empty|volatile]
-- titlebar_[normal|focus]
-- tooltip_[font|opacity|fg_color|bg_color|border_width|border_color]
-- Example: theme.taglist_bg_focus = "#CC9393"

--theme.fg_widget        = "#AECF96"
--theme.fg_center_widget = "#88A175"
--theme.fg_end_widget    = "#FF5656"
--theme.bg_widget        = "#494B4F"
--theme.border_widget    = "#3F3F3F"

theme.mouse_finder_color = "#CC9393"
-- mouse_finder_[timeout|animate_timeout|radius|factor]

-- menu_[bg|fg]_[normal|focus]
-- menu_[border_color|border_width]
theme.menu_height = 40  -- 右键菜单高度
theme.menu_width = 200  -- 右键菜单宽度
-- theme.menu_border_width = 2  -- 右键菜单边框

-- tag有任务打开和获得焦点时的标识 默认的正方形我不喜欢 自己设计了两个不同颜色的小直角标记
theme.taglist_squares_sel = themes_path .. "murongxixi/icons/square_sel.png"
theme.taglist_squares_unsel = themes_path .. "murongxixi/icons/square_unsel.png"
--theme.taglist_squares_resize = "false"

theme.useless_gap = 0  -- 平铺窗口间的间距 屏幕小挥霍不起 直接设成零

return theme
```
