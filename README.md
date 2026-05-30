--[[
    ONYX HUB - DELTA EDITION v3 (COMPLETO)
    Compatível: Delta, Xeno, Synapse, Krnl
    Corrigido para funcionear em todos os executores
]]

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local CoreGui_Service = game:GetService("CoreGui")
local Debris = game:GetService("Debris")
local HttpService = game:GetService("HttpService")
local TeleportService = game:GetService("TeleportService")
local Workspace = game:GetService("Workspace")
local Camera = Workspace.CurrentCamera

local LP = Players.LocalPlayer

-- ==================== DETECÇÃO DE GUI PARENT ====================
local function getGuiParent()
    local parent = nil
    
    -- Tentar CoreGui
    pcall(function()
        local test = Instance.new("ScreenGui")
        test.Parent = CoreGui_Service
        test:Destroy()
        parent = CoreGui_Service
    end)
    
    if not parent then
        pcall(function()
            parent = LP:WaitForChild("PlayerGui", 1)
        end)
    end
    
    if not parent then
        parent = LP:FindFirstChildOfClass("PlayerGui") or workspace
    end
    
    return parent
end

local GuiParent = getGuiParent()

-- ==================== THEME ====================
local THEME = {
    Background = Color3.fromRGB(18, 10, 18),
    Surface    = Color3.fromRGB(28, 16, 28),
    SurfaceAlt = Color3.fromRGB(38, 22, 38),
    Sidebar    = Color3.fromRGB(22, 12, 22),
    Stroke     = Color3.fromRGB(80, 30, 70),
    Text       = Color3.fromRGB(255, 255, 255),
    SubText    = Color3.fromRGB(220, 190, 210),
    Accent     = Color3.fromRGB(255, 20, 147),
    AccentSoft = Color3.fromRGB(200, 15, 115),
    Success    = Color3.fromRGB(90, 220, 150),
    Danger     = Color3.fromRGB(255, 95, 95),
}

local CORNER    = UDim.new(0, 12)
local CORNER_SM = UDim.new(0, 8)
local TWEEN     = TweenInfo.new(0.25, Enum.EasingStyle.Quart, Enum.EasingDirection.Out)
local TWEEN_FAST= TweenInfo.new(0.14, Enum.EasingStyle.Quad,  Enum.EasingDirection.Out)

local IS_MOBILE = UserInputService.TouchEnabled and not UserInputService.MouseEnabled

-- ==================== DRAWING DETECTION ====================
local hasDrawing = false
pcall(function()
    if Drawing and typeof(Drawing) == "table" and Drawing.new then
        local test = Drawing.new("Circle")
        test:Remove()
        hasDrawing = true
    end
end)

if not hasDrawing then
    warn("[ONYX] Drawing não suportado")
end

-- ==================== HELPERS ====================
local function new(class, props, children)
    local i = Instance.new(class)
    for k, v in pairs(props or {}) do 
        pcall(function() i[k] = v end)
    end
    for _, c in ipairs(children or {}) do 
        pcall(function() c.Parent = i end)
    end
    return i
end

local function corner(p, r) 
    return new("UICorner", {CornerRadius = r or CORNER, Parent = p}) 
end

local function stroke(p, c, t, tr)
    return new("UIStroke", {
        Color = c or THEME.Stroke, Thickness = t or 1,
        Transparency = tr or 0,
        ApplyStrokeMode = Enum.ApplyStrokeMode.Border, Parent = p,
    })
end

local function padding(p, v)
    return new("UIPadding", {
        PaddingTop = UDim.new(0, v), PaddingBottom = UDim.new(0, v),
        PaddingLeft = UDim.new(0, v), PaddingRight = UDim.new(0, v), Parent = p,
    })
end

local function tween(o, p, i) 
    local ok = pcall(function()
        TweenService:Create(o, i or TWEEN, p):Play()
    end)
end

local function SafeDrawing(t)
    if not hasDrawing then return nil end
    local ok, o = pcall(function() return Drawing.new(t) end)
    return ok and o or nil
