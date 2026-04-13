local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")
local TweenService = game:GetService("TweenService")

local Lib = { connections = {} }

local function track(conn)
    table.insert(Lib.connections, conn)
    return conn
end

Lib.THEME = {
    BG_MAIN = Color3.fromRGB(20, 20, 25),
    BG_SIDE = Color3.fromRGB(15, 15, 20),
    BG_CONTENT = Color3.fromRGB(25, 25, 30),
    ACCENT = Color3.fromRGB(155, 89, 182),
    TEXT = Color3.fromRGB(240, 240, 240),
    TEXT_DIM = Color3.fromRGB(150, 150, 150),
    GREEN = Color3.fromRGB(50, 200, 50),
    RED = Color3.fromRGB(220, 50, 50),
    DARK_ITEM = Color3.fromRGB(35, 35, 40)
}

local function corner(p, r) 
    local c = Instance.new("UICorner") 
    c.CornerRadius = UDim.new(0, r or 8) 
    c.Parent = p
    return c 
end

local function stroke(p, c, t, b) 
    local s = Instance.new("UIStroke") 
    s.Color = c or (Lib.THEME.ACCENT) 
    s.Thickness = t or 1 
    s.ApplyStrokeMode = Enum.ApplyStrokeMode.Border 
    s.Parent = p
    return s 
end

local function tween(o, p, d) 
    local t = TweenService:Create(o, TweenInfo.new(d or 0.3, Enum.EasingStyle.Quart), p)
    t:Play() 
    return t
end

local function makeDraggable(o)
    local drag, dInput, dStart, sPos
    track(o.InputBegan:Connect(function(i) 
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then 
            drag, dStart, sPos = true, i.Position, o.Position 
            track(i.Changed:Connect(function() 
                if i.UserInputState == Enum.UserInputState.End then drag = false end 
            end))
        end 
    end))
    track(o.InputChanged:Connect(function(i) 
        if i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch then 
            dInput = i 
        end 
    end))
    track(UserInputService.InputChanged:Connect(function(i) 
        if i == dInput and drag then 
            local d = i.Position - dStart 
            o.Position = UDim2.new(sPos.X.Scale, sPos.X.Offset + d.X, sPos.Y.Scale, sPos.Y.Offset + d.Y) 
        end 
    end))
end

