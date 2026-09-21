# mac上实现"边写边译"的功能

## **背景**

目前 mac 一直缺少一个免费的"边写边译"的功能.要想把中文翻译成英文,得用鼠标点点点,一通操作才能实现.

chrome浏览器有一个叫"沉浸式翻译"的插件,按下三下空格,就能将输入框里的文本翻译成英文.我自己体验下来觉得有三个场景它不满足

*   只能在浏览器里使用
*   不支持仅翻译选中的文本,只能完全翻译整句话
*   连续按 3 下空格,不是特别舒服,有种紧张感,实测不如快捷键

## **解决方案**

用自动化的脚本实现

脚本思路: 模拟 cmd+c -> 调用翻译接口 -> 模拟 cmd+v

这里的翻译接口可以用快捷指令实现,如下图:

![](https://github.com/user-attachments/assets/8ce20a2e-9fcc-419f-a005-766b3b06f655)

可以用下面的 icloud 链接快速添加该快捷指令

```
https://www.icloud.com/shortcuts/f02cd8ebbf1d46338cfaee2fdbbd4734
```

模拟键盘操作,我使用的是 hammerspoon 这个软件,免费开源的,先安装一下.辅助权限什么的都打开.

启动成功后,要编辑脚本.脚本文件在如图位置打开

![](https://github.com/user-attachments/assets/c3664e2f-430e-4f92-bcd6-dd5b083dd030)


点击上图的 open config 按钮,就会自动打开一个叫做 init.lua 的脚本文件

把下图的代码粘贴进去就行了

```lua
local log = hs.logger.new("translate2En", "info")

-- 获取当前选中的文字（优先用 Accessibility）
local function getSelectedText()
    local app = hs.application.frontmostApplication()
    if not app then return nil end

    local axApp = hs.axuielement.applicationElement(app)
    if not axApp then return nil end

    local focused = axApp:attributeValue("AXFocusedUIElement")
    if not focused then
        log.e("没有获取到焦点元素")
        return nil
    end

    local selected = focused:attributeValue("AXSelectedText")
    if selected and selected ~= "" then
        return selected
    end

    return nil
end

-- 回退方案：模拟 Cmd+C 并等待剪贴板变化
local function copyWithCmdC()
    local oldContent = hs.pasteboard.getContents() or ""
    local oldChangeCount = hs.pasteboard.changeCount()

    hs.eventtap.keyStroke({"cmd"}, "c")

    -- 等待剪贴板变化，最多等 1 秒
    local start = hs.timer.secondsSinceEpoch()
    while hs.timer.secondsSinceEpoch() - start < 1.0 do
        if hs.pasteboard.changeCount() ~= oldChangeCount then
            local newContent = hs.pasteboard.getContents()
            if newContent and newContent ~= oldContent then
                return newContent
            end
        end
        hs.timer.usleep(30000) -- 30ms
    end

    log.e("Cmd+C 复制失败或超时")
    return nil
end

-- 主功能
function translate2En()
    -- 1. 获取选中文字
    local text = getSelectedText()

    if not text or text == "" then
        text = copyWithCmdC()

        if not text or text == "" then
            hs.alert.show("没有获取到选中文字")
            log.e("没有获取到选中文字")
            return
        end
    else
        hs.pasteboard.setContents(text)
    end

    -- 3. 运行快捷指令
    local output, status, typ, rc = hs.execute('shortcuts run "翻译成英文"', true)
    if rc ~= 0 then
        hs.alert.show("快捷指令执行失败")
        log.e("快捷指令失败: " .. (output or ""))
        return
    end
    if output then
        -- 4. 更新剪贴板（清空+设置）
        hs.pasteboard.clearContents()
        hs.pasteboard.setContents(output)
        -- 5. 粘贴翻译文本
        hs.eventtap.keyStroke({"cmd"}, "v")
    end
end

```

粘贴完了以后,保存一下文件,然后重新用 hammerspoon 加载一下脚本,重新加载的方式如下图:

![](https://github.com/user-attachments/assets/82888ec7-f8e2-4618-9985-918da625f5e5)

如果加载成功,就可以用了

按下快捷键option + shift + i 就是把选中的文本翻译成英文

按下快捷键option + shift + o 就是自动全选,然后翻译成英文