end

-- ==================== ONYX CLASS ====================
local Onyx = {}
Onyx.__index = Onyx

function Onyx:Init()
    -- Destruir GUI antiga
    pcall(function()
        local old = GuiParent:FindFirstChild("OnyxHub")
        if old then old:Destroy() end
    end)

    local gui = new("ScreenGui", {
        Name = "OnyxHub", 
        IgnoreGuiInset = true,
        ResetOnSpawn = false, 
        ZIndexBehavior = Enum.ZIndexBehavior.Sibling,
    })
    
    gui.Parent = GuiParent
    self.Gui = gui
    self.NotificationsEnabled = true

    local size = IS_MOBILE and UDim2.new(0.95, 0, 0.82, 0) or UDim2.fromOffset(780, 500)

    local main = new("Frame", {
        Name = "Main",
        AnchorPoint = Vector2.new(0.5, 0.5),
        Position = UDim2.fromScale(0.5, 0.5),
        Size = size,
        BackgroundColor3 = THEME.Background,
        BackgroundTransparency = 0.04,
        Parent = gui,
    })
    corner(main, CORNER)
    stroke(main, THEME.Accent, 1, 0.6)

    self.Main = main
    self._BaseSize = size

    self:BuildSidebar()
    self:BuildContent()
    self:BuildTopBar()
    self:BuildOpenButton()
    self:BuildNotifications()

    main.Size = UDim2.fromScale(0, 0)
    tween(main, {Size = size}, TweenInfo.new(0.4, Enum.EasingStyle.Quint, Enum.EasingDirection.Out))

    self:_StartStats()
    return self
end

-- ==================== SIDEBAR ====================
function Onyx:BuildSidebar()
    local side = new("Frame", {
        Name = "Sidebar",
        Size = UDim2.new(0, 170, 1, 0),
        BackgroundColor3 = THEME.Sidebar,
        Parent = self.Main,
    })
    corner(side, CORNER)
    new("Frame", {
        Size = UDim2.new(0, 14, 1, 0), Position = UDim2.new(1, -14, 0, 0),
        BackgroundColor3 = THEME.Sidebar, BorderSizePixel = 0, Parent = side,
    })

    local logo = new("Frame", {Size = UDim2.new(1, 0, 0, 50), BackgroundTransparency = 1, Parent = side})
    new("UIListLayout", {
        FillDirection = Enum.FillDirection.Horizontal,
        HorizontalAlignment = Enum.HorizontalAlignment.Center,
        VerticalAlignment = Enum.VerticalAlignment.Center,
        Padding = UDim.new(0, 4), Parent = logo,
    })
    new("TextLabel", {
        Size = UDim2.fromOffset(40, 50), BackgroundTransparency = 1,
        Font = Enum.Font.GothamBold, TextSize = 17, TextColor3 = THEME.Text,
        Text = "CLS", Parent = logo,
    })
    new("TextLabel", {
        Size = UDim2.fromOffset(35, 50), BackgroundTransparency = 1,
        Font = Enum.Font.GothamBold, TextSize = 17, TextColor3 = THEME.Accent,
        Text = "HUB", Parent = logo,
    })

    local tabs = new("ScrollingFrame", {
        Name = "Tabs",
        Position = UDim2.new(0, 8, 0, 58),
        Size = UDim2.new(1, -16, 1, -58 - 78),
        BackgroundTransparency = 1, BorderSizePixel = 0,
        ScrollBarThickness = 0,
        CanvasSize = UDim2.new(), AutomaticCanvasSize = Enum.AutomaticSize.Y,
        Parent = side,
    })
    new("UIListLayout", {Padding = UDim.new(0, 6), SortOrder = Enum.SortOrder.LayoutOrder, Parent = tabs})

    self.Sidebar = side
    self.TabsHolder = tabs
end