function Lib:CreateWindow(title, author, iconId)
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "HB_PRO_V" .. math.random(100, 999)
    ScreenGui.ResetOnSpawn = false
    ScreenGui.Parent = CoreGui
    
    local guiVisible = true
    
    local ToggleBtn = Instance.new("ImageButton")
    ToggleBtn.Name = "ToggleButton"
    ToggleBtn.Size = UDim2.new(0, 60, 0, 60)
    ToggleBtn.Position = UDim2.new(0.02, 0, 0.5, -30)
    ToggleBtn.BackgroundColor3 = Lib.THEME.BG_SIDE
    ToggleBtn.BackgroundTransparency = 0.2
    ToggleBtn.Image = "rbxthumb://type=Asset&id=" .. (iconId or "91598067356551") .. "&w=420&h=420"
    ToggleBtn.ScaleType = Enum.ScaleType.Fit
    ToggleBtn.ZIndex = 15
    ToggleBtn.Parent = ScreenGui
    corner(ToggleBtn, 12)
    stroke(ToggleBtn, Lib.THEME.ACCENT, 2)
    makeDraggable(ToggleBtn)
    
    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Size = UDim2.new(0, 540, 0, 340)
    MainFrame.Position = UDim2.new(0.5, -270, 0.5, -170)
    MainFrame.BackgroundColor3 = Lib.THEME.BG_MAIN
    MainFrame.BackgroundTransparency = 0.1
    MainFrame.BorderSizePixel = 0
    MainFrame.ClipsDescendants = true
    MainFrame.Visible = true
    MainFrame.Parent = ScreenGui
    corner(MainFrame, 12)
    stroke(MainFrame, Lib.THEME.ACCENT, 2)
    makeDraggable(MainFrame)
    
    track(ToggleBtn.MouseButton1Click:Connect(function() 
        guiVisible = not guiVisible 
        MainFrame.Visible = guiVisible 
        tween(ToggleBtn, {BackgroundTransparency = guiVisible and 0.3 or 0.2}, 0.2) 
    end))
    
    local Sidebar = Instance.new("Frame")
    Sidebar.Name = "Sidebar"
    Sidebar.Size = UDim2.new(0, 110, 1, 0)
    Sidebar.BackgroundColor3 = Lib.THEME.BG_SIDE
    Sidebar.BackgroundTransparency = 0.2
    Sidebar.BorderSizePixel = 0
    Sidebar.Parent = MainFrame
    corner(Sidebar, 12)
    
    local SidebarLabel = Instance.new("TextLabel")
    SidebarLabel.Size = UDim2.new(1, 0, 0, 35)
    SidebarLabel.Position = UDim2.new(0, 0, 0, 5)
    SidebarLabel.BackgroundTransparency = 1
    SidebarLabel.Text = title or "Script"
    SidebarLabel.TextColor3 = Lib.THEME.ACCENT
    SidebarLabel.Font = Enum.Font.GothamBold
    SidebarLabel.TextSize = 22
    SidebarLabel.Parent = Sidebar
    
    local AuthorLabel = Instance.new("TextLabel")
    AuthorLabel.Size = UDim2.new(1, 0, 0, 20)
    AuthorLabel.Position = UDim2.new(0, 0, 0, 35)
    AuthorLabel.BackgroundTransparency = 1
    AuthorLabel.Text = "by " .. (author or "realayako")
    AuthorLabel.TextColor3 = Lib.THEME.TEXT_DIM
    AuthorLabel.Font = Enum.Font.GothamMedium
    AuthorLabel.TextSize = 10
    AuthorLabel.Parent = Sidebar
    
    local TabContainer = Instance.new("Frame")
    TabContainer.Size = UDim2.new(1, 0, 1, -70)
    TabContainer.Position = UDim2.new(0, 0, 0, 70)
    TabContainer.BackgroundTransparency = 1
    TabContainer.Parent = Sidebar
    local TabLayout = Instance.new("UIListLayout")
    TabLayout.Padding = UDim.new(0, 6)
    TabLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    TabLayout.Parent = TabContainer
    
    local ContentArea = Instance.new("Frame")
    ContentArea.Name = "ContentArea"
    ContentArea.Size = UDim2.new(1, -125, 1, -15)
    ContentArea.Position = UDim2.new(0, 120, 0, 10)
    ContentArea.BackgroundTransparency = 1
    ContentArea.ClipsDescendants = false
    ContentArea.Parent = MainFrame
    
    local pages = {}
    local tabBtns = {}
    
    local Window = {}
    
    local function addCommonMethods(Target, ParentScroll)
        local itemIndex = 0
        local function getIdx() itemIndex += 1 return itemIndex end

        function Target:AddSection(title)
            local f = Instance.new("Frame")
            f.Name = "Section_" .. title
            f.Size = UDim2.new(1, 0, 0, 26)
            f.BackgroundTransparency = 1
            f.LayoutOrder = getIdx()
            f.Parent = ParentScroll
            
            local label = Instance.new("TextLabel")
            label.Size = UDim2.new(1, 0, 1, -4)
            label.Position = UDim2.new(0, 4, 0, 0)
            label.BackgroundTransparency = 1
            label.Text = title:upper()
            label.TextColor3 = Lib.THEME.ACCENT
            label.Font = Enum.Font.GothamBold
            label.TextSize = 10
            label.TextXAlignment = Enum.TextXAlignment.Left
            label.Parent = f
            
            local line = Instance.new("Frame")
            line.Size = UDim2.new(1, 0, 0, 1)
            line.Position = UDim2.new(0, 0, 1, -2)
            line.BackgroundColor3 = Lib.THEME.ACCENT
            line.BackgroundTransparency = 0.5
            line.BorderSizePixel = 0
            line.Parent = f
            return f
        end
        
        function Target:AddLabel(text, size)
            local l = Instance.new("TextLabel")
            l.Size = UDim2.new(1, 0, 0, size or 20)
            l.BackgroundTransparency = 1
            l.Text = text
            l.TextColor3 = Lib.THEME.TEXT_DIM
            l.Font = Enum.Font.Gotham
            l.TextSize = 12
            l.TextXAlignment = Enum.TextXAlignment.Left
            l.TextWrapped = true
            l.LayoutOrder = getIdx()
            l.Parent = ParentScroll
            return l
        end

        function Target:AddToggle(t, d, c)
            local f = Instance.new("Frame")
            f.Size = UDim2.new(1, 0, 0, 30)
            f.BackgroundTransparency = 1
            f.LayoutOrder = getIdx()
            f.Parent = ParentScroll
            
            local l = Instance.new("TextLabel")
            l.Size = UDim2.new(1, -50, 1, 0)
            l.Position = UDim2.new(0, 10, 0, 0)
            l.BackgroundTransparency = 1
            l.Text = t
            l.TextColor3 = Lib.THEME.TEXT
            l.Font = Enum.Font.Gotham
            l.TextSize = 11
            l.TextXAlignment = Enum.TextXAlignment.Left
            l.Parent = f
            
            local btn = Instance.new("TextButton")
            btn.Size = UDim2.new(0, 30, 0, 16)
            btn.Position = UDim2.new(1, -40, 0.5, -8)
            btn.BackgroundColor3 = d and Lib.THEME.ACCENT or Color3.fromRGB(60, 60, 70)
            btn.Text = ""
            btn.Parent = f
            corner(btn, 8)
            
            local o = Instance.new("Frame")
            o.Size = UDim2.new(0, 12, 0, 12)
            o.Position = UDim2.new(d and 1 or 0, d and -14 or 2, 0.5, -6)
            o.BackgroundColor3 = Color3.new(1, 1, 1)
            o.Parent = btn
            corner(o, 6)
            
            local state = d
            local function update()
                tween(btn, {BackgroundColor3 = state and Lib.THEME.ACCENT or Color3.fromRGB(60, 60, 70)}, 0.2) 
                tween(o, {Position = UDim2.new(state and 1 or 0, state and -14 or 2, 0.5, -6)}, 0.2) 
            end

            track(btn.MouseButton1Click:Connect(function() 
                state = not state 
                update()
                c(state) 
            end))
            
            local ToggleObj = {Instance = f}
            function ToggleObj:Set(v)
                state = v 
                update()
            end
            return ToggleObj
        end
        
        function Target:AddSlider(t, min, max, d, dec, c)
            local f = Instance.new("Frame")
            f.Size = UDim2.new(1, 0, 0, 42)
            f.BackgroundTransparency = 1
            f.LayoutOrder = getIdx()
            f.Parent = ParentScroll
            
            local l = Instance.new("TextLabel")
            l.Size = UDim2.new(1, -20, 0, 20)
            l.Position = UDim2.new(0, 10, 0, 4)
            l.BackgroundTransparency = 1
            l.Text = t .. ": " .. d
            l.TextColor3 = Lib.THEME.TEXT
            l.Font = Enum.Font.Gotham
            l.TextSize = 11
            l.TextXAlignment = Enum.TextXAlignment.Left
            l.Parent = f
            
            local bg = Instance.new("Frame")
            bg.Size = UDim2.new(1, -20, 0, 3)
            bg.Position = UDim2.new(0, 10, 0, 30)
            bg.BackgroundColor3 = Color3.fromRGB(45, 45, 50)
            bg.Parent = f
            corner(bg, 2)
            
            local fill = Instance.new("Frame")
            fill.Size = UDim2.new((d - min) / (max - min), 0, 1, 0)
            fill.BackgroundColor3 = Lib.THEME.ACCENT
            fill.Parent = bg
            corner(fill, 2)
            
            local b = Instance.new("Frame")
            b.Size = UDim2.new(0, 10, 0, 10)
            b.Position = UDim2.new((d - min) / (max - min), -5, 0.5, -5)
            b.BackgroundColor3 = Lib.THEME.ACCENT
            b.Parent = bg
            corner(b, 5)
            
            local tr = Instance.new("TextButton")
            tr.Size = UDim2.new(1, 0, 1, 0)
            tr.BackgroundTransparency = 1
            tr.Text = ""
            tr.Parent = f
            
            local prec, drag = 10 ^ (dec or 0), false
            local function fV(v) 
                return (dec or 0) <= 0 and tostring(math.round(v)) or tostring(math.floor(v * prec + 0.5) / prec) 
            end
            
            local function up(pos) 
                local rel = math.clamp((pos.X - bg.AbsolutePosition.X) / bg.AbsoluteSize.X, 0, 1) 
                local v = (min + (max - min) * rel)
                v = math.floor(v * prec + 0.5) / prec 
                l.Text, fill.Size, b.Position = t .. ": " .. fV(v), UDim2.new(rel, 0, 1, 0), UDim2.new(rel, -5, 0.5, -5) 
                c(v) 
            end
            
            track(tr.InputBegan:Connect(function(i) 
                if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then 
                    drag = true 
                    up(i.Position) 
                end 
            end))
            track(UserInputService.InputEnded:Connect(function(i) 
                if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then 
                    drag = false 
                end 
            end))
            track(UserInputService.InputChanged:Connect(function(i) 
                if drag and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then 
                    up(i.Position) 
                end 
            end))
            
            local SliderObj = {Instance = f}
            function SliderObj:Set(v)
                local rel = math.clamp((v - min) / (max - min), 0, 1) 
                l.Text, fill.Size, b.Position = t .. ": " .. fV(v), UDim2.new(rel, 0, 1, 0), UDim2.new(rel, -5, 0.5, -5) 
            end
            return SliderObj
        end
        
        function Target:AddInput(t, d, ph, c)
            local f = Instance.new("Frame")
            f.Size = UDim2.new(1, 0, 0, 55)
            f.BackgroundTransparency = 1
            f.LayoutOrder = getIdx()
            f.Parent = ParentScroll

            local label = Instance.new("TextLabel")
            label.Size = UDim2.new(1, -20, 0, 25)
            label.Position = UDim2.new(0, 12, 0, 5)
            label.BackgroundTransparency = 1
            label.Text = t
            label.TextColor3 = Lib.THEME.TEXT
            label.Font = Enum.Font.Gotham
            label.TextSize = 13
            label.TextXAlignment = Enum.TextXAlignment.Left
            label.Parent = f
            
            local i = Instance.new("TextBox")
            i.Size = UDim2.new(1, -24, 0, 24)
            i.Position = UDim2.new(0, 12, 0, 26)
            i.BackgroundColor3 = Color3.fromRGB(40, 40, 45)
            i.Text = d
            i.PlaceholderText = ph
            i.TextColor3 = Lib.THEME.TEXT
            i.Font = Enum.Font.Gotham
            i.TextSize = 12
            i.ClearTextOnFocus = false
            i.Parent = f
            corner(i, 4)
            stroke(i, Lib.THEME.ACCENT)
            track(i.FocusLost:Connect(function() c(i.Text) end))
            
            local InputObj = {Instance = f, TextBox = i}
            return InputObj
        end
        
        function Target:AddButton(t, cl, c)
            local b = Instance.new("TextButton")
            b.Size = UDim2.new(1, 0, 0, 30)
            b.BackgroundColor3 = cl or Lib.THEME.ACCENT
            b.BackgroundTransparency = 0.8
            b.Text = t
            b.TextColor3 = Lib.THEME.TEXT
            b.Font = Enum.Font.GothamBold
            b.TextSize = 11
            b.LayoutOrder = getIdx()
            b.Parent = ParentScroll
            corner(b, 6)
            stroke(b, Lib.THEME.ACCENT)

            track(b.MouseButton1Click:Connect(function() 
                tween(b, {BackgroundTransparency = 0.5}, 0.1) 
                task.wait(0.1) 
                tween(b, {BackgroundTransparency = 0.8}, 0.1) 
                c() 
            end))
            return b
        end
        
        function Target:AddDropdown(t, items, d, c)
            local f = Instance.new("Frame")
            f.Size = UDim2.new(1, 0, 0, 32)
            f.BackgroundTransparency = 1
            f.ClipsDescendants = true
            f.LayoutOrder = getIdx()
            f.Parent = ParentScroll
            
            local l = Instance.new("TextButton")
            l.Size = UDim2.new(1, 0, 0, 32)
            l.BackgroundTransparency = 1
            l.Text = "  " .. t .. ": " .. d
            l.TextColor3 = Lib.THEME.TEXT
            l.Font = Enum.Font.GothamMedium
            l.TextSize = 11
            l.TextXAlignment = Enum.TextXAlignment.Left
            l.Parent = f
            
            local scroll = Instance.new("ScrollingFrame")
            scroll.Size = UDim2.new(1, 0, 0, 0)
            scroll.Position = UDim2.new(0, 0, 0, 32)
            scroll.BackgroundTransparency = 1
            scroll.BorderSizePixel = 0
            scroll.ScrollBarThickness = 2
            scroll.ScrollBarImageColor3 = Lib.THEME.ACCENT
            scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
            scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
            scroll.Visible = false
            scroll.Parent = f
            
            local layout = Instance.new("UIListLayout")
            layout.SortOrder = Enum.SortOrder.LayoutOrder
            layout.Parent = scroll
            
            for ide, v in ipairs(items) do
                local ib = Instance.new("TextButton")
                ib.Size = UDim2.new(1, 0, 0, 26)
                ib.BackgroundColor3 = Lib.THEME.ACCENT
                ib.BackgroundTransparency = 1
                ib.BorderSizePixel = 0
                ib.Text = "    " .. tostring(v)
                ib.TextColor3 = Lib.THEME.TEXT_DIM
                ib.Font = Enum.Font.Gotham
                ib.TextSize = 10
                ib.LayoutOrder = ide
                ib.TextXAlignment = Enum.TextXAlignment.Left
                ib.Parent = scroll

                track(ib.MouseEnter:Connect(function()
                    tween(ib, {BackgroundTransparency = 0.8, TextColor3 = Lib.THEME.TEXT}, 0.2)
                end))
                track(ib.MouseLeave:Connect(function()
                    tween(ib, {BackgroundTransparency = 1, TextColor3 = Lib.THEME.TEXT_DIM}, 0.2)
                end))

                track(ib.MouseButton1Click:Connect(function() 
                    l.Text = "  " .. t .. ": " .. tostring(v) 
                    tween(f, {Size = UDim2.new(1, 0, 0, 32)}, 0.2)
                    tween(scroll, {Size = UDim2.new(1, 0, 0, 0)}, 0.2)
                    task.delay(0.2, function() scroll.Visible = false end)
                    c(v) 
                end))
            end
            
            local op = false
            track(l.MouseButton1Click:Connect(function() 
                op = not op 
                if op then scroll.Visible = true end
                local targetH = math.min(#items * 26, 130)
                tween(f, {Size = UDim2.new(1, 0, 0, op and (32 + targetH + 4) or 32)}, 0.2)
                tween(scroll, {Size = UDim2.new(1, 0, 0, op and targetH or 0)}, 0.2)
                if not op then task.delay(0.2, function() scroll.Visible = false end) end
            end))
            
            local DropObj = {Instance = f}
            function DropObj:Set(v) l.Text = "  " .. t .. ": " .. tostring(v) end
            return DropObj
        end
        
        function Target:AddCollapsible(ti)
            local c = Instance.new("Frame")
            c.Size = UDim2.new(1, 0, 0, 30)
            c.BackgroundColor3 = Lib.THEME.BG_CONTENT
            c.BackgroundTransparency = 0.4
            c.ClipsDescendants = true
            c.BorderSizePixel = 0
            c.LayoutOrder = getIdx()
            c.Parent = ParentScroll
            corner(c, 6)
            stroke(c, Lib.THEME.ACCENT)
            
            local btn = Instance.new("TextButton")
            btn.Size = UDim2.new(1, 0, 0, 30)
            btn.BackgroundTransparency = 1
            btn.Text = "  " .. ti .. " ▼"
            btn.TextColor3 = Lib.THEME.TEXT
            btn.Font = Enum.Font.GothamBold
            btn.TextSize = 10
            btn.TextXAlignment = Enum.TextXAlignment.Left
            btn.Parent = c
            
            local con = Instance.new("Frame")
            con.Position = UDim2.new(0, 0, 0, 30)
            con.Size = UDim2.new(1, 0, 0, 0)
            con.BackgroundTransparency = 1
            con.Parent = c
            local ly = Instance.new("UIListLayout")
            ly.Padding = UDim.new(0, 4)
            ly.SortOrder = Enum.SortOrder.LayoutOrder
            ly.Parent = con
            
            local op = false
            local function updateH()
                local h = op and (30 + ly.AbsoluteContentSize.Y + 8) or 30 
                tween(c, {Size = UDim2.new(1, 0, 0, h)}, 0.3) 
                btn.Text, btn.TextColor3 = "  " .. ti .. (op and " ▲" or " ▼"), op and Lib.THEME.ACCENT or Lib.THEME.TEXT 
            end

            track(btn.MouseButton1Click:Connect(function() 
                op = not op 
                updateH()
            end))
            
            track(ly:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function() 
                if op then updateH() end 
            end))
            
            local CollapsibleObj = {Instance = c, Container = con}
            addCommonMethods(CollapsibleObj, con)
            return CollapsibleObj
        end
    end

    function Window:CreateTab(name)
        local b = Instance.new("TextButton")
        b.Size = UDim2.new(0.85, 0, 0, 36)
        b.BackgroundColor3 = Lib.THEME.DARK_ITEM
        b.BackgroundTransparency = 1
        b.Text = name
        b.TextColor3 = Lib.THEME.TEXT_DIM
        b.Font = Enum.Font.GothamBold
        b.TextSize = 12
        b.Parent = TabContainer
        corner(b, 8)
        
        local p = Instance.new("ScrollingFrame")
        p.Size = UDim2.new(1, 0, 1, 0)
        p.BackgroundTransparency = 1
        p.Visible = false
        p.ScrollBarThickness = 2
        p.ScrollBarImageColor3 = Lib.THEME.ACCENT
        p.AutomaticCanvasSize = Enum.AutomaticSize.Y
        p.CanvasSize = UDim2.new(0, 0, 0, 0)
        p.Parent = ContentArea
        
        local l = Instance.new("UIListLayout")
        l.Padding = UDim.new(0, 8)
        l.SortOrder = Enum.SortOrder.LayoutOrder
        l.Parent = p
        
        track(b.MouseButton1Click:Connect(function() 
            for _, x in ipairs(pages) do x.Visible = false end 
            for _, x in ipairs(tabBtns) do tween(x, {BackgroundTransparency = 1, TextColor3 = Lib.THEME.TEXT_DIM}, 0.2) end 
            p.Visible = true
            b.BackgroundTransparency = 0
            b.TextColor3 = Lib.THEME.ACCENT 
        end))
        
        table.insert(pages, p)
        table.insert(tabBtns, b)
        
        local Tab = {ScrollFrame = p, Instance = p}
        addCommonMethods(Tab, p)
        return Tab
    end
    
    function Window:SelectTab(num)
        if #tabBtns >= num then
            for _, x in ipairs(pages) do x.Visible = false end
            for _, x in ipairs(tabBtns) do x.BackgroundTransparency = 1; x.TextColor3 = Lib.THEME.TEXT_DIM end
            pages[num].Visible = true
            tabBtns[num].BackgroundTransparency = 0
            tabBtns[num].TextColor3 = Lib.THEME.ACCENT
        end
    end
    
    function Window:Destroy()
        ScreenGui:Destroy()
        for _, conn in ipairs(Lib.connections) do
            if conn.Connected then
                conn:Disconnect()
            end
        end
        Lib.connections = {}
    end

    return Window
end

return Lib