-- ==================== TOP BAR ====================
function Onyx:BuildTopBar()
    local top = new("Frame", {
        Name = "TopBar",
        Position = UDim2.new(0, 170, 0, 0),
        Size = UDim2.new(1, -170, 0, 46),
        BackgroundTransparency = 1, Parent = self.Main,
    })
    self.TopBar = top

    do
        local dragging, dragStart, startPos
        local main = self.Main
        top.Active = true
        top.InputBegan:Connect(function(i)
            if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
                dragging = true
                dragStart = i.Position
                startPos = main.Position
            end
        end)
        top.InputEnded:Connect(function(i)
            if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
                dragging = false
            end
        end)
        UserInputService.InputChanged:Connect(function(i)
            if dragging and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
                local d = i.Position - dragStart
                main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + d.X,
                                          startPos.Y.Scale, startPos.Y.Offset + d.Y)
            end
        end)
    end

    local function ctrl(text, x, color)
        local b = new("TextButton", {
            AnchorPoint = Vector2.new(1, 0.5),
            Position = UDim2.new(1, x, 0.5, 0),
            Size = UDim2.fromOffset(30, 30),
            BackgroundColor3 = THEME.SurfaceAlt,
            Text = text, Font = Enum.Font.GothamBold,
            TextSize = 16, TextColor3 = color, AutoButtonColor = false,
            Parent = top,
        })
        corner(b, CORNER_SM)
        stroke(b, THEME.Stroke, 1, 0.4)
        return b
    end

    local close = ctrl("X", -12, THEME.Danger)
    close.MouseButton1Click:Connect(function() self:Minimize() end)
end

-- ==================== OPEN BUTTON ====================
function Onyx:BuildOpenButton()
    local btn = new("ImageButton", {
        Name = "OpenButton",
        AnchorPoint = Vector2.new(0, 0.5),
        Position = UDim2.new(0, 14, 0.5, 0),
        Size = UDim2.fromOffset(46, 46),
        BackgroundColor3 = THEME.Background,
        Image = "", AutoButtonColor = false, Visible = false,
        Parent = self.Gui,
    })
    corner(btn, UDim.new(1, 0))
    stroke(btn, THEME.Accent, 2, 0.2)

    new("TextLabel", {
        Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1,
        Font = Enum.Font.GothamBold, TextSize = 18, TextColor3 = THEME.Accent,
        Text = "+", Parent = btn,
    })

    btn.MouseButton1Click:Connect(function() self:Maximize() end)
    self.OpenBtn = btn
end

function Onyx:Minimize()
    tween(self.Main, {Size = UDim2.fromScale(0, 0)},
        TweenInfo.new(0.25, Enum.EasingStyle.Quart, Enum.EasingDirection.In))
    task.wait(0.26)
    self.Main.Visible = false
    self.OpenBtn.Visible = true
    self.OpenBtn.Size = UDim2.fromOffset(0, 0)
    tween(self.OpenBtn, {Size = UDim2.fromOffset(46, 46)},
        TweenInfo.new(0.3, Enum.EasingStyle.Back, Enum.EasingDirection.Out))
    self:Notify("Menu", "Minimizado - Clique no ícone azul")
end

function Onyx:Maximize()
    self.OpenBtn.Visible = false
    self.Main.Visible = true
    self.Main.Size = UDim2.fromScale(0, 0)
    tween(self.Main, {Size = self._BaseSize},
        TweenInfo.new(0.35, Enum.EasingStyle.Quint, Enum.EasingDirection.Out))
end

-- ==================== CONTENT ====================
function Onyx:BuildContent()
    local content = new("Frame", {
        Name = "Content",
        Position = UDim2.new(0, 170, 0, 46),
        Size = UDim2.new(1, -170, 1, -46),
        BackgroundTransparency = 1, Parent = self.Main,
    })
    padding(content, 12)
    self.Content = content
    self.Tabs = {}
end

-- ==================== NOTIFICATIONS ====================
function Onyx:BuildNotifications()
    local holder = new("Frame", {
        Name = "Notifications",
        AnchorPoint = Vector2.new(0.5, 0),
        Position = UDim2.new(0.5, 0, 0, 16),
        Size = UDim2.new(0, 320, 1, -32),
        BackgroundTransparency = 1, Parent = self.Gui,
    })
    new("UIListLayout", {
        Padding = UDim.new(0, 8),
        VerticalAlignment = Enum.VerticalAlignment.Top,
        HorizontalAlignment = Enum.HorizontalAlignment.Center,
        SortOrder = Enum.SortOrder.LayoutOrder, Parent = holder,
    })
    self.NotifHolder = holder
end

function Onyx:Notify(title, text, duration)
    if not self.NotificationsEnabled then return end
    duration = duration or 3
    
    local n = new("Frame", {
        Size = UDim2.new(1, 0, 0, 56),
        BackgroundColor3 = THEME.Surface,
        Parent = self.NotifHolder,
    })
    corner(n, CORNER_SM)
    stroke(n, THEME.Accent, 1, 0.5)

    new("Frame", {
        Size = UDim2.new(0, 3, 1, -10),
        Position = UDim2.new(0, 6, 0, 5),
        BackgroundColor3 = THEME.Accent, BorderSizePixel = 0, Parent = n,
    })

    new("TextLabel", {
        Position = UDim2.new(0, 16, 0, 6), Size = UDim2.new(1, -22, 0, 18),
        BackgroundTransparency = 1, Font = Enum.Font.GothamBold,
        TextSize = 12, TextColor3 = THEME.Text,
        Text = title, TextXAlignment = Enum.TextXAlignment.Left, Parent = n,
    })
    new("TextLabel", {
        Position = UDim2.new(0, 16, 0, 26), Size = UDim2.new(1, -22, 0, 22),
        BackgroundTransparency = 1, Font = Enum.Font.Gotham,
        TextSize = 11, TextColor3 = THEME.SubText, TextWrapped = true,
        Text = text, TextXAlignment = Enum.TextXAlignment.Left,
        TextYAlignment = Enum.TextYAlignment.Top, Parent = n,
    })

    n.Position = UDim2.new(0, 0, 0, -80)
    tween(n, {Position = UDim2.new(0, 0, 0, 0)})

    task.delay(duration, function()
        if n.Parent then
            tween(n, {BackgroundTransparency = 1}, TWEEN_FAST)
            for _, c in ipairs(n:GetDescendants()) do
                if c:IsA("TextLabel") then tween(c, {TextTransparency = 1}, TWEEN_FAST)
                elseif c:IsA("Frame") then tween(c, {BackgroundTransparency = 1}, TWEEN_FAST)
                elseif c:IsA("UIStroke") then tween(c, {Transparency = 1}, TWEEN_FAST) end
            end
            task.wait(0.2)
            pcall(function() n:Destroy() end)
        end
    end)
end

-- ==================== TABS ====================
function Onyx:AddTab(name, icon)
    local tab = {Name = name}
    tab.Button = new("TextButton", {
        Size = UDim2.new(1, 0, 0, 40),
        BackgroundColor3 = THEME.SurfaceAlt, BackgroundTransparency = 1,
        Text = "", AutoButtonColor = false, Parent = self.TabsHolder,
    })
    corner(tab.Button, CORNER_SM)

    tab.Indicator = new("Frame", {
        Size = UDim2.new(0, 3, 0.6, 0),
        Position = UDim2.new(0, 0, 0.2, 0),
        BackgroundColor3 = THEME.Accent, BackgroundTransparency = 1,
        BorderSizePixel = 0, Parent = tab.Button,
    })
    corner(tab.Indicator, UDim.new(1, 0))

    new("TextLabel", {
        Position = UDim2.new(0, 14, 0, 0), Size = UDim2.new(1, -18, 1, 0),
        BackgroundTransparency = 1, Font = Enum.Font.Gotham,
        TextSize = 14, TextColor3 = THEME.SubText,
        Text = (icon and (icon .. "  ") or "") .. name,
        TextXAlignment = Enum.TextXAlignment.Left, Name = "Label", Parent = tab.Button,
    })

    tab.Page = new("ScrollingFrame", {
        Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1,
        BorderSizePixel = 0, ScrollBarThickness = 2,
        ScrollBarImageColor3 = THEME.Accent,
        CanvasSize = UDim2.new(), AutomaticCanvasSize = Enum.AutomaticSize.Y,
        Visible = false, Parent = self.Content,
    })
    new("UIListLayout", {Padding = UDim.new(0, 10), Parent = tab.Page})

    tab.Button.MouseEnter:Connect(function()
        if self.Active ~= tab then tween(tab.Button, {BackgroundTransparency = 0.85}, TWEEN_FAST) end
    end)
    tab.Button.MouseLeave:Connect(function()
        if self.Active ~= tab then tween(tab.Button, {BackgroundTransparency = 1}, TWEEN_FAST) end
    end)
    tab.Button.MouseButton1Click:Connect(function() self:SelectTab(tab) end)

    table.insert(self.Tabs, tab)
    if not self.Active then self:SelectTab(tab) end

    function tab:AddCard(title) return Onyx._BuildCard(Onyx, self, title) end
    return tab
end

function Onyx:SelectTab(tab)
    for _, t in ipairs(self.Tabs) do
        t.Page.Visible = false
        tween(t.Button, {BackgroundTransparency = 1}, TWEEN_FAST)
        tween(t.Indicator, {BackgroundTransparency = 1}, TWEEN_FAST)
        tween(t.Button.Label, {TextColor3 = THEME.SubText}, TWEEN_FAST)
    end
    tab.Page.Visible = true
    tween(tab.Button, {BackgroundTransparency = 0.78}, TWEEN_FAST)
    tween(tab.Indicator, {BackgroundTransparency = 0}, TWEEN_FAST)
    tween(tab.Button.Label, {TextColor3 = THEME.Text}, TWEEN_FAST)
    self.Active = tab
end

-- ==================== CARDS ====================
function Onyx._BuildCard(hub, tab, title)
    local card = new("Frame", {
        Size = UDim2.new(1, -4, 0, 40),
        AutomaticSize = Enum.AutomaticSize.Y,
        BackgroundColor3 = THEME.Surface,
        Parent = tab.Page,
    })
    corner(card, CORNER)
    stroke(card, THEME.Stroke, 1, 0.3)
    padding(card, 12)
    new("UIListLayout", {Padding = UDim.new(0, 8), SortOrder = Enum.SortOrder.LayoutOrder, Parent = card})

    if title and title ~= "" then
        new("TextLabel", {
            Name = "CardTitle", LayoutOrder = 0,
            Size = UDim2.new(1, 0, 0, 20), BackgroundTransparency = 1,
            Font = Enum.Font.GothamBold, TextSize = 13, TextColor3 = THEME.Text,
            Text = title, TextXAlignment = Enum.TextXAlignment.Left, Parent = card,
        })
    end

    local api = {Frame = card}

    function api:AddInfo(label, value)
        local r = new("Frame", {
            Size = UDim2.new(1, 0, 0, 24),
            BackgroundColor3 = THEME.SurfaceAlt, Parent = card,
        })
        corner(r, CORNER_SM)
        padding(r, 8)

        new("TextLabel", {
            Size = UDim2.new(0.5, 0, 1, 0), BackgroundTransparency = 1,
            Font = Enum.Font.Gotham, TextSize = 11, TextColor3 = THEME.SubText,
            Text = label, TextXAlignment = Enum.TextXAlignment.Left, Parent = r,
        })
        local v = new("TextLabel", {
            Position = UDim2.new(0.5, 0, 0, 0), Size = UDim2.new(0.5, 0, 1, 0),
            BackgroundTransparency = 1, Font = Enum.Font.GothamBold,
            TextSize = 11, TextColor3 = THEME.Text, Text = tostring(value),
            TextXAlignment = Enum.TextXAlignment.Right, Parent = r,
        })
        return v
    end

    function api:AddToggle(text, default, cb)
        local r = new("Frame", {
            Size = UDim2.new(1, 0, 0, 30),
            BackgroundColor3 = THEME.SurfaceAlt, Parent = card,
        })
        corner(r, CORNER_SM)
        padding(r, 8)

        new("TextLabel", {
            Size = UDim2.new(1, -56, 1, 0), BackgroundTransparency = 1,
            Font = Enum.Font.Gotham, TextSize = 12, TextColor3 = THEME.Text,
            Text = text, TextXAlignment = Enum.TextXAlignment.Left, Parent = r,
        })

        local sw = new("TextButton", {
            AnchorPoint = Vector2.new(1, 0.5),
            Position = UDim2.new(1, 0, 0.5, 0),
            Size = UDim2.fromOffset(38, 20),
            BackgroundColor3 = THEME.Stroke,
            Text = "", AutoButtonColor = false, Parent = r,
        })
        corner(sw, UDim.new(1, 0))

        local knob = new("Frame", {
            Size = UDim2.fromOffset(16, 16), Position = UDim2.fromOffset(2, 2),
            BackgroundColor3 = THEME.Text, BorderSizePixel = 0, Parent = sw,
        })
        corner(knob, UDim.new(1, 0))

        local state = default and true or false
        local function refresh(silent)
            if state then
                tween(sw, {BackgroundColor3 = THEME.Accent})
                tween(knob, {Position = UDim2.fromOffset(20, 2)})
            else
                tween(sw, {BackgroundColor3 = THEME.Stroke})
                tween(knob, {Position = UDim2.fromOffset(2, 2)})
            end
            if cb then pcall(cb, state) end
            if not silent then
                hub:Notify(text, state and "Ativado ✓" or "Desativado ✕", 2.5)
            end
        end

        sw.MouseButton1Click:Connect(function() state = not state; refresh() end)
        if state then refresh(true) end
        return {Set = function(_, v) state = v; refresh() end}
    end

    function api:AddButton(text, cb)
        local b = new("TextButton", {
            Size = UDim2.new(1, 0, 0, 30),
            BackgroundColor3 = THEME.Accent, BackgroundTransparency = 0.1,
            AutoButtonColor = false,
            Font = Enum.Font.GothamBold, TextSize = 12, TextColor3 = THEME.Text,
            Text = text, Parent = card,
        })
        corner(b, CORNER_SM)
        b.MouseEnter:Connect(function() tween(b, {BackgroundTransparency = 0}) end)
        b.MouseLeave:Connect(function() tween(b, {BackgroundTransparency = 0.1}) end)
        b.MouseButton1Click:Connect(function()
            if cb then pcall(cb) end
            hub:Notify(text, "Executado ✓", 2)
        end)
        return b
    end

    function api:AddSlider(text, min, max, default, cb)
        min, max = min or 0, max or 100
        local value = default or min
        local OUTER_H = 44
        local BAR_H   = 5
        local KNOB    = 14
        local outer = new("Frame", {
            Size = UDim2.new(1, 0, 0, OUTER_H),
            BackgroundColor3 = THEME.SurfaceAlt, Parent = card,
        })
        corner(outer, CORNER_SM)
        padding(outer, 8)

        new("TextLabel", {
            Size = UDim2.new(1, -60, 0, 16), BackgroundTransparency = 1,
            Font = Enum.Font.Gotham, TextSize = 12, TextColor3 = THEME.Text,
            Text = text, TextXAlignment = Enum.TextXAlignment.Left, Parent = outer,
        })
        local vLbl = new("TextLabel", {
            AnchorPoint = Vector2.new(1, 0), Position = UDim2.new(1, 0, 0, 0),
            Size = UDim2.fromOffset(60, 16), BackgroundTransparency = 1,
            Font = Enum.Font.GothamBold, TextSize = 11, TextColor3 = THEME.Accent,
            Text = tostring(value), TextXAlignment = Enum.TextXAlignment.Right, Parent = outer,
        })

        local touchZone = new("Frame", {
            Position = UDim2.new(0, -4, 1, -(BAR_H+18)),
            Size = UDim2.new(1, 8, 0, BAR_H+28),
            BackgroundTransparency = 1, Parent = outer,
        })
        touchZone.Active = true

        local bar = new("Frame", {
            AnchorPoint = Vector2.new(0, 0.5),
            Position = UDim2.new(0, 4, 0.5, 0),
            Size = UDim2.new(1, -8, 0, BAR_H),
            BackgroundColor3 = THEME.Stroke, BorderSizePixel = 0, Parent = touchZone,
        })
        corner(bar, UDim.new(1, 0))

        local fill = new("Frame", {
            Size = UDim2.fromScale((value - min) / (max - min), 1),
            BackgroundColor3 = THEME.Accent, BorderSizePixel = 0, Parent = bar,
        })
        corner(fill, UDim.new(1, 0))

        local knob = new("Frame", {
            AnchorPoint = Vector2.new(0.5, 0.5),
            Position = UDim2.new((value - min) / (max - min), 0, 0.5, 0),
            Size = UDim2.fromOffset(KNOB, KNOB),
            BackgroundColor3 = THEME.Text, BorderSizePixel = 0, Parent = bar,
        })
        corner(knob, UDim.new(1, 0))
        stroke(knob, THEME.Accent, 2, 0)

        local dragging = false
        local function update(x)
            local rel = math.clamp((x - bar.AbsolutePosition.X) / bar.AbsoluteSize.X, 0, 1)
            value = math.floor(min + (max - min) * rel + 0.5)
            fill.Size = UDim2.fromScale(rel, 1)
            knob.Position = UDim2.new(rel, 0, 0.5, 0)
            vLbl.Text = tostring(value)
            if cb then pcall(cb, value) end
        end

        touchZone.InputBegan:Connect(function(i)
            if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
                dragging = true
                update(i.Position.X)
            end
        end)
        UserInputService.InputChanged:Connect(function(i)
            if dragging and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
                update(i.Position.X)
            end
        end)
        UserInputService.InputEnded:Connect(function(i)
            if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
                dragging = false
            end
        end)
        return outer
    end

    function api:AddDropdown(text, options, cb)
        local current = options[1] or "—"
        local r = new("Frame", {
            Size = UDim2.new(1, 0, 0, 30),
            BackgroundColor3 = THEME.SurfaceAlt, Parent = card,
        })
        corner(r, CORNER_SM)
        padding(r, 8)

        new("TextLabel", {
            Size = UDim2.new(0.45, 0, 1, 0), BackgroundTransparency = 1,
            Font = Enum.Font.Gotham, TextSize = 12, TextColor3 = THEME.Text,
            Text = text, TextXAlignment = Enum.TextXAlignment.Left, Parent = r,
        })

        local btn = new("TextButton", {
            AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, 0, 0.5, 0),
            Size = UDim2.new(0.5, 0, 0, 22),
            BackgroundColor3 = THEME.Surface, AutoButtonColor = false,
            Font = Enum.Font.Gotham, TextSize = 11, TextColor3 = THEME.Text,
            Text = "  " .. current .. "   ▾",
            TextXAlignment = Enum.TextXAlignment.Left, Parent = r,
        })
        corner(btn, CORNER_SM)
        stroke(btn, THEME.Stroke, 1, 0.4)

        local DH = 24
        local list = new("Frame", {
            Visible = false, Size = UDim2.new(1, 0, 0, #options * DH),
            BackgroundColor3 = THEME.Surface, Parent = card,
        })
        corner(list, CORNER_SM)
        stroke(list, THEME.Stroke, 1, 0.3)
        new("UIListLayout", {Parent = list})

        for _, opt in ipairs(options) do
            local b = new("TextButton", {
                Size = UDim2.new(1, 0, 0, DH),
                BackgroundTransparency = 1, AutoButtonColor = false,
                Font = Enum.Font.Gotham, TextSize = 11, TextColor3 = THEME.SubText,
                Text = opt, Parent = list,
            })
            b.MouseEnter:Connect(function() b.TextColor3 = THEME.Accent end)
            b.MouseLeave:Connect(function() b.TextColor3 = THEME.SubText end)
            b.MouseButton1Click:Connect(function()
                current = opt
                btn.Text = "  " .. current .. "   ▾"
                list.Visible = false
                if cb then pcall(cb, opt) end
                hub:Notify(text, "Selecionado: " .. opt, 2.5)
            end)
        end

        btn.MouseButton1Click:Connect(function() list.Visible = not list.Visible end)
        return r
    end

    return api
end

-- ==================== STATS ====================
function Onyx:_StartStats()
    task.spawn(function()
        while self.Gui and self.Gui.Parent do
            pcall(function()
                local fps = math.clamp(math.floor(1 / RunService.RenderStepped:Wait()), 1, 999)
                if self.PerfPill then self.PerfPill.Text = fps .. " FPS" end
                if self.FPSLabel then self.FPSLabel.Text = tostring(fps) end
                if self.PlayersLabel then self.PlayersLabel.Text = tostring(#Players:GetPlayers()) end
            end)
        end
    end)
end

-- ==================== MAIN ====================
local hub = setmetatable({}, Onyx):Init()

local function notify(title, text, dur) 
    hub:Notify(title, text, dur or 2) 
end

-- ==================== HOME TAB ====================
local home = hub:AddTab("Home", "🏠")
do
    local c1 = home:AddCard("Status")
    c1:AddInfo("Status", "✓ Carregado")
    c1:AddInfo("Executor", "Delta/Xeno")
    
    local c2 = home:AddCard("Player")
    c2:AddInfo("Nome", LP.Name)
    c2:AddInfo("User ID", tostring(LP.UserId))
    c2:AddInfo("Account Age", LP.AccountAge .. " dias")
    
    local c3 = home:AddCard("Sistema")
    hub.FPSLabel = c3:AddInfo("FPS", "—")
    hub.PlayersLabel = c3:AddInfo("Players", "—")
    local status = c3:AddInfo("Status", "Undetected ✓")
    status.TextColor3 = THEME.Success
end

-- ==================== FEATURES TAB ====================
local features = hub:AddTab("Features", "⚡")
do
    local c = features:AddCard("Informações")
    if hasDrawing then
        c:AddInfo("Drawing", "✓ Disponível")
    else
        c:AddInfo("Drawing", "✗ Não suportado")
    end
    c:AddInfo("Mobile", IS_MOBILE and "✓ Sim" or "✕ Não")
    c:AddInfo("GUI Parent", GuiParent.Name or "Workspace")
end

-- ==================== CONFIG TAB ====================
local cfg = hub:AddTab("Config", "⚙️")
do
    local c = cfg:AddCard("Interface")
    c:AddToggle("Notificações", true, function(s) hub.NotificationsEnabled = s end)
    c:AddButton("Minimizar Menu", function() hub:Minimize() end)
    c:AddButton("Maximizar Menu", function() hub:Maximize() end)
    
    local d = cfg:AddCard("Teste")
    d:AddSlider("Slider Test", 0, 100, 50, function(v)
        notify("Slider", "Valor: " .. v, 1)
    end)
    d:AddDropdown("Dropdown Test", {"Opção 1", "Opção 2", "Opção 3"}, function(v)
        notify("Dropdown", "Selecionado: " .. v, 1)
    end)
end

-- ==================== STARTUP ====================
task.delay(0.5, function()
    hub:Notify(
        "ONYX HUB",
        "✓ Carregado com sucesso!\nExecutor: Delta/Xeno\nDrawing: " .. (hasDrawing and "✓ Sim" or "✗ Não"),
        5
    )
end)

return hub
