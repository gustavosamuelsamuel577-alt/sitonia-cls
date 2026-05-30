--[[
    FLUXOVIP+ (Base: FLUXOVIP + Complementos do ONYX HUB)
    - Interface completa (temas, notificações, minimizar, arrastar)
    - Aimbot + BodyShot + RapidFire + Hold to Aim + Smooth + Mobile
    - ESP somente com Drawing (Box, Tracer, Distance, Rainbow, 3D, Skeleton, Studs, Inventory)
    - Noclip, Anti-Sit, Anti-AFK
    - Auto Farm Gari (com desvio de obstáculos)
    - Nitro Automático (tecla N / botão mobile)
    - Speed Car / Fly Car
    - Hitbox (100hs e LEGIT)
    - Fake Dash / Fake Lag / Spinbot / BHOP
    - Copiar roupas, Server Hop, Rejoin, Bypass
    - Whitelist (amigos)
    - Salvar/Carregar configuração (inclui todas as toggles)
    - TODAS AS FUNÇÕES INICIAM DESLIGADAS
    - SEM ESP HIGHLIGHT / CHAMS
--]]

local Players          = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService     = game:GetService("TweenService")
local RunService       = game:GetService("RunService")
local CoreGui          = game:GetService("CoreGui")
local Camera           = workspace.CurrentCamera
local SoundService     = game:GetService("SoundService")
local HttpService      = game:GetService("HttpService")
local TeleportService  = game:GetService("TeleportService")
local Workspace        = game:GetService("Workspace")
local Debris           = game:GetService("Debris")

local LP = Players.LocalPlayer

---------------------------------------------------------------- THEME
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

local THEMES = {
    ["CLS Pink"] = {
        Background=Color3.fromRGB(18,10,18), Surface=Color3.fromRGB(28,16,28),
        SurfaceAlt=Color3.fromRGB(38,22,38), Sidebar=Color3.fromRGB(22,12,22),
        Stroke=Color3.fromRGB(80,30,70), Text=Color3.fromRGB(255,255,255),
        SubText=Color3.fromRGB(220,190,210), Accent=Color3.fromRGB(255,20,147),
        AccentSoft=Color3.fromRGB(200,15,115),
    },
    ["Midnight"] = {
        Background=Color3.fromRGB(10,12,20), Surface=Color3.fromRGB(16,20,32),
        SurfaceAlt=Color3.fromRGB(22,28,44), Sidebar=Color3.fromRGB(12,14,24),
        Stroke=Color3.fromRGB(40,50,80), Text=Color3.fromRGB(235,240,255),
        SubText=Color3.fromRGB(140,150,180), Accent=Color3.fromRGB(120,90,255),
        AccentSoft=Color3.fromRGB(80,60,200),
    },
    ["Lurs Style"] = {
        Background=Color3.fromRGB(8,8,12), Surface=Color3.fromRGB(14,14,20),
        SurfaceAlt=Color3.fromRGB(22,22,30), Sidebar=Color3.fromRGB(10,10,16),
        Stroke=Color3.fromRGB(60,30,60), Text=Color3.fromRGB(245,235,245),
        SubText=Color3.fromRGB(160,140,170), Accent=Color3.fromRGB(220,40,200),
        AccentSoft=Color3.fromRGB(170,30,160),
    },
    ["Onyx Blue"] = {
        Background=Color3.fromRGB(14,16,22), Surface=Color3.fromRGB(20,23,32),
        SurfaceAlt=Color3.fromRGB(26,30,42), Sidebar=Color3.fromRGB(12,14,24),
        Stroke=Color3.fromRGB(40,46,62), Text=Color3.fromRGB(240,244,252),
        SubText=Color3.fromRGB(145,155,175), Accent=Color3.fromRGB(70,150,255),
        AccentSoft=Color3.fromRGB(50,110,220),
    },
    ["Emerald"] = {
        Background=Color3.fromRGB(10,18,14), Surface=Color3.fromRGB(16,28,22),
        SurfaceAlt=Color3.fromRGB(22,38,30), Sidebar=Color3.fromRGB(12,22,16),
        Stroke=Color3.fromRGB(30,70,50), Text=Color3.fromRGB(235,255,245),
        SubText=Color3.fromRGB(150,190,170), Accent=Color3.fromRGB(40,220,140),
        AccentSoft=Color3.fromRGB(30,170,110),
    },
    ["Sunset"] = {
        Background=Color3.fromRGB(22,12,10), Surface=Color3.fromRGB(34,18,14),
        SurfaceAlt=Color3.fromRGB(46,24,18), Sidebar=Color3.fromRGB(26,14,12),
        Stroke=Color3.fromRGB(90,40,30), Text=Color3.fromRGB(255,245,235),
        SubText=Color3.fromRGB(220,180,160), Accent=Color3.fromRGB(255,120,40),
        AccentSoft=Color3.fromRGB(220,80,30),
    },
}

local CORNER    = UDim.new(0, 12)
local CORNER_SM = UDim.new(0, 8)
local TWEEN     = TweenInfo.new(0.25, Enum.EasingStyle.Quart, Enum.EasingDirection.Out)
local TWEEN_FAST= TweenInfo.new(0.14, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)

local IS_MOBILE = UserInputService.TouchEnabled and not UserInputService.MouseEnabled
local function ms(base) return IS_MOBILE and (base + 4) or base end
local function mh(base) return IS_MOBILE and math.floor(base * 1.35) or base end

---------------------------------------------------------------- HELPERS
local function new(class, props, children)
    local i = Instance.new(class)
    for k, v in pairs(props or {}) do i[k] = v end
    for _, c in ipairs(children or {}) do c.Parent = i end
    return i
end
local function corner(p, r) return new("UICorner", {CornerRadius = r or CORNER, Parent = p}) end
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
local function tween(o, p, i) TweenService:Create(o, i or TWEEN, p):Play() end

---------------------------------------------------------------- ONYX HUB (UI)
local Onyx = {}
Onyx.__index = Onyx

function Onyx:Init()
    local old = (CoreGui:FindFirstChild("OnyxHub")) or (LP:FindFirstChild("PlayerGui") and LP.PlayerGui:FindFirstChild("OnyxHub"))
    if old then old:Destroy() end

    local gui = new("ScreenGui", {
        Name = "OnyxHub", IgnoreGuiInset = true,
        ResetOnSpawn = false, ZIndexBehavior = Enum.ZIndexBehavior.Sibling,
    })
    pcall(function() gui.Parent = CoreGui end)
    if not gui.Parent then gui.Parent = LP:WaitForChild("PlayerGui") end
    self.Gui = gui
    if self.NotificationsEnabled == nil then self.NotificationsEnabled = true end

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
    corner(main, CORNER); stroke(main, THEME.Accent, 1, 0.6)
    new("UISizeConstraint", {MinSize = Vector2.new(360, 320), MaxSize = Vector2.new(920, 600), Parent = main})
    new("UIAspectRatioConstraint", {
        AspectRatio = 1.56, DominantAxis = Enum.DominantAxis.Width,
        AspectType = Enum.AspectType.ScaleWithParentSize, Parent = main,
    })
    new("ImageLabel", {
        Name = "Shadow", AnchorPoint = Vector2.new(0.5, 0.5),
        Position = UDim2.fromScale(0.5, 0.5),
        Size = UDim2.new(1, 50, 1, 50),
        BackgroundTransparency = 1,
        Image = "rbxassetid://5028857084",
        ImageColor3 = Color3.fromRGB(0, 0, 0),
        ImageTransparency = 0.5,
        ScaleType = Enum.ScaleType.Slice,
        SliceCenter = Rect.new(24, 24, 276, 276),
        ZIndex = 0, Parent = main,
    })

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
        Text = "FLUXO", Parent = logo,
    })
    new("TextLabel", {
        Size = UDim2.fromOffset(35, 50), BackgroundTransparency = 1,
        Font = Enum.Font.GothamBold, TextSize = 17, TextColor3 = THEME.Accent,
        Text = "VIP", Parent = logo,
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
    self:BuildProfileCard(side)
end

function Onyx:BuildProfileCard(parent)
    local card = new("Frame", {
        Name = "Profile",
        AnchorPoint = Vector2.new(0.5, 1),
        Position = UDim2.new(0.5, 0, 1, -10),
        Size = UDim2.new(1, -16, 0, 60),
        BackgroundColor3 = THEME.SurfaceAlt, Parent = parent,
    })
    corner(card, UDim.new(0, 10)); stroke(card, THEME.Accent, 1, 0.7)

    local avatar = new("ImageLabel", {
        Position = UDim2.new(0, 8, 0.5, 0), AnchorPoint = Vector2.new(0, 0.5),
        Size = UDim2.fromOffset(40, 40), BackgroundColor3 = THEME.Surface,
        Image = "", Parent = card,
    })
    pcall(function()
        avatar.Image = Players:GetUserThumbnailAsync(LP.UserId,
            Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size100x100)
    end)
    new("UICorner", {CornerRadius = UDim.new(1, 0), Parent = avatar})
    stroke(avatar, THEME.Accent, 1, 0.5)

    new("TextLabel", {
        Position = UDim2.new(0, 56, 0, 10), Size = UDim2.new(1, -60, 0, 18),
        BackgroundTransparency = 1, Font = Enum.Font.GothamBold,
        TextSize = 13, TextColor3 = THEME.Text,
        Text = LP.Name, TextXAlignment = Enum.TextXAlignment.Left, Parent = card,
    })
    new("TextLabel", {
        Position = UDim2.new(0, 56, 0, 28), Size = UDim2.new(1, -60, 0, 18),
        BackgroundTransparency = 1, Font = Enum.Font.Gotham,
        TextSize = 11, TextColor3 = THEME.Accent,
        Text = "Premium Build", TextXAlignment = Enum.TextXAlignment.Left, Parent = card,
    })
end

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
                dragging = true; dragStart = i.Position; startPos = main.Position
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

    local function pill(name, label, value, color, posX)
        local p = new("Frame", {
            Position = UDim2.new(0, posX, 0.5, 0),
            AnchorPoint = Vector2.new(0, 0.5),
            Size = UDim2.fromOffset(150, 30),
            BackgroundColor3 = THEME.SurfaceAlt,
            Parent = top,
        })
        corner(p, CORNER_SM); stroke(p, THEME.Stroke, 1, 0.4)

        local dot = new("Frame", {
            Position = UDim2.fromOffset(10, 11), Size = UDim2.fromOffset(8, 8),
            BackgroundColor3 = color, BorderSizePixel = 0, Parent = p,
        })
        new("UICorner", {CornerRadius = UDim.new(1, 0), Parent = dot})

        local holder = new("Frame", {
            Position = UDim2.fromOffset(24, 0),
            Size = UDim2.new(1, -32, 1, 0),
            BackgroundTransparency = 1, Parent = p,
        })
        new("TextLabel", {
            Size = UDim2.new(0.6, 0, 1, 0), BackgroundTransparency = 1,
            Font = Enum.Font.Gotham, TextSize = 10, TextColor3 = THEME.SubText,
            Text = label, TextXAlignment = Enum.TextXAlignment.Left, Parent = holder,
        })
        local val = new("TextLabel", {
            Position = UDim2.new(0.6, 0, 0, 0),
            Size = UDim2.new(0.4, 0, 1, 0),
            BackgroundTransparency = 1, Font = Enum.Font.GothamBold,
            TextSize = 11, TextColor3 = THEME.Text,
            Text = value, TextXAlignment = Enum.TextXAlignment.Right, Parent = holder,
        })
        self[name] = val
        return p
    end

    pill("PerfPill", "FPS", "75", THEME.Success, 12)
    pill("LatPill",  "PING",  "0", THEME.Accent,  172)

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
        corner(b, CORNER_SM); stroke(b, THEME.Stroke, 1, 0.4)
        b.MouseEnter:Connect(function() tween(b, {BackgroundColor3 = color}, TWEEN_FAST); tween(b, {TextColor3 = Color3.new(1,1,1)}, TWEEN_FAST) end)
        b.MouseLeave:Connect(function() tween(b, {BackgroundColor3 = THEME.SurfaceAlt}, TWEEN_FAST); tween(b, {TextColor3 = color}, TWEEN_FAST) end)
        return b
    end

    local mini = ctrl("-", -12, THEME.Accent)
    mini.MouseButton1Click:Connect(function() self:Minimize() end)

    local close = ctrl("X", -50, THEME.Danger)
    close.MouseButton1Click:Connect(function() self:Minimize() end)
end

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
    corner(btn, UDim.new(1, 0)); stroke(btn, THEME.Accent, 2, 0.2)

    new("TextLabel", {
        Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1,
        Font = Enum.Font.GothamBold, TextSize = 18, TextColor3 = THEME.Accent,
        Text = "+", Parent = btn,
    })
    local glow = new("UIStroke", {
        Color = THEME.Accent, Thickness = 6, Transparency = 0.85, Parent = btn,
    })

    btn.MouseEnter:Connect(function() tween(glow, {Transparency = 0.6, Thickness = 8}, TWEEN_FAST) end)
    btn.MouseLeave:Connect(function() tween(glow, {Transparency = 0.85, Thickness = 6}, TWEEN_FAST) end)
    btn.MouseButton1Click:Connect(function() self:Maximize() end)

    local dragging, dragStart, startPos
    btn.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
            dragging = true; dragStart = i.Position; startPos = btn.Position
        end
    end)
    UserInputService.InputChanged:Connect(function(i)
        if dragging and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
            local d = i.Position - dragStart
            btn.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + d.X,
                                     startPos.Y.Scale, startPos.Y.Offset + d.Y)
        end
    end)
    UserInputService.InputEnded:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
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
    self:Notify("Menu minimizado", "Clique no ícone para abrir.")
end

function Onyx:Maximize()
    self.OpenBtn.Visible = false
    self.Main.Visible = true
    self.Main.Size = UDim2.fromScale(0, 0)
    tween(self.Main, {Size = self._BaseSize},
        TweenInfo.new(0.35, Enum.EasingStyle.Quint, Enum.EasingDirection.Out))
end

function Onyx:ApplyTheme(name)
    local preset = THEMES[name]; if not preset then return end
    local old = {Accent=THEME.Accent, AccentSoft=THEME.AccentSoft, Text=THEME.Text,
        SubText=THEME.SubText, Background=THEME.Background, Surface=THEME.Surface,
        SurfaceAlt=THEME.SurfaceAlt, Sidebar=THEME.Sidebar, Stroke=THEME.Stroke}
    for k,v in pairs(preset) do THEME[k] = v end
    if not self.Gui then return end
    local function near(a,b) return a and b and math.abs(a.R-b.R)<0.012 and math.abs(a.G-b.G)<0.012 and math.abs(a.B-b.B)<0.012 end
    local map = {
        {old.Accent, THEME.Accent}, {old.AccentSoft, THEME.AccentSoft},
        {old.Text, THEME.Text}, {old.SubText, THEME.SubText},
        {old.Background, THEME.Background}, {old.Surface, THEME.Surface},
        {old.SurfaceAlt, THEME.SurfaceAlt}, {old.Sidebar, THEME.Sidebar},
        {old.Stroke, THEME.Stroke},
    }
    local props = {"BackgroundColor3","TextColor3","ImageColor3","Color","ScrollBarImageColor3","PlaceholderColor3"}
    for _,d in ipairs(self.Gui:GetDescendants()) do
        for _,p in ipairs(props) do
            local ok, val = pcall(function() return d[p] end)
            if ok and typeof(val) == "Color3" then
                for _,pair in ipairs(map) do
                    if near(val, pair[1]) then pcall(function() d[p] = pair[2] end) break end
                end
            end
        end
    end
end

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
    if self.NotificationsEnabled == false then return end
    duration = duration or 3
    local n = new("Frame", {
        Size = UDim2.new(1, 0, 0, 56),
        BackgroundColor3 = THEME.Surface,
        Parent = self.NotifHolder,
    })
    corner(n, CORNER_SM); stroke(n, THEME.Accent, 1, 0.5)

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
            task.wait(0.2); n:Destroy()
        end
    end)
end

function Onyx:AddTab(name, icon)
    local tab = {Name = name}
    tab.Button = new("TextButton", {
        Size = UDim2.new(1, 0, 0, IS_MOBILE and 48 or 40),
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
        TextSize = ms(14), TextColor3 = THEME.SubText,
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

function Onyx._BuildCard(hub, tab, title)
    local card = new("Frame", {
        Size = UDim2.new(1, -4, 0, 40),
        AutomaticSize = Enum.AutomaticSize.Y,
        BackgroundColor3 = THEME.Surface,
        Parent = tab.Page,
    })
    corner(card, CORNER); stroke(card, THEME.Stroke, 1, 0.3); padding(card, 12)
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

    local function row(h)
        local r = new("Frame", {
            Size = UDim2.new(1, 0, 0, h or 30),
            BackgroundColor3 = THEME.SurfaceAlt, Parent = card,
        })
        corner(r, CORNER_SM); padding(r, 8)
        return r
    end

    function api:AddInfo(label, value)
        local r = row(mh(24))
        new("TextLabel", {
            Size = UDim2.new(0.5, 0, 1, 0), BackgroundTransparency = 1,
            Font = Enum.Font.Gotham, TextSize = ms(11), TextColor3 = THEME.SubText,
            Text = label, TextXAlignment = Enum.TextXAlignment.Left, Parent = r,
        })
        local v = new("TextLabel", {
            Position = UDim2.new(0.5, 0, 0, 0), Size = UDim2.new(0.5, 0, 1, 0),
            BackgroundTransparency = 1, Font = Enum.Font.GothamBold,
            TextSize = ms(11), TextColor3 = THEME.Text, Text = tostring(value),
            TextXAlignment = Enum.TextXAlignment.Right, Parent = r,
        })
        return v
    end

    function api:AddToggle(text, default, cb)
        local r = row(mh(30))
        new("TextLabel", {
            Size = UDim2.new(1, -56, 1, 0), BackgroundTransparency = 1,
            Font = Enum.Font.Gotham, TextSize = ms(12), TextColor3 = THEME.Text,
            Text = text, TextXAlignment = Enum.TextXAlignment.Left, Parent = r,
        })
        local sw = new("TextButton", {
            AnchorPoint = Vector2.new(1, 0.5),
            Position = UDim2.new(1, 0, 0.5, 0),
            Size = UDim2.fromOffset(IS_MOBILE and 52 or 38, IS_MOBILE and 28 or 20),
            BackgroundColor3 = THEME.Stroke,
            Text = "", AutoButtonColor = false, Parent = r,
        })
        corner(sw, UDim.new(1, 0))
        local KS = IS_MOBILE and 24 or 16
        local knob = new("Frame", {
            Size = UDim2.fromOffset(KS, KS), Position = UDim2.fromOffset(2, 2),
            BackgroundColor3 = THEME.Text, BorderSizePixel = 0, Parent = sw,
        })
        corner(knob, UDim.new(1, 0))

        local state = default and true or false
        local function refresh(silent)
            if state then
                tween(sw, {BackgroundColor3 = THEME.Accent})
                tween(knob, {Position = UDim2.fromOffset((IS_MOBILE and 26 or 20), 2)})
            else
                tween(sw, {BackgroundColor3 = THEME.Stroke})
                tween(knob, {Position = UDim2.fromOffset(2, 2)})
            end
            if cb then pcall(cb, state) end
            if not silent then
                hub:Notify(text, state and "Ativado" or "Desativado", 2.5)
            end
        end
        sw.MouseButton1Click:Connect(function() state = not state; refresh() end)
        if state then refresh(true) end
        return {Set = function(_, v) state = v; refresh() end}
    end

    function api:AddSlider(text, min, max, default, cb)
        min, max = min or 0, max or 100
        local value = default or min
        local OUTER_H = IS_MOBILE and 64 or 44
        local BAR_H   = IS_MOBILE and 10 or 5
        local KNOB    = IS_MOBILE and 22 or 14
        local outer = new("Frame", {
            Size = UDim2.new(1, 0, 0, OUTER_H),
            BackgroundColor3 = THEME.SurfaceAlt, Parent = card,
        })
        corner(outer, CORNER_SM); padding(outer, 8)
        new("TextLabel", {
            Size = UDim2.new(1, -60, 0, IS_MOBILE and 22 or 16), BackgroundTransparency = 1,
            Font = Enum.Font.Gotham, TextSize = ms(12), TextColor3 = THEME.Text,
            Text = text, TextXAlignment = Enum.TextXAlignment.Left, Parent = outer,
        })
        local vLbl = new("TextLabel", {
            AnchorPoint = Vector2.new(1, 0), Position = UDim2.new(1, 0, 0, 0),
            Size = UDim2.fromOffset(60, IS_MOBILE and 22 or 16), BackgroundTransparency = 1,
            Font = Enum.Font.GothamBold, TextSize = ms(11), TextColor3 = THEME.Accent,
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
        corner(knob, UDim.new(1, 0)); stroke(knob, THEME.Accent, 2, 0)

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
                dragging = true; update(i.Position.X)
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
        local r = row(mh(30))
        new("TextLabel", {
            Size = UDim2.new(0.45, 0, 1, 0), BackgroundTransparency = 1,
            Font = Enum.Font.Gotham, TextSize = ms(12), TextColor3 = THEME.Text,
            Text = text, TextXAlignment = Enum.TextXAlignment.Left, Parent = r,
        })
        local btn = new("TextButton", {
            AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, 0, 0.5, 0),
            Size = UDim2.new(0.5, 0, 0, IS_MOBILE and 30 or 22),
            BackgroundColor3 = THEME.Surface, AutoButtonColor = false,
            Font = Enum.Font.Gotham, TextSize = ms(11), TextColor3 = THEME.Text,
            Text = "  " .. current .. "   ▾",
            TextXAlignment = Enum.TextXAlignment.Left, Parent = r,
        })
        corner(btn, CORNER_SM); stroke(btn, THEME.Stroke, 1, 0.4)

        local DH = IS_MOBILE and 34 or 24
        local list = new("Frame", {
            Visible = false, Size = UDim2.new(1, 0, 0, #options * DH),
            BackgroundColor3 = THEME.Surface, Parent = card,
        })
        corner(list, CORNER_SM); stroke(list, THEME.Stroke, 1, 0.3)
        new("UIListLayout", {Parent = list})
        for _, opt in ipairs(options) do
            local b = new("TextButton", {
                Size = UDim2.new(1, 0, 0, DH),
                BackgroundTransparency = 1, AutoButtonColor = false,
                Font = Enum.Font.Gotham, TextSize = ms(11), TextColor3 = THEME.SubText,
                Text = opt, Parent = list,
            })
            b.MouseEnter:Connect(function() b.TextColor3 = THEME.Accent end)
            b.MouseLeave:Connect(function() b.TextColor3 = THEME.SubText end)
            b.MouseButton1Click:Connect(function()
                current = opt; btn.Text = "  " .. current .. "   ▾"
                list.Visible = false
                if cb then pcall(cb, opt) end
                hub:Notify(text, "Selecionado: " .. opt, 2.5)
            end)
        end
        btn.MouseButton1Click:Connect(function() list.Visible = not list.Visible end)
        return r
    end

    function api:AddButton(text, cb)
        local b = new("TextButton", {
            Size = UDim2.new(1, 0, 0, mh(30)),
            BackgroundColor3 = THEME.Accent, BackgroundTransparency = 0.1,
            AutoButtonColor = false,
            Font = Enum.Font.GothamBold, TextSize = ms(12), TextColor3 = THEME.Text,
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

    function api:AddKeybind(text, default, cb)
        local r = row(30)
        new("TextLabel", {
            Size = UDim2.new(1, -70, 1, 0), BackgroundTransparency = 1,
            Font = Enum.Font.Gotham, TextSize = 12, TextColor3 = THEME.Text,
            Text = text, TextXAlignment = Enum.TextXAlignment.Left, Parent = r,
        })
        local key = default or Enum.KeyCode.E
        local btn = new("TextButton", {
            AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, 0, 0.5, 0),
            Size = UDim2.fromOffset(64, 22),
            BackgroundColor3 = THEME.Surface, AutoButtonColor = false,
            Font = Enum.Font.GothamBold, TextSize = 10, TextColor3 = THEME.Accent,
            Text = key.Name, Parent = r,
        })
        corner(btn, CORNER_SM); stroke(btn, THEME.Stroke, 1, 0.4)
        btn.MouseButton1Click:Connect(function()
            btn.Text = "..."
            local c; c = UserInputService.InputBegan:Connect(function(i)
                if i.UserInputType == Enum.UserInputType.Keyboard then
                    key = i.KeyCode; btn.Text = key.Name
                    if cb then pcall(cb, key) end
                    hub:Notify(text, "Tecla: " .. key.Name, 2)
                    c:Disconnect()
                end
            end)
        end)
        return btn
    end

    function api:AddTextbox(text, placeholder, cb)
        local r = row(30)
        new("TextLabel", {
            Size = UDim2.new(0.4, 0, 1, 0), BackgroundTransparency = 1,
            Font = Enum.Font.Gotham, TextSize = 12, TextColor3 = THEME.Text,
            Text = text, TextXAlignment = Enum.TextXAlignment.Left, Parent = r,
        })
        local tb = new("TextBox", {
            AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, 0, 0.5, 0),
            Size = UDim2.new(0.55, 0, 0, 22),
            BackgroundColor3 = THEME.Surface, BorderSizePixel = 0,
            Font = Enum.Font.Gotham, TextSize = 11,
            TextColor3 = THEME.Text, PlaceholderColor3 = THEME.SubText,
            PlaceholderText = placeholder or "...",
            Text = "", ClearTextOnFocus = false, Parent = r,
        })
        corner(tb, CORNER_SM); stroke(tb, THEME.Stroke, 1, 0.4)
        tb.FocusLost:Connect(function() if cb then pcall(cb, tb.Text) end end)
        return tb
    end

    return api
end

function Onyx:_StartStats()
    task.spawn(function()
        while self.Gui and self.Gui.Parent do
            local fps = math.clamp(math.floor(1 / RunService.RenderStepped:Wait()), 1, 999)
            local ping = 0
            pcall(function() ping = math.floor(LP:GetNetworkPing() * 1000) end)
            if self.PerfPill then self.PerfPill.Text = fps .. " FPS" end
            if self.LatPill  then self.LatPill.Text  = ping .. " MS" end
            if self.FPSLabel then self.FPSLabel.Text = tostring(fps) end
            if self.PingLabel then self.PingLabel.Text = ping .. " ms" end
            if self.PlayersLabel then self.PlayersLabel.Text = tostring(#Players:GetPlayers()) end
        end
    end)
end

----------------------------------------------------------------
--                     FLUXOVIP+ FEATURES
--  Todas as variáveis começam desligadas (false / nil)
----------------------------------------------------------------

-- Aimbot (base FLUXO)
local aimbotEnabled = false
local FOVRadius = 200
local lockedTarget = nil
local killCheckEnabled = true
local wallCheckEnabled = true
local HeadOffset = 1

-- Body Shot
local bodyShotModeEnabled = false
local bodyShotThreshold = 2
local shotCounts = {}
local prevHealth = {}

-- RapidFire
local rapidFireEnabled = false
local rapidFireDelay = 0.06
local actionDown = false
local rapidFireLoop = nil

-- Hold to Aim
local holdToAimEnabled = false
local aimKey = Enum.KeyCode.E
local mobileAimDown = false

-- Smooth Aimbot (novo do sitonia)
local aimbotSmoothEnabled = false
local smoothSpeed = 0.15

-- Aimbot Mobile (novo)
local aimbotMobileEnabled = false
local aimbotMobileLookEnabled = false

-- Aimbot Hotkey (segurar)
local aimbotHotkeyEnabled = false
local hotkeyAimbotActive = false
local hotkeyBind = {Kind="KeyCode", Key=Enum.KeyCode.E}
local function IsHotkeyMatch(input)
    if not hotkeyBind or not hotkeyBind.Key then return false end
    if hotkeyBind.Kind=="KeyCode" then return input.KeyCode==hotkeyBind.Key end
    if hotkeyBind.Kind=="UserInputType" then return input.UserInputType==hotkeyBind.Key end
    return false
end

-- ESP (Drawing only, sem Highlight/Chams)
local drawingSupported, _ = pcall(function() return Drawing end)
local espEnabled = false
local tracersEnabled = false
local distanceEnabled = false
local rainbowESPEnabled = false
local box3dEnabled = false
local espColor = Color3.fromRGB(64, 156, 255)
local ESPS = {}

-- ESP extras do sitonia
local espBoxEnabled = false        -- box 2D (mesmo do drawing ESP)
local espLineEnabled = false       -- line from bottom of screen
local espSkeletonEnabled = false
local espNameEnabled = false
local espStudsEnabled = false      -- distância
local espInventoryEnabled = false
local espTracerEnabled = false     -- tracer from foot
local ESPRainbowActive = false
local ESP_Colors = {
    ESPName = Color3.fromRGB(255,255,255),
    ESPBox = Color3.fromRGB(0,255,120),
    ESPLine = Color3.fromRGB(0,255,120),
    ESPTracer = Color3.fromRGB(0,255,120),
}
local PlayerInvs = {}

-- Billboard (do FLUXO, mas sem highlight)
local billboardEnabled = false

-- Movement
local spinbotEnabled = false
local spinSpeed = 50
local spinbotConnection = nil

local fakeDashEnabled = false
local DashDistance = 8
local DashCooldown = 0.25
local LastDashTime = 0
local dashLoop = nil

local fakeLagEnabled = false
local LagDistance = 15
local LagCooldown = 1.0
local LagDistanceVariacoes = {12, 15, 18}
local forceIndex = 1
local LastLagTime = 0
local lagLoop = nil

-- Hitbox
local hitboxExpanded = false
local hitboxSize = 15
local hitboxTransparency = 0.7
local hitboxOriginais = {}
local legitAtivo = false
local legitTam = 6
local originaisLegit = {}
local visualPartsLegit = {}

-- Freeze
local freezePlayer = false

-- Audio
local audioEnhancerEnabled = false
local hitSound = Instance.new("Sound", workspace)
hitSound.SoundId = "rbxassetid://9120386403"
hitSound.Volume = 1
hitSound.Name = "FluxoHitSound"
local lastHitHealths = {}

-- Bypass
local bypassEnabled = false
local clickTimes = {}
local shiftTimes = {}
local previousParent = nil
local prevMenuVisible = true
local prevMaximizeVisible = true

-- BHOP
local bhopConn = nil

-- Copy clothes
local selectedCopyTarget = nil

-- Whitelist (amigos)
local friendSet = {}

-- Head size (próprio personagem)
local headSizeEnabled = false
local headSizeValue = 1.0
local headTransparency = 0
local headSizeConnection = nil

-- Noclip, Anti-Sit, Anti-AFK (novos do sitonia)
local noclipEnabled = false
local antiSitEnabled = false
local antiAfkEnabled = false
local noclipConn = nil
local antiSitConn = nil
local antiAfkThread = nil

-- Auto Farm Gari (novo)
local autoFarmGari = false
local TELEPORT_DELAY_SECONDS = 10

-- Nitro automático (novo)
local nitroAuto = false
local nitroForce = 50
local nitroConnection = nil
local nitroButtonMobile = nil

-- Speed Car / Fly Car (novos)
local speedCarEnabled = false
local vFlyEnabled = false
local carSensitivity = 2
local carVelocity = 0

-- Anti-kick (simples)
local lastKickTime = 0
local kickCooldown = 5

-- FOV Circle drawing
local drawingFOV = nil
local fovVisible = true

if drawingSupported then
    drawingFOV = Drawing.new("Circle")
    if drawingFOV then
        drawingFOV.Radius = FOVRadius
        drawingFOV.Color = THEME.Accent
        drawingFOV.Thickness = 2
        drawingFOV.Filled = false
        drawingFOV.Visible = false
    end
end

----------------------------------------------------------------
--                     FUNÇÕES AUXILIARES
----------------------------------------------------------------

local function GetChar() return LP.Character end
local function GetHRP() local c=GetChar(); return c and c:FindFirstChild("HumanoidRootPart") end

local function notify(title, text, dur) 
    if hub then hub:Notify(title, text, dur or 2) end
end

local function newDrawing(kind)
    if not drawingSupported then return nil end
    local ok, obj = pcall(function() return Drawing.new(kind) end)
    if ok then return obj end
    return nil
end

-- Head size loop
local function applyHeadSize()
    local char = LP.Character
    if not char then return end
    local head = char:FindFirstChild("Head")
    if head and head:IsA("BasePart") then
        pcall(function()
            head.Size = Vector3.new(headSizeValue, headSizeValue, headSizeValue)
            head.Transparency = headTransparency
        end)
    end
end

local function startHeadSizeLoop()
    if headSizeConnection then return end
    headSizeConnection = RunService.Heartbeat:Connect(function()
        if headSizeEnabled then
            applyHeadSize()
        else
            local char = LP.Character
            if char then
                local head = char:FindFirstChild("Head")
                if head and head:IsA("BasePart") then
                    pcall(function()
                        head.Size = Vector3.new(1, 1, 1)
                        head.Transparency = 0
                    end)
                end
            end
        end
    end)
end

-- Aimbot helpers (base FLUXO)
local function findHeadPart(char)
    if not char then return nil end
    for _, part in ipairs(char:GetChildren()) do
        if part:IsA("BasePart") then
            local lname = part.Name:lower()
            if lname:find("head") or lname:find("cabeca") or lname:find("cabesa") then
                return part
            end
        end
    end
    local highest = nil
    for _, part in ipairs(char:GetChildren()) do
        if part:IsA("BasePart") then
            if not highest or part.Position.Y > highest.Position.Y then
                highest = part
            end
        end
    end
    return highest or char:FindFirstChild("HumanoidRootPart") or char:FindFirstChildWhichIsA("BasePart")
end

local function findChestPart(char)
    if not char then return nil end
    for _, part in ipairs(char:GetChildren()) do
        if part:IsA("BasePart") then
            local lname = part.Name:lower()
            if lname:find("torso") or lname:find("uppertorso") or lname:find("chest") or lname:find("peito") then
                return part
            end
        end
    end
    return char:FindFirstChild("HumanoidRootPart") or char:FindFirstChildWhichIsA("BasePart")
end

local function isFriend(plr) return plr and friendSet[plr.UserId] == true end

local function getBestTarget()
    local center = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
    local closest, closestDist = nil, math.huge
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LP and plr.Character and not isFriend(plr) then
            local hum = plr.Character:FindFirstChildOfClass("Humanoid")
            if killCheckEnabled then if not hum or hum.Health <= 0 then continue end end
            local headPart = findHeadPart(plr.Character)
            local chestPart = findChestPart(plr.Character) or headPart
            local targetPart = headPart
            if bodyShotModeEnabled then
                local cnt = shotCounts[plr] or 0
                if cnt < bodyShotThreshold then targetPart = chestPart or headPart else targetPart = headPart end
            end
            if targetPart then
                local aimPosition = targetPart.Position
                if targetPart ~= headPart and HeadOffset ~= 0 then
                    aimPosition = aimPosition + Vector3.new(0, HeadOffset, 0)
                end
                local screenPos, onScreen = Camera:WorldToViewportPoint(aimPosition)
                if onScreen then
                    if wallCheckEnabled then
                        local rayParams = RaycastParams.new()
                        rayParams.FilterDescendantsInstances = {LP.Character}
                        rayParams.FilterType = Enum.RaycastFilterType.Blacklist
                        local rayResult = workspace:Raycast(Camera.CFrame.Position, (aimPosition - Camera.CFrame.Position).Unit * 1000, rayParams)
                        if rayResult and rayResult.Instance and rayResult.Instance:IsDescendantOf(plr.Character) then
                            local dist = (Vector2.new(screenPos.X, screenPos.Y) - center).Magnitude
                            if dist < closestDist and dist <= FOVRadius then closest, closestDist = targetPart, dist end
                        end
                    else
                        local dist = (Vector2.new(screenPos.X, screenPos.Y) - center).Magnitude
                        if dist < closestDist and dist <= FOVRadius then closest, closestDist = targetPart, dist end
                    end
                end
            end
        end
    end
    return closest
end

local function DoAim()
    local target = getBestTarget()
    if target then
        pcall(function()
            local aimPos = target.Position
            local headPart = findHeadPart(target.Parent)
            if headPart and target ~= headPart and HeadOffset ~= 0 then
                aimPos = aimPos + Vector3.new(0, HeadOffset, 0)
            end
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, aimPos)
            lockedTarget = target
        end)
    else lockedTarget = nil end
end

local function DoSmoothAim()
    local target = getBestTarget()
    if target then
        pcall(function()
            local aimPos = target.Position
            local headPart = findHeadPart(target.Parent)
            if headPart and target ~= headPart and HeadOffset ~= 0 then
                aimPos = aimPos + Vector3.new(0, HeadOffset, 0)
            end
            local newCF = CFrame.new(Camera.CFrame.Position, aimPos)
            Camera.CFrame = Camera.CFrame:Lerp(newCF, smoothSpeed)
            lockedTarget = target
        end)
    else lockedTarget = nil end
end

local function updateFOVVisual()
    if drawingSupported and drawingFOV then
        pcall(function()
            drawingFOV.Radius = FOVRadius
            drawingFOV.Position = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
            drawingFOV.Color = THEME.Accent
            drawingFOV.Visible = aimbotEnabled and fovVisible
        end)
    end
end

-- RapidFire
local function startRapidFire()
    if rapidFireLoop then return end
    rapidFireLoop = task.spawn(function()
        while actionDown and rapidFireEnabled do
            pcall(function()
                local char = LP.Character
                if char then
                    local tool = nil
                    for _, c in ipairs(char:GetChildren()) do
                        if c:IsA("Tool") then tool = c; break end
                    end
                    if tool and tool.Parent == char then
                        pcall(function() tool:Activate() end)
                    end
                end
            end)
            task.wait(rapidFireDelay)
        end
        rapidFireLoop = nil
    end)
end

UserInputService.InputBegan:Connect(function(input, processed)
    if processed then return end
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        actionDown = true
        if rapidFireEnabled then startRapidFire() end
    end
end)
UserInputService.InputEnded:Connect(function(input, processed)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        actionDown = false
        mobileAimDown = false
    end
end)

-- Hotkey aimbot
UserInputService.InputBegan:Connect(function(input)
    if aimbotHotkeyEnabled and IsHotkeyMatch(input) then hotkeyAimbotActive = true end
end)
UserInputService.InputEnded:Connect(function(input)
    if IsHotkeyMatch(input) then hotkeyAimbotActive = false end
end)

-- Aimbot loops (PC / Mobile)
local aimbotThread = nil
local function AimbotLoopPC()
    while aimbotEnabled and not holdToAimEnabled do
        if aimbotSmoothEnabled then DoSmoothAim() else DoAim() end
        task.wait()
    end
end

local function SetupMobileAutoAim()
    local conn
    conn = RunService.RenderStepped:Connect(function()
        if aimbotMobileEnabled and not holdToAimEnabled then
            if aimbotSmoothEnabled then DoSmoothAim() else DoAim() end
        end
    end)
    return conn
end
local mobileAutoAimConn = nil

local function SetupMobileLookAim()
    local conn
    conn = RunService.RenderStepped:Connect(function()
        if aimbotMobileLookEnabled then
            if aimbotSmoothEnabled then DoSmoothAim() else DoAim() end
        end
    end)
    return conn
end
local mobileLookConn = nil

-- Smooth aimbot connection
local smoothAimbotConn = nil

-- RenderStepped principal para aimbot condicional
RunService.RenderStepped:Connect(function()
    updateFOVVisual()
    if not aimbotEnabled then return end
    -- Hold to Aim
    if holdToAimEnabled then
        local active = false
        if IS_MOBILE then active = mobileAimDown else active = UserInputService:IsKeyDown(aimKey) end
        if not active then lockedTarget = nil; return end
        if aimbotSmoothEnabled then DoSmoothAim() else DoAim() end
    end
    -- Aimbot Hotkey
    if aimbotHotkeyEnabled and hotkeyAimbotActive then
        if aimbotSmoothEnabled then DoSmoothAim() else DoAim() end
    end
    -- Aimbot PC normal (sem hold) já tratado em loop separado
end)

-- Iniciar aimbot PC
task.spawn(function()
    while true do
        if aimbotEnabled and not holdToAimEnabled and not aimbotHotkeyEnabled and not IS_MOBILE then
            if aimbotSmoothEnabled then DoSmoothAim() else DoAim() end
        end
        task.wait()
    end
end)

-- ESP Drawing (base FLUXO + extras do sitonia)
local function ensureStorage(name)
    local g = LP.PlayerGui:FindFirstChild(name)
    if not g then
        g = Instance.new("Folder")
        g.Name = name
        g.Parent = LP.PlayerGui
    end
    return g
end

local function createBillboardForCharacter(char, plr)
    if not char or not plr or not billboardEnabled then return end
    local storage = ensureStorage("Stopped_Billboard_Storage")
    local nodeName = plr.Name.."_bb"
    if storage:FindFirstChild(nodeName) then storage[nodeName]:Destroy() end

    local head = findHeadPart(char)
    if not head then return end

    local bb = Instance.new("BillboardGui")
    bb.Name = nodeName
    bb.Size = UDim2.new(0, 140, 0, 48)
    bb.StudsOffset = Vector3.new(0, 2.4, 0)
    bb.Adornee = head
    bb.AlwaysOnTop = true
    bb.Parent = storage

    local bg = Instance.new("Frame", bb)
    bg.Size = UDim2.new(1, 0, 1, 0)
    bg.BackgroundTransparency = 0.55
    bg.BackgroundColor3 = Color3.fromRGB(10,10,10)
    bg.BorderSizePixel = 0
    Instance.new("UICorner", bg).CornerRadius = UDim.new(0,6)

    local nameLbl = Instance.new("TextLabel", bg)
    nameLbl.Size = UDim2.new(1, -8, 0, 18)
    nameLbl.Position = UDim2.new(0,4,0,2)
    nameLbl.BackgroundTransparency = 1
    nameLbl.Font = Enum.Font.GothamBold
    nameLbl.TextSize = 14
    nameLbl.TextColor3 = THEME.Text
    nameLbl.TextXAlignment = Enum.TextXAlignment.Left
    nameLbl.Text = plr.Name

    local infoLbl = Instance.new("TextLabel", bg)
    infoLbl.Size = UDim2.new(1, -8, 0, 16)
    infoLbl.Position = UDim2.new(0,4,0,22)
    infoLbl.BackgroundTransparency = 1
    infoLbl.Font = Enum.Font.Gotham
    infoLbl.TextSize = 12
    infoLbl.TextColor3 = THEME.SubText
    infoLbl.TextXAlignment = Enum.TextXAlignment.Left
    infoLbl.Text = "0m | HP: --"
end

local function destroyBillboardForCharacter(char, plr)
    local storage = LP.PlayerGui:FindFirstChild("Stopped_Billboard_Storage")
    if not storage then return end
    local nodeName = (plr and plr.Name) and (plr.Name.."_bb") or nil
    if nodeName and storage:FindFirstChild(nodeName) then pcall(function() storage[nodeName]:Destroy() end) end
end

local function destroyAllBillboards()
    local storage = LP.PlayerGui:FindFirstChild("Stopped_Billboard_Storage")
    if storage then pcall(function() storage:Destroy() end) end
end

local function createESPObjectsForPlayer(plr)
    if not drawingSupported then return nil end
    if ESPS[plr] then return ESPS[plr] end
    local data = {}
    data.box = newDrawing("Quad")
    if data.box then data.box.Thickness = 2; data.box.Transparency = 1; data.box.Visible = false end
    data.healthText = newDrawing("Text")
    if data.healthText then data.healthText.Size = 14; data.healthText.Color = Color3.fromRGB(255,255,255); data.healthText.Center = true; data.healthText.Outline = true; data.healthText.Visible = false end
    data.healthBar = newDrawing("Line")
    if data.healthBar then data.healthBar.Thickness = 4; data.healthBar.Transparency = 1; data.healthBar.Visible = false end
    data.tracer = newDrawing("Line")
    if data.tracer then data.tracer.Thickness = 2; data.tracer.Transparency = 0.8; data.tracer.Visible = false end
    data.distanceText = newDrawing("Text")
    if data.distanceText then data.distanceText.Size = 14; data.distanceText.Color = Color3.fromRGB(200,200,255); data.distanceText.Center = true; data.distanceText.Outline = true; data.distanceText.Visible = false end
    data.nameText = newDrawing("Text")
    if data.nameText then data.nameText.Size = 15; data.nameText.Color = Color3.fromRGB(200,230,255); data.nameText.Center = true; data.nameText.Outline = true; data.nameText.Visible = false end
    data.box3dLines = {}
    for i = 1, 12 do
        local l = newDrawing("Line")
        if l then l.Thickness = 2; l.Visible = false end
        table.insert(data.box3dLines, l)
    end
    -- Extras do sitonia
    data.lineBottom = newDrawing("Line")
    if data.lineBottom then data.lineBottom.Thickness = 2; data.lineBottom.Transparency = 1; data.lineBottom.Visible = false end
    data.skeletonLines = {}
    for i = 1, 14 do
        local l = newDrawing("Line")
        if l then l.Thickness = 1; l.Visible = false end
        table.insert(data.skeletonLines, l)
    end
    data.studsText = newDrawing("Text")
    if data.studsText then data.studsText.Size = 12; data.studsText.Color = Color3.fromRGB(255,255,255); data.studsText.Center = true; data.studsText.Outline = true; data.studsText.Visible = false end
    data.tracerFoot = newDrawing("Line")
    if data.tracerFoot then data.tracerFoot.Thickness = 2; data.tracerFoot.Transparency = 0.8; data.tracerFoot.Visible = false end
    ESPS[plr] = data
    return data
end

local function removeESPObjects(plr)
    local data = ESPS[plr]
    if not data then return end
    local function safeRemove(obj)
        if not obj then return end
        pcall(function()
            if obj.Visible ~= nil then obj.Visible = false end
            if obj.Remove then obj:Remove() end
        end)
    end
    safeRemove(data.box)
    safeRemove(data.healthText)
    safeRemove(data.healthBar)
    safeRemove(data.tracer)
    safeRemove(data.distanceText)
    safeRemove(data.nameText)
    for _, l in ipairs(data.box3dLines) do safeRemove(l) end
    safeRemove(data.lineBottom)
    for _, l in ipairs(data.skeletonLines) do safeRemove(l) end
    safeRemove(data.studsText)
    safeRemove(data.tracerFoot)
    ESPS[plr] = nil
end

-- Atualização do inventário dos jogadores
task.spawn(function()
    while true do
        if espInventoryEnabled or espNameEnabled then
            for _, p in pairs(Players:GetPlayers()) do
                if p ~= LP then
                    local items = {}
                    if p:FindFirstChild("Backpack") then
                        for _, item in pairs(p.Backpack:GetChildren()) do table.insert(items, item.Name) end
                    end
                    if p.Character then
                        for _, item in pairs(p.Character:GetChildren()) do
                            if item:IsA("Tool") then table.insert(items, item.Name .. " (Mão)") end
                        end
                    end
                    PlayerInvs[p] = #items > 0 and table.concat(items, ", ") or "Vazio"
                end
            end
        end
        task.wait(5)
    end
end)

-- RenderStepped ESP completo
RunService.RenderStepped:Connect(function()
    if not drawingSupported then return end
    -- Atualiza rainbow se ativo
    if rainbowESPEnabled or ESPRainbowActive then
        local hue = (tick() % 5) / 5
        espColor = Color3.fromHSV(hue, 1, 1)
        ESP_Colors.ESPBox = espColor
        ESP_Colors.ESPLine = espColor
        ESP_Colors.ESPTracer = espColor
    end

    for plr, data in pairs(ESPS) do
        if not plr or not plr.Character or plr == LP or isFriend(plr) then
            if data.box then data.box.Visible = false end
            if data.healthText then data.healthText.Visible = false end
            if data.healthBar then data.healthBar.Visible = false end
            if data.tracer then data.tracer.Visible = false end
            if data.distanceText then data.distanceText.Visible = false end
            if data.nameText then data.nameText.Visible = false end
            for _, l in ipairs(data.box3dLines) do if l then l.Visible = false end end
            if data.lineBottom then data.lineBottom.Visible = false end
            if data.studsText then data.studsText.Visible = false end
            if data.tracerFoot then data.tracerFoot.Visible = false end
            for _, l in ipairs(data.skeletonLines) do if l then l.Visible = false end end
        else
            local char = plr.Character
            local root = char:FindFirstChild("HumanoidRootPart") or char:FindFirstChild("Torso") or char:FindFirstChildWhichIsA("BasePart")
            local hum = char:FindFirstChildOfClass("Humanoid")
            if not root or not hum or hum.Health <= 0 then
                -- ocultar
                if data.box then data.box.Visible = false end
                if data.healthText then data.healthText.Visible = false end
                if data.healthBar then data.healthBar.Visible = false end
                if data.tracer then data.tracer.Visible = false end
                if data.distanceText then data.distanceText.Visible = false end
                if data.nameText then data.nameText.Visible = false end
                for _, l in ipairs(data.box3dLines) do if l then l.Visible = false end end
                if data.lineBottom then data.lineBottom.Visible = false end
                if data.studsText then data.studsText.Visible = false end
                if data.tracerFoot then data.tracerFoot.Visible = false end
                for _, l in ipairs(data.skeletonLines) do if l then l.Visible = false end end
            else
                local pos = root.Position
                local size = Vector3.new(2, 3, 1.5)
                local tl = Camera:WorldToViewportPoint(pos + Vector3.new(-size.X, size.Y, 0))
                local tr = Camera:WorldToViewportPoint(pos + Vector3.new(size.X, size.Y, 0))
                local bl = Camera:WorldToViewportPoint(pos + Vector3.new(-size.X, -size.Y, 0))
                local br = Camera:WorldToViewportPoint(pos + Vector3.new(size.X, -size.Y, 0))
                local col = (isFriend(plr) and Color3.fromRGB(80,220,80)) or espColor

                -- Box 2D
                if data.box and espBoxEnabled and tl.Z > 0 and tr.Z > 0 and bl.Z > 0 and br.Z > 0 then
                    data.box.PointA = Vector2.new(tl.X, tl.Y)
                    data.box.PointB = Vector2.new(tr.X, tr.Y)
                    data.box.PointC = Vector2.new(br.X, br.Y)
                    data.box.PointD = Vector2.new(bl.X, bl.Y)
                    data.box.Color = col
                    data.box.Visible = true
                elseif data.box then data.box.Visible = false end

                -- Box 3D
                if espEnabled and box3dEnabled then
                    local corners = (function(cf, s)
                        local c = {}
                        local sx, sy, sz = s.X/2, s.Y/2, s.Z/2
                        local points = {
                            Vector3.new(-sx, -sy, -sz), Vector3.new(sx, -sy, -sz),
                            Vector3.new(sx, sy, -sz), Vector3.new(-sx, sy, -sz),
                            Vector3.new(-sx, -sy, sz), Vector3.new(sx, -sy, sz),
                            Vector3.new(sx, sy, sz), Vector3.new(-sx, sy, sz)
                        }
                        for i, p in ipairs(points) do
                            local world = root.CFrame:PointToWorldSpace(p)
                            local screen, vis = Camera:WorldToViewportPoint(world)
                            c[i] = {Vector2.new(screen.X, screen.Y), vis and screen.Z > 0}
                        end
                        return c
                    end)(root.CFrame, size * 1.1)
                    local indices = {{1,2},{2,3},{3,4},{4,1},{5,6},{6,7},{7,8},{8,5},{1,5},{2,6},{3,7},{4,8}}
                    for i = 1, 12 do
                        local a, b = indices[i][1], indices[i][2]
                        local line = data.box3dLines[i]
                        if corners[a][2] and corners[b][2] and line then
                            line.From = corners[a][1]
                            line.To = corners[b][1]
                            line.Color = col
                            line.Visible = true
                        elseif line then
                            line.Visible = false
                        end
                    end
                else
                    for _, l in ipairs(data.box3dLines) do if l then l.Visible = false end end
                end

                -- Line from bottom (ESP Line)
                if data.lineBottom and espLineEnabled and tl.Z > 0 then
                    data.lineBottom.Visible = true
                    data.lineBottom.From = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y)
                    data.lineBottom.To = Vector2.new(bl.X, bl.Y)
                    data.lineBottom.Color = col
                elseif data.lineBottom then data.lineBottom.Visible = false end

                -- Skeleton (base do sitonia)
                if data.skeletonLines and espSkeletonEnabled then
                    local bones = {
                        {"Head","UpperTorso"},{"UpperTorso","LowerTorso"},
                        {"UpperTorso","RightUpperArm"},{"RightUpperArm","RightLowerArm"},{"RightLowerArm","RightHand"},
                        {"UpperTorso","LeftUpperArm"},{"LeftUpperArm","LeftLowerArm"},{"LeftLowerArm","LeftHand"},
                        {"LowerTorso","RightUpperLeg"},{"RightUpperLeg","RightLowerLeg"},{"RightLowerLeg","RightFoot"},
                        {"LowerTorso","LeftUpperLeg"},{"LeftUpperLeg","LeftLowerLeg"},{"LeftLowerLeg","LeftFoot"},
                    }
                    for i, bonePair in ipairs(bones) do
                        local partA = char:FindFirstChild(bonePair[1])
                        local partB = char:FindFirstChild(bonePair[2])
                        if partA and partB and partA:IsA("BasePart") and partB:IsA("BasePart") then
                            local aVec, aOn = Camera:WorldToViewportPoint(partA.Position)
                            local bVec, bOn = Camera:WorldToViewportPoint(partB.Position)
                            if aOn and bOn then
                                local line = data.skeletonLines[i]
                                if line then
                                    line.From = Vector2.new(aVec.X, aVec.Y)
                                    line.To = Vector2.new(bVec.X, bVec.Y)
                                    line.Color = col
                                    line.Visible = true
                                end
                            elseif data.skeletonLines[i] then
                                data.skeletonLines[i].Visible = false
                            end
                        elseif data.skeletonLines[i] then
                            data.skeletonLines[i].Visible = false
                        end
                    end
                else
                    for _, l in ipairs(data.skeletonLines) do if l then l.Visible = false end end
                end

                -- Name, Health, Distance, Studs, Inventory
                if tl.Z > 0 and tr.Z > 0 and bl.Z > 0 and br.Z > 0 then
                    if espEnabled then
                        if espNameEnabled and data.nameText then
                            local txt = plr.DisplayName or plr.Name
                            if espInventoryEnabled and PlayerInvs[plr] then
                                txt = txt .. "\n" .. PlayerInvs[plr]
                            end
                            data.nameText.Text = txt
                            data.nameText.Position = Vector2.new((tl.X + tr.X)/2, tl.Y - 18)
                            data.nameText.Color = col
                            data.nameText.Visible = true
                        elseif data.nameText then data.nameText.Visible = false end

                        if data.healthText then
                            data.healthText.Text = tostring(math.floor(hum.Health)) .. " HP"
                            data.healthText.Position = Vector2.new((tl.X + tr.X)/2, tl.Y - 32)
                            data.healthText.Color = col
                            data.healthText.Visible = true
                        end
                        if data.healthBar then
                            local hpPercent = hum.Health / math.max(1, hum.MaxHealth)
                            local barHeight = (bl.Y - tl.Y) * hpPercent
                            data.healthBar.From = Vector2.new(bl.X - 6, bl.Y)
                            data.healthBar.To = Vector2.new(bl.X - 6, bl.Y - barHeight)
                            data.healthBar.Color = hpPercent > 0.5 and Color3.fromRGB(0,255,0) or hpPercent > 0.2 and Color3.fromRGB(255,255,0) or Color3.fromRGB(255,0,0)
                            data.healthBar.Visible = true
                        end
                        if distanceEnabled and data.distanceText then
                            local dist = (Camera.CFrame.Position - root.Position).Magnitude
                            data.distanceText.Text = string.format("%.1f m", dist)
                            data.distanceText.Position = Vector2.new((bl.X + br.X)/2, bl.Y + 18)
                            data.distanceText.Visible = true
                            data.distanceText.Color = col
                        elseif data.distanceText then data.distanceText.Visible = false end

                        if espStudsEnabled and data.studsText then
                            local dist = (Camera.CFrame.Position - root.Position).Magnitude
                            data.studsText.Text = math.floor(dist) .. "m"
                            data.studsText.Position = Vector2.new(bl.X, bl.Y + 12)
                            data.studsText.Visible = true
                            data.studsText.Color = col
                        elseif data.studsText then data.studsText.Visible = false end

                        if espTracerEnabled and data.tracerFoot then
                            local footPos = root.Position + Vector3.new(0, -3, 0)
                            local footScreen, footOn = Camera:WorldToViewportPoint(footPos)
                            if footOn then
                                data.tracerFoot.From = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y)
                                data.tracerFoot.To = Vector2.new(footScreen.X, footScreen.Y)
                                data.tracerFoot.Color = col
                                data.tracerFoot.Visible = true
                            else
                                data.tracerFoot.Visible = false
                            end
                        elseif data.tracerFoot then data.tracerFoot.Visible = false end
                    else
                        if data.nameText then data.nameText.Visible = false end
                        if data.healthText then data.healthText.Visible = false end
                        if data.healthBar then data.healthBar.Visible = false end
                        if data.distanceText then data.distanceText.Visible = false end
                        if data.studsText then data.studsText.Visible = false end
                        if data.tracerFoot then data.tracerFoot.Visible = false end
                    end
                    if data.tracer and tracersEnabled then
                        data.tracer.Visible = true
                        data.tracer.From = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y)
                        data.tracer.To = Vector2.new((bl.X + br.X)/2, (bl.Y + br.Y)/2)
                        data.tracer.Color = col
                    elseif data.tracer then data.tracer.Visible = false end
                else
                    if data.nameText then data.nameText.Visible = false end
                    if data.healthText then data.healthText.Visible = false end
                    if data.healthBar then data.healthBar.Visible = false end
                    if data.tracer then data.tracer.Visible = false end
                    if data.distanceText then data.distanceText.Visible = false end
                    if data.studsText then data.studsText.Visible = false end
                    if data.tracerFoot then data.tracerFoot.Visible = false end
                end
            end
        end
    end
end)

-- Billboard update
RunService.RenderStepped:Connect(function()
    if billboardEnabled then
        local storage = LP.PlayerGui:FindFirstChild("Stopped_Billboard_Storage")
        if storage then
            for _, bb in ipairs(storage:GetChildren()) do
                if bb:IsA("BillboardGui") then
                    local ador = bb.Adornee
                    if not ador or not ador.Parent then pcall(function() bb:Destroy() end) else
                        local plr = Players:GetPlayerFromCharacter(ador.Parent)
                        if plr and not isFriend(plr) then
                            local humanoid = ador.Parent:FindFirstChildOfClass("Humanoid")
                            local hp = humanoid and math.floor(humanoid.Health) or 0
                            local root = LP.Character and LP.Character:FindFirstChild("HumanoidRootPart")
                            local dist = root and (root.Position - ador.Position).Magnitude or 0
                            local textLabels = {}
                            for _,c in ipairs(bb:GetDescendants()) do if c:IsA("TextLabel") then table.insert(textLabels, c) end end
                            local infoLabel = textLabels[2]
                            if infoLabel then
                                local distDisplay = (dist>=1000) and string.format("%.1fk", dist/1000) or string.format("%dm", math.floor(dist))
                                infoLabel.Text = distDisplay.." | HP: "..tostring(hp)
                            end
                        end
                    end
                end
            end
        end
    end
end)

-- Players events for ESP
Players.PlayerAdded:Connect(function(plr)
    shotCounts[plr] = 0; prevHealth[plr] = nil
    plr.CharacterAdded:Connect(function(char)
        if billboardEnabled then createBillboardForCharacter(char, plr) end
        if drawingSupported then createESPObjectsForPlayer(plr) end
    end)
    if plr.Character then
        if billboardEnabled then createBillboardForCharacter(plr.Character, plr) end
        if drawingSupported then createESPObjectsForPlayer(plr) end
    end
end)
Players.PlayerRemoving:Connect(function(plr)
    local char = plr.Character
    if char then destroyBillboardForCharacter(char, plr) end
    shotCounts[plr] = nil; prevHealth[plr] = nil; friendSet[plr.UserId] = nil
    if drawingSupported then removeESPObjects(plr) end
end)

-- Inicializar ESP para players existentes
for _, plr in ipairs(Players:GetPlayers()) do
    shotCounts[plr] = 0
    plr.CharacterAdded:Connect(function(char)
        if billboardEnabled then createBillboardForCharacter(char, plr) end
        if drawingSupported then createESPObjectsForPlayer(plr) end
    end)
    if plr.Character then
        if billboardEnabled then createBillboardForCharacter(plr.Character, plr) end
        if drawingSupported then createESPObjectsForPlayer(plr) end
    end
end

-- Hitbox 100hs e LEGIT
local function expandirUpperTorso(char)
    local part = char:FindFirstChild("UpperTorso")
    if part and part:IsA("BasePart") then
        if not hitboxOriginais[part] then
            hitboxOriginais[part] = {
                Size = part.Size,
                Transparency = part.Transparency,
                Material = part.Material,
                Color = part.Color,
                CanCollide = part.CanCollide,
                Massless = part.Massless,
            }
        end
        part.Size = Vector3.new(hitboxSize, hitboxSize, hitboxSize)
        part.Transparency = hitboxTransparency
        part.Material = Enum.Material.Neon
        part.Color = Color3.fromRGB(255, 0, 0)
        part.CanCollide = false
        part.Massless = true
    end
end

local function criarVisualPart(torso)
    if visualPartsLegit[torso] then return end
    local ok, visual = pcall(function() return torso:Clone() end)
    if not ok or not visual then return end
    visual.Size = torso.Size
    visual.CFrame = torso.CFrame
    visual.Anchored = false
    visual.CanCollide = false
    visual.Transparency = 0
    visual.Parent = torso.Parent
    local weld = Instance.new("Weld")
    weld.Part0 = torso
    weld.Part1 = visual
    weld.Parent = visual
    visualPartsLegit[torso] = visual
end

local function expandirTronco(char)
    local torso = char:FindFirstChild("UpperTorso")
    if torso and torso:IsA("BasePart") then
        if not originaisLegit[torso] then
            originaisLegit[torso] = {
                Size = torso.Size,
                Transparency = torso.Transparency
            }
            criarVisualPart(torso)
        end
        torso.Size = Vector3.new(legitTam, legitTam, legitTam)
        torso.Transparency = 1
    end
end

-- Loops de hitbox
RunService.RenderStepped:Connect(function()
    if freezePlayer then
        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= LP and plr.Character then
                local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                if hrp then pcall(function() hrp.Anchored = true end) end
            end
        end
    end
    if hitboxExpanded then
        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= LP and plr.Character then pcall(expandirUpperTorso, plr.Character) end
        end
    end
    if legitAtivo then
        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= LP and plr.Character then pcall(expandirTronco, plr.Character) end
        end
    end
end)

-- Audio enhancer + hit sound
task.spawn(function()
    while task.wait(0.5) do
        if audioEnhancerEnabled then
            for _, obj in pairs(workspace:GetDescendants()) do
                if obj:IsA("Sound") then
                    local soundId = tostring(obj.SoundId or ""):lower()
                    if soundId:find("gun") or soundId:find("shoot") or soundId:find("fire") 
                       or soundId:find("weapon") or soundId:find("rifle") or soundId:find("pistol") then
                        obj.Volume = math.clamp((obj.Volume or 1) * 0.3, 0, 0.3)
                    end
                end
            end
            for _, player in pairs(Players:GetPlayers()) do
                if player ~= LP and player.Character then
                    local humanoid = player.Character:FindFirstChild("Humanoid")
                    if humanoid then
                        for _, sound in pairs(humanoid:GetChildren()) do
                            if sound:IsA("Sound") and string.lower(sound.Name):find("running") then
                                sound.Volume = math.min((sound.Volume or 1) * 1.8, 1.5)
                            end
                        end
                    end
                end
            end
            SoundService.AmbientReverb = Enum.ReverbType.NoReverb
        end
    end
end)

task.spawn(function()
    while task.wait(0.1) do
        if audioEnhancerEnabled then
            for _, player in pairs(Players:GetPlayers()) do
                if player ~= LP and player.Character then
                    local humanoid = player.Character:FindFirstChild("Humanoid")
                    if humanoid and humanoid.Health > 0 then
                        if not lastHitHealths[player] then
                            lastHitHealths[player] = humanoid.Health
                        end
                        if humanoid.Health < lastHitHealths[player] then
                            hitSound:Stop()
                            hitSound:Play()
                        end
                        lastHitHealths[player] = humanoid.Health
                    end
                end
            end
        end
    end
end)

-- Noclip, Anti-Sit, Anti-AFK
local function ToggleNoclip(s)
    noclipEnabled = s
    if s then
        if noclipConn then return end
        noclipConn = RunService.Stepped:Connect(function()
            if not noclipEnabled then return end
            local ch = GetChar(); if not ch then return end
            for _,p in ipairs(ch:GetDescendants()) do if p:IsA("BasePart") then pcall(function() p.CanCollide=false end) end end
        end)
        notify("Noclip", "Ativado ✓")
    else
        if noclipConn then noclipConn:Disconnect(); noclipConn=nil end
        local ch = GetChar()
        if ch then for _,p in ipairs(ch:GetDescendants()) do if p:IsA("BasePart") then pcall(function() p.CanCollide=true end) end end end
        notify("Noclip", "Desativado ✕")
    end
end

local function ToggleAntiSit(s)
    antiSitEnabled = s
    if s then
        if antiSitConn then return end
        antiSitConn = RunService.Heartbeat:Connect(function()
            if not antiSitEnabled then return end
            local ch=GetChar(); if not ch then return end
            local h=ch:FindFirstChildOfClass("Humanoid")
            if h then if h.Sit then h.Sit=false end
                if h:GetState()==Enum.HumanoidStateType.Seated then h:ChangeState(Enum.HumanoidStateType.Running) end
            end
        end)
        notify("Anti-Sit", "Ativado ✓")
    else
        if antiSitConn then antiSitConn:Disconnect(); antiSitConn=nil end
        notify("Anti-Sit", "Desativado ✕")
    end
end

local function ToggleAntiAfk(s)
    antiAfkEnabled = s
    if s then
        if antiAfkThread then return end
        antiAfkThread = task.spawn(function()
            local t=false
            while antiAfkEnabled do
                task.wait(50)
                local ch=GetChar(); local h=ch and ch:FindFirstChildOfClass("Humanoid")
                if h then t=not t; pcall(function() h:Move(t and Vector3.new(0.1,0,0) or Vector3.new(-0.1,0,0),false); task.wait(0.1); h:Move(Vector3.new(),false) end) end
            end
        end)
        notify("Anti-AFK", "Ativado ✓")
    else
        if antiAfkThread then pcall(task.cancel, antiAfkThread); antiAfkThread=nil end
        notify("Anti-AFK", "Desativado ✕")
    end
end

-- Spinbot, Fake Dash, Fake Lag, BHOP
local function ToggleSpinbot(state)
    spinbotEnabled = state
    if state then
        if spinbotConnection then return end
        spinbotConnection = RunService.Heartbeat:Connect(function()
            if not spinbotEnabled then return end
            local hrp = LP.Character and LP.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                pcall(function() hrp.CFrame = hrp.CFrame * CFrame.Angles(0, math.rad(spinSpeed), 0) end)
            end
        end)
    else
        if spinbotConnection then spinbotConnection:Disconnect(); spinbotConnection = nil end
    end
end

local function startDashLoop()
    if dashLoop then return end
    dashLoop = RunService.Heartbeat:Connect(function()
        if fakeDashEnabled and tick() - LastDashTime > DashCooldown then
            local char = LP.Character
            if char and char:FindFirstChild("HumanoidRootPart") and char:FindFirstChild("Humanoid") then
                local hrp = char.HumanoidRootPart
                local hum = char.Humanoid
                if hum.MoveDirection.Magnitude > 0 then
                    local dir = hrp.CFrame.LookVector
                    local rayParams = RaycastParams.new()
                    rayParams.FilterDescendantsInstances = {char}
                    rayParams.FilterType = Enum.RaycastFilterType.Blacklist
                    local rayResult = workspace:Raycast(hrp.Position, dir * DashDistance, rayParams)
                    if not rayResult then
                        hrp.CFrame = hrp.CFrame + dir * DashDistance
                    end
                    LastDashTime = tick()
                end
            end
        end
    end)
end

local function startLagLoop()
    if lagLoop then return end
    lagLoop = RunService.Heartbeat:Connect(function()
        if fakeLagEnabled and tick() - LastLagTime > LagCooldown then
            local char = LP.Character
            if char and char:FindFirstChild("HumanoidRootPart") and char:FindFirstChild("Humanoid") then
                local hrp = char.HumanoidRootPart
                local hum = char.Humanoid
                if hum.MoveDirection.Magnitude > 0 then
                    forceIndex = (forceIndex % #LagDistanceVariacoes) + 1
                    local dist = LagDistanceVariacoes[forceIndex]
                    local direction = hrp.CFrame.LookVector
                    local rayParams = RaycastParams.new()
                    rayParams.FilterDescendantsInstances = {char}
                    rayParams.FilterType = Enum.RaycastFilterType.Blacklist
                    local rayResult = workspace:Raycast(hrp.Position, direction * dist, rayParams)
                    if not rayResult or (rayResult and not rayResult.Instance.CanCollide) then
                        hrp.CFrame = hrp.CFrame + direction * dist
                    end
                    LastLagTime = tick()
                end
            end
        end
    end)
end

local function toggleBhop(state)
    if state then
        if bhopConn then return end
        bhopConn = RunService.Heartbeat:Connect(function()
            local char = LP.Character
            if not char then return end
            local hum = char:FindFirstChildOfClass("Humanoid")
            if not hum then return end
            local mv = hum.MoveDirection
            local onGround = hum.FloorMaterial ~= Enum.Material.Air
            if onGround and mv.Magnitude > 0.2 then hum.Jump = true end
        end)
    else
        if bhopConn then bhopConn:Disconnect(); bhopConn = nil end
    end
end

-- Auto Farm Gari (caminhando + desvio)
local function findTrashEntries()
    local list = {}
    local lixeiro = workspace:FindFirstChild("Lixeiro")
    if not lixeiro then
        for _, v in ipairs(workspace:GetChildren()) do
            if string.find(string.lower(v.Name), "lixeiro") then lixeiro = v; break end
        end
    end
    if not lixeiro then return list end

    local function isPromptMatch(prompt)
        if not prompt then return false end
        local lname = string.lower(prompt.Name or "")
        local action = string.lower(prompt.ActionText or "")
        if lname:find("peg") or lname:find("lixo") or action:find("peg") or action:find("lixo") then
            return true
        end
        return false
    end

    local function inspectModel(model)
        for _, desc in ipairs(model:GetDescendants()) do
            if desc:IsA("ProximityPrompt") then
                if isPromptMatch(desc) then
                    table.insert(list, {target = model, prompt = desc})
                    return
                end
            end
        end
        for _, child in ipairs(model:GetDescendants()) do
            if child:IsA("RemoteEvent") or child:IsA("RemoteFunction") then
                local n = string.lower(child.Name or "")
                if n:find("peg") or n:find("lixo") or n:find("pega") then
                    table.insert(list, {target = model, remote = child})
                    return
                end
            end
        end
        local remote = model:FindFirstChild("PegarLixo") or model:FindFirstChild("pegarLixo") or model:FindFirstChild("pegaLixo")
        if remote and (remote:IsA("RemoteEvent") or remote:IsA("RemoteFunction")) then
            table.insert(list, {target = model, remote = remote})
            return
        end
        local folder = model:FindFirstChild("lixo automatico")
        if folder and folder:IsA("Folder") then
            for _, desc in ipairs(folder:GetDescendants()) do
                if desc:IsA("ProximityPrompt") and isPromptMatch(desc) then
                    table.insert(list, {target = model, prompt = desc})
                    return
                elseif (desc:IsA("RemoteEvent") or desc:IsA("RemoteFunction")) and string.find(string.lower(desc.Name or ""), "pegar") then
                    table.insert(list, {target = model, remote = desc})
                    return
                end
            end
        end
    end

    for _, child in ipairs(lixeiro:GetChildren()) do
        if child:IsA("Model") or child:IsA("Folder") or child:IsA("BasePart") then
            pcall(function() inspectModel(child) end)
        end
    end
    return list
end

local function getModelBasePart(model)
    if not model then return nil end
    if model:IsA("BasePart") then return model end
    if model.PrimaryPart and model.PrimaryPart:IsA("BasePart") then return model.PrimaryPart end
    for _, v in ipairs(model:GetDescendants()) do
        if v:IsA("BasePart") then return v end
    end
    return nil
end

local function walkToPosition(targetPos)
    local char = LP.Character
    if not char then return false end
    local humanoid = char:FindFirstChildOfClass("Humanoid")
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not humanoid or not hrp then return false end

    local DISTANCE_THRESHOLD = 4
    local CHECK_INTERVAL = 0.1
    local TIMEOUT = 12
    local STUCK_TIMEOUT = 2
    local SIDE_STEP_DISTANCE = 3

    local startTime = tick()
    local lastDist = (hrp.Position - targetPos).Magnitude
    local lastStuckCheck = startTime
    local stuckCount = 0

    humanoid.AutoRotate = true

    local function hasObstacle(direction)
        local rayOrigin = hrp.Position + Vector3.new(0, 1, 0)
        local rayDir = direction.Unit
        local raycastParams = RaycastParams.new()
        raycastParams.FilterType = Enum.RaycastFilterType.Blacklist
        raycastParams.FilterDescendantsInstances = {char}
        local result = workspace:Raycast(rayOrigin, rayDir * 3, raycastParams)
        return result ~= nil
    end

    local function getFreeDirection()
        local forward = (targetPos - hrp.Position).Unit
        local right = forward:Cross(Vector3.new(0, 1, 0)).Unit
        local left = -right

        if not hasObstacle(forward) then return forward end
        if not hasObstacle(right) then return right end
        if not hasObstacle(left) then return left end
        local diagRight = (forward + right).Unit
        if not hasObstacle(diagRight) then return diagRight end
        local diagLeft = (forward + left).Unit
        if not hasObstacle(diagLeft) then return diagLeft end
        return nil
    end

    while tick() - startTime < TIMEOUT do
        if not autoFarmGari then return false end
        char = LP.Character
        if not char then return false end
        humanoid = char:FindFirstChildOfClass("Humanoid")
        hrp = char:FindFirstChild("HumanoidRootPart")
        if not humanoid or not hrp then return false end

        local currentPos = hrp.Position
        local dist = (currentPos - targetPos).Magnitude
        if dist < DISTANCE_THRESHOLD then
            humanoid:MoveTo(hrp.Position)
            return true
        end

        if tick() - lastStuckCheck > STUCK_TIMEOUT then
            local newDist = (hrp.Position - targetPos).Magnitude
            if math.abs(newDist - lastDist) < 0.5 then
                stuckCount = stuckCount + 1
                if stuckCount >= 2 then
                    humanoid.Jump = true
                    local freeDir = getFreeDirection()
                    if freeDir then
                        local sidePos = hrp.Position + freeDir * SIDE_STEP_DISTANCE
                        humanoid:MoveTo(sidePos)
                    end
                    task.wait(0.3)
                    stuckCount = 0
                end
            else
                stuckCount = 0
            end
            lastDist = newDist
            lastStuckCheck = tick()
        end

        humanoid:MoveTo(targetPos)
        task.wait(CHECK_INTERVAL)
    end
    return false
end

local function attemptCollect(entry)
    if not entry then return false end

    if entry.prompt and entry.prompt:IsA("ProximityPrompt") then
        local prompt = entry.prompt
        local parentPart = prompt.Parent
        local targetPart = nil
        if parentPart and parentPart:IsA("BasePart") then
            targetPart = parentPart
        else
            local base = nil
            local p = prompt.Parent
            while p and p ~= workspace do
                if p:IsA("BasePart") then base = p; break end
                p = p.Parent
            end
            targetPart = base
        end
        
        if targetPart then
            local ok = walkToPosition(targetPart.Position)
            if not ok then return false end
        end
        
        task.wait(0.2)
        local hold = tonumber(prompt.HoldDuration) or 0
        for attempt = 1, 3 do
            local ok, err = pcall(function()
                if hold > 0 then
                    prompt:InputHoldBegin()
                    local waited = 0
                    local timeout = hold + 1.0
                    while prompt.Enabled and waited < timeout do
                        task.wait(0.1)
                        waited = waited + 0.1
                    end
                    prompt:InputHoldEnd()
                else
                    prompt:InputHoldBegin()
                    task.wait(0.08)
                    prompt:InputHoldEnd()
                end
            end)
            task.wait(0.25)
            if not prompt or not prompt.Parent or not prompt.Parent:IsDescendantOf(workspace) then
                return true
            end
            if ok then
                if entry.target and not entry.target:IsDescendantOf(workspace) then return true end
            end
            task.wait(0.15)
        end
        return false
    end

    if entry.remote then
        local rem = entry.remote
        local targetPart = getModelBasePart(entry.target)
        if targetPart then
            walkToPosition(targetPart.Position)
            task.wait(0.2)
        end
        local ok = false
        pcall(function()
            if rem:IsA("RemoteEvent") then
                pcall(function() rem:FireServer() end)
                pcall(function() rem:FireServer(entry.target) end)
                pcall(function() rem:FireServer(LP) end)
                pcall(function() rem:FireServer(entry.target, LP) end)
                ok = true
            elseif rem:IsA("RemoteFunction") then
                pcall(function() rem:InvokeServer() end)
                pcall(function() rem:InvokeServer(entry.target) end)
                pcall(function() rem:InvokeServer(LP) end)
                ok = true
            end
        end)
        task.wait(0.35)
        if entry.target and not entry.target:IsDescendantOf(workspace) then return true end
        return ok
    end

    if entry.target then
        local cd = nil
        for _, d in ipairs(entry.target:GetDescendants()) do
            if d:IsA("ClickDetector") and (string.find(string.lower(d.Name or ""), "pegar") or string.find(string.lower(d.Name or ""), "lixo")) then
                cd = d
                break
            end
        end
        if cd then
            local targetPart = getModelBasePart(entry.target)
            if targetPart then
                walkToPosition(targetPart.Position)
                task.wait(0.2)
            end
            pcall(function() cd:MouseClick(LP) end)
            task.wait(0.3)
            if entry.target and not entry.target:IsDescendantOf(workspace) then return true end
            return true
        end
    end
    return false
end

local function farmLoop()
    if not autoFarmGari then return end
    task.spawn(function()
        while autoFarmGari do
            local entries = findTrashEntries()
            if #entries == 0 then
                notify("Auto Farm", "Nenhum lixo encontrado...", 2)
                task.wait(5)
            else
                for i, entry in ipairs(entries) do
                    if not autoFarmGari then break end
                    if entry.target and not entry.target:IsDescendantOf(workspace) then
                        continue
                    end
                    local success = false
                    for attempt = 1, 3 do
                        if not autoFarmGari then break end
                        local ok = false
                        pcall(function() ok = attemptCollect(entry) end)
                        if ok then
                            success = true
                            break
                        end
                        task.wait(0.5)
                    end
                    if autoFarmGari then
                        notify("Auto Farm", "Aguardando " .. tostring(TELEPORT_DELAY_SECONDS) .. "s...", 1.4)
                        local waited = 0
                        while waited < TELEPORT_DELAY_SECONDS and autoFarmGari do
                            task.wait(0.5)
                            waited = waited + 0.5
                        end
                    end
                end
            end
            task.wait(0.6)
        end
    end)
end

local function toggleAutoFarm(state)
    autoFarmGari = state
    if state then
        notify("Auto Farm", "Auto Farm Gari ATIVADO! (caminhando + desvio)", 2)
        farmLoop()
    else
        notify("Auto Farm", "Auto Farm Gari DESATIVADO!", 2)
    end
end

-- Nitro automático
local function getCurrentVehicleSeat()
    local char = LP.Character
    if not char or not char:FindFirstChild("Humanoid") then return nil end
    local humanoid = char.Humanoid
    if humanoid.SeatPart and humanoid.SeatPart:IsA("VehicleSeat") then
        return humanoid.SeatPart
    end
    return nil
end

local function getCarFromSeat(seat)
    if not seat then return nil end
    local car = seat.Parent
    while car and car ~= workspace do
        local seats, parts = 0, 0
        for _, child in pairs(car:GetChildren()) do
            if child:IsA("VehicleSeat") or child:IsA("Seat") then seats = seats + 1 end
            if child:IsA("BasePart") then parts = parts + 1 end
        end
        if seats >= 1 and parts >= 3 then return car end
        car = car.Parent
    end
    return seat.Parent
end

local function nitroMethod1(seat)
    local car = getCarFromSeat(seat)
    if not car then return false end
    local success = pcall(function()
        local mainPart, maxSize = nil, 0
        for _, part in pairs(car:GetChildren()) do
            if part:IsA("BasePart") and part ~= seat then
                local size = part.Size.Magnitude
                if size > maxSize then maxSize = size; mainPart = part end
            end
        end
        if not mainPart then mainPart = seat end
        for _, obj in pairs(mainPart:GetChildren()) do if obj:IsA("BodyVelocity") then obj:Destroy() end end
        local bv = Instance.new("BodyVelocity")
        bv.MaxForce = Vector3.new(4000, 0, 4000)
        bv.Velocity = mainPart.CFrame.LookVector * nitroForce
        bv.Parent = mainPart
        Debris:AddItem(bv, 0.5)
    end)
    return success
end

local function nitroMethod2(seat)
    local car = getCarFromSeat(seat)
    if not car then return false end
    local success = pcall(function()
        for _, part in pairs(car:GetChildren()) do
            if part:IsA("BasePart") then
                local currentVel = part.AssemblyLinearVelocity
                part.AssemblyLinearVelocity = currentVel + (part.CFrame.LookVector * nitroForce)
            end
        end
    end)
    return success
end

local function nitroMethod3(seat)
    local success = pcall(function()
        local bv = Instance.new("BodyVelocity")
        bv.MaxForce = Vector3.new(math.huge, 0, math.huge)
        bv.Velocity = seat.CFrame.LookVector * nitroForce
        bv.Parent = seat
        Debris:AddItem(bv, 0.3)
    end)
    return success
end

local function activateNitro()
    local seat = getCurrentVehicleSeat()
    if not seat then notify("Nitro", "Entre em um veículo primeiro!", 3); return end
    nitroMethod1(seat); task.wait(0.1); nitroMethod2(seat); task.wait(0.1); nitroMethod3(seat)
    notify("Nitro", "Nitro ativado!", 2)
end

local function toggleNitro(state)
    nitroAuto = state
    if state then
        if nitroConnection then
            if IS_MOBILE and nitroConnection:IsA("TextButton") then nitroConnection:Destroy() else pcall(function() nitroConnection:Disconnect() end) end
        end
        if IS_MOBILE then
            if nitroButtonMobile then nitroButtonMobile:Destroy() end
            nitroButtonMobile = Instance.new("TextButton")
            nitroButtonMobile.Name = "NitroButtonMobile"
            nitroButtonMobile.Parent = hub.Gui
            nitroButtonMobile.Size = UDim2.new(0, 80, 0, 80)
            nitroButtonMobile.Position = UDim2.new(1, -100, 1, -100)
            nitroButtonMobile.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
            nitroButtonMobile.Text = "NITRO"
            nitroButtonMobile.TextColor3 = Color3.fromRGB(255, 255, 255)
            nitroButtonMobile.TextSize = 16
            nitroButtonMobile.ZIndex = 1000
            Instance.new("UICorner", nitroButtonMobile).CornerRadius = UDim.new(0, 20)
            nitroButtonMobile.MouseButton1Click:Connect(function() activateNitro() end)
            nitroConnection = nitroButtonMobile
        else
            nitroConnection = UserInputService.InputBegan:Connect(function(input, gameProcessed)
                if gameProcessed then return end
                if input.KeyCode == Enum.KeyCode.N then activateNitro() end
            end)
        end
        notify("Nitro", "Nitro automático ativado! (Tecla N)", 3)
    else
        if nitroConnection then
            if IS_MOBILE and nitroConnection:IsA("TextButton") then nitroConnection:Destroy() else pcall(function() nitroConnection:Disconnect() end) end
            nitroConnection = nil
        end
        if nitroButtonMobile then nitroButtonMobile:Destroy(); nitroButtonMobile = nil end
        notify("Nitro", "Nitro automático desativado!", 3)
    end
end

-- Speed Car / Fly Car (loop no final)
RunService.RenderStepped:Connect(function()
    local ch = LP.Character
    if ch and ch:FindFirstChild("Humanoid") and ch.Humanoid.SeatPart then
        local seatPart = ch.Humanoid.SeatPart
        if vFlyEnabled and UserInputService:IsKeyDown(Enum.KeyCode.F) then
            local direction = Vector3.new(0,0,0)
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then direction = direction + Camera.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then direction = direction - Camera.CFrame.LookVector end
            seatPart.AssemblyLinearVelocity = direction * 400
        elseif speedCarEnabled then
            if UserInputService:IsKeyDown(Enum.KeyCode.Q) then
                carVelocity = 5000
            elseif seatPart.Throttle ~= 0 then
                carVelocity = math.clamp(carVelocity + (seatPart.Throttle * carSensitivity), -5000, 5000)
            else
                carVelocity = carVelocity * 0.98
            end
            seatPart.AssemblyLinearVelocity = (seatPart.CFrame.LookVector * carVelocity) + Vector3.new(0, -20, 0)
        end
    end
end)

-- Bypass (ocultar menu)
local function enableBypass()
    if bypassEnabled then return end
    bypassEnabled = true
    prevMenuVisible = hub.Main.Visible
    prevMaximizeVisible = (hub.Main:FindFirstChild("TopBar") and hub.Main.TopBar:FindFirstChild("Maximize")) and hub.Main.TopBar.Maximize.Visible or true
    previousParent = hub.Main.Parent
    hub.Main.Parent = nil
    if hub.OpenBtn then hub.OpenBtn.Visible = false end
    hub:Notify("Bypass","Menu oculto. Clique 5x ou Shift 5x para restaurar.",3)
end

local function disableBypass()
    if not bypassEnabled then return end
    bypassEnabled = false
    if previousParent then hub.Main.Parent = previousParent; previousParent = nil end
    hub.Main.Visible = prevMenuVisible
    if hub.Main:FindFirstChild("TopBar") and hub.Main.TopBar:FindFirstChild("Maximize") then
        hub.Main.TopBar.Maximize.Visible = prevMaximizeVisible
    end
    hub:Notify("Bypass","Menu restaurado.",2)
end

UserInputService.InputBegan:Connect(function(input, processed)
    if not bypassEnabled then return end
    local focus = UserInputService:GetFocusedTextBox()
    if focus and focus ~= "" then return end
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        table.insert(clickTimes, tick())
        while clickTimes[1] and tick() - clickTimes[1] > 1.2 do table.remove(clickTimes, 1) end
        if #clickTimes >= 5 then clickTimes = {}; disableBypass() end
    end
    if input.UserInputType == Enum.UserInputType.Keyboard then
        local kc = input.KeyCode
        if kc == Enum.KeyCode.LeftShift or kc == Enum.KeyCode.RightShift then
            table.insert(shiftTimes, tick())
            while shiftTimes[1] and tick() - shiftTimes[1] > 1.2 do table.remove(shiftTimes, 1) end
            if #shiftTimes >= 5 then shiftTimes = {}; disableBypass() end
        end
    end
end)

-- Copy clothes
local function copyClothes()
    if not selectedCopyTarget then hub:Notify("Copiar Roupas","Nenhum jogador selecionado.",2.5) return end
    local targetChar = selectedCopyTarget.Character
    if not targetChar then hub:Notify("Copiar Roupas","Jogador sem character.",2.5) return end
    local myChar = LP.Character
    if not myChar then hub:Notify("Copiar Roupas","Seu character não está pronto.",2.5) return end
    local applied = false
    pcall(function()
        for _, v in ipairs(myChar:GetChildren()) do if v:IsA("Shirt") or v:IsA("Pants") then v:Destroy() end end
        local shirt = targetChar:FindFirstChildOfClass("Shirt") or targetChar:FindFirstChild("Shirt")
        local pants = targetChar:FindFirstChildOfClass("Pants") or targetChar:FindFirstChild("Pants")
        if shirt and shirt.ShirtTemplate and shirt.ShirtTemplate ~= "" then
            local ns = Instance.new("Shirt"); ns.ShirtTemplate = shirt.ShirtTemplate; ns.Parent = myChar; applied = true
        end
        if pants and pants.PantsTemplate and pants.PantsTemplate ~= "" then
            local np = Instance.new("Pants"); np.PantsTemplate = pants.PantsTemplate; np.Parent = myChar; applied = true
        end
        if not applied and selectedCopyTarget.UserId then
            local ok, desc = pcall(function() return Players:GetHumanoidDescriptionFromUserId(selectedCopyTarget.UserId) end)
            if ok and desc then
                local hum = myChar:FindFirstChildOfClass("Humanoid")
                if hum then hum:ApplyDescription(desc); applied = true end
            end
        end
    end)
    if applied then hub:Notify("Copiar Roupas","Roupas copiadas!",2.5) else hub:Notify("Copiar Roupas","Falha ao copiar.",2.5) end
end

----------------------------------------------------------------
--                     CONSTRUÇÃO DAS ABAS (UI)
----------------------------------------------------------------
local hub = setmetatable({}, Onyx):Init()
hub.NotificationsEnabled = true

-- Aba Home
local home = hub:AddTab("Home", nil)
do
    local c1 = home:AddCard("Active Features")
    c1:AddInfo("Features", "Aimbot • ESP • Movement • Head Size • Auto Farm • Nitro")
    c1:AddInfo("Status", "Carregado ✓")
    local c2 = home:AddCard("Player Info")
    c2:AddInfo("Account Age", LP.AccountAge .. " days")
    c2:AddInfo("User ID", tostring(LP.UserId))
    c2:AddInfo("Username", LP.Name)
    local c3 = home:AddCard("System Status")
    hub.FPSLabel    = c3:AddInfo("FPS", "—")
    hub.PingLabel   = c3:AddInfo("Ping", "—")
    hub.PlayersLabel= c3:AddInfo("Players Online", "—")
    local s = c3:AddInfo("Status", "Undetected ✓")
    s.TextColor3 = THEME.Success
end

-- Aba Aimbots
local aimTab = hub:AddTab("Aimbots", nil)
do
    local c = aimTab:AddCard("Aimbot Geral")
    c:AddToggle("Aimbot (PC/Mobile)", false, function(s)
        aimbotEnabled = s
        updateFOVVisual()
        if s and not IS_MOBILE and not holdToAimEnabled and not aimbotHotkeyEnabled then
            -- já tem loop infinito
        end
    end)
    c:AddSlider("FOV", 50, 400, FOVRadius, function(v) FOVRadius = v; updateFOVVisual() end)
    c:AddToggle("FOV Visível", true, function(s) fovVisible = s; updateFOVVisual() end)
    c:AddToggle("Kill Check", true, function(s) killCheckEnabled = s end)
    c:AddToggle("Wall Check", true, function(s) wallCheckEnabled = s end)
    c:AddSlider("Head Offset", 0, 5, HeadOffset, function(v) HeadOffset = v end)
    c:AddToggle("Smooth Aimbot", false, function(s) aimbotSmoothEnabled = s end)
    c:AddSlider("Smooth Speed (x100)", 5, 50, math.floor(smoothSpeed*100), function(v) smoothSpeed = v/100 end)

    local mobileCard = aimTab:AddCard("Mobile Aimbot")
    mobileCard:AddToggle("Aimbot Mobile (segurar tela)", false, function(s)
        aimbotMobileEnabled = s
        if s then
            if mobileAutoAimConn then mobileAutoAimConn:Disconnect() end
            mobileAutoAimConn = SetupMobileAutoAim()
        else
            if mobileAutoAimConn then mobileAutoAimConn:Disconnect(); mobileAutoAimConn = nil end
        end
    end)
    mobileCard:AddToggle("Aimbot Mobile (olhar direção)", false, function(s)
        aimbotMobileLookEnabled = s
        if s then
            if mobileLookConn then mobileLookConn:Disconnect() end
            mobileLookConn = SetupMobileLookAim()
        else
            if mobileLookConn then mobileLookConn:Disconnect(); mobileLookConn = nil end
        end
    end)

    local hotkeyCard = aimTab:AddCard("Aimbot Hotkey")
    hotkeyCard:AddToggle("Aimbot Hotkey (segurar)", false, function(s)
        aimbotHotkeyEnabled = s
        if not s then hotkeyAimbotActive = false end
    end)
    local function fmtHotkey(h)
        if not h or not h.Key then return "?" end
        return tostring(h.Key.Name or h.Key)
    end
    local hotkeyBtn = hotkeyCard:AddKeybind("Tecla", Enum.KeyCode.E, function(key)
        hotkeyBind = {Kind="KeyCode", Key=key}
    end)

    local bodyCard = aimTab:AddCard("Body Shot Progressivo")
    bodyCard:AddToggle("Ativar", false, function(s)
        bodyShotModeEnabled = s
        if not s then for _, p in ipairs(Players:GetPlayers()) do shotCounts[p] = 0 end end
    end)
    bodyCard:AddSlider("Tiros no peito antes de focar cabeça", 1, 10, bodyShotThreshold, function(v) bodyShotThreshold = v end)

    local rapidCard = aimTab:AddCard("RapidFire")
    rapidCard:AddToggle("RapidFire (segurar mouse/touch)", false, function(s) rapidFireEnabled = s end)
    rapidCard:AddSlider("Delay (ms)", 10, 200, rapidFireDelay*1000, function(v) rapidFireDelay = v/1000 end)

    local holdCard = aimTab:AddCard("Hold to Aim")
    holdCard:AddToggle("Ativar", false, function(s) holdToAimEnabled = s end)
    holdCard:AddKeybind("Tecla (PC)", Enum.KeyCode.E, function(key) aimKey = key end)
    if IS_MOBILE then
        holdCard:AddButton("Botão mobile (Toque aqui)", function()
            mobileAimDown = true
            task.wait(0.1)
            mobileAimDown = false
            hub:Notify("Mobile Aim","Toque para simular pressão",1.5)
        end)
    end
end

-- Aba Visual (ESP)
local visTab = hub:AddTab("Visual", nil)
do
    local draw = visTab:AddCard("Drawing ESP")
    draw:AddToggle("ESP Master", false, function(s)
        espEnabled = s
        if drawingSupported and espEnabled then
            for _, plr in ipairs(Players:GetPlayers()) do if plr ~= LP then createESPObjectsForPlayer(plr) end end
        else
            for plr,_ in pairs(ESPS) do removeESPObjects(plr) end
        end
    end)
    draw:AddToggle("ESP Box 2D", false, function(s) espBoxEnabled = s end)
    draw:AddToggle("ESP Line (chão)", false, function(s) espLineEnabled = s end)
    draw:AddToggle("ESP Skeleton", false, function(s) espSkeletonEnabled = s end)
    draw:AddToggle("ESP Name", false, function(s) espNameEnabled = s end)
    draw:AddToggle("ESP Studs (distância)", false, function(s) espStudsEnabled = s end)
    draw:AddToggle("ESP Inventory", false, function(s) espInventoryEnabled = s end)
    draw:AddToggle("ESP Tracer (pé)", false, function(s) espTracerEnabled = s end)
    draw:AddToggle("Tracers (topo)", false, function(s) tracersEnabled = s end)
    draw:AddToggle("Distance", false, function(s) distanceEnabled = s end)
    draw:AddToggle("Rainbow", false, function(s) rainbowESPEnabled = s; ESPRainbowActive = s end)
    draw:AddToggle("Box 3D", false, function(s) box3dEnabled = s end)
    draw:AddTextbox("Cor (R,G,B)", "64,156,255", function(txt)
        local r,g,b = txt:match("(%d+)%s*,%s*(%d+)%s*,%s*(%d+)")
        if r and g and b then espColor = Color3.fromRGB(tonumber(r), tonumber(g), tonumber(b)) end
    end)

    local bill = visTab:AddCard("Billboard ESP (sem Highlight)")
    bill:AddToggle("Ativar", false, function(s)
        billboardEnabled = s
        if s then
            for _, plr in ipairs(Players:GetPlayers()) do if plr ~= LP and plr.Character then createBillboardForCharacter(plr.Character, plr) end end
        else
            destroyAllBillboards()
        end
    end)
end

-- Aba Movement
local movTab = hub:AddTab("Movement", nil)
do
    local spin = movTab:AddCard("Spinbot")
    spin:AddToggle("Spinbot", false, ToggleSpinbot)
    spin:AddSlider("Velocidade (deg/frame)", 1, 100, spinSpeed, function(v) spinSpeed = v end)

    local dash = movTab:AddCard("Fake Dash")
    dash:AddToggle("Ativar", false, function(s)
        fakeDashEnabled = s
        if s then startDashLoop() elseif dashLoop then dashLoop:Disconnect(); dashLoop = nil end
    end)
    dash:AddSlider("Distância (m)", 1, 30, DashDistance, function(v) DashDistance = v end)
    dash:AddSlider("Cooldown (ms)", 50, 1000, DashCooldown*1000, function(v) DashCooldown = v/1000 end)

    local lag = movTab:AddCard("Fake Lag")
    lag:AddToggle("Ativar", false, function(s)
        fakeLagEnabled = s
        if s then startLagLoop() elseif lagLoop then lagLoop:Disconnect(); lagLoop = nil end
    end)
    lag:AddSlider("Distância base", 5, 50, LagDistance, function(v) LagDistance = v end)
    lag:AddSlider("Cooldown (ms)", 100, 2000, LagCooldown*1000, function(v) LagCooldown = v/1000 end)
    lag:AddTextbox("Variações (separadas por vírgula)", "12,15,18", function(txt)
        local arr = {}
        for num in txt:gmatch("(%d+)") do table.insert(arr, tonumber(num)) end
        if #arr > 0 then LagDistanceVariacoes = arr end
    end)

    local bhopCard = movTab:AddCard("BHOP")
    bhopCard:AddToggle("BHOP", false, toggleBhop)
end

-- Aba Misc
local miscTab = hub:AddTab("Misc", nil)
do
    -- Freeze
    miscTab:AddCard("Freeze"):AddToggle("Congelar Players", false, function(s) freezePlayer = s end)

    -- Hitbox 100hs
    local hb = miscTab:AddCard("Hitbox 100hs")
    hb:AddToggle("Ativar", false, function(s)
        hitboxExpanded = s
        if not s then
            for _, player in pairs(Players:GetPlayers()) do
                if player ~= LP and player.Character then
                    local part = player.Character:FindFirstChild("UpperTorso")
                    if part and hitboxOriginais[part] then
                        local o = hitboxOriginais[part]
                        pcall(function()
                            part.Size = o.Size; part.Transparency = o.Transparency; part.Material = o.Material
                            part.Color = o.Color; part.CanCollide = o.CanCollide; part.Massless = o.Massless
                        end)
                        hitboxOriginais[part] = nil
                    end
                end
            end
        end
    end)
    hb:AddSlider("Tamanho", 5, 50, hitboxSize, function(v) hitboxSize = v end)
    hb:AddSlider("Transparência (%)", 0, 100, hitboxTransparency*100, function(v) hitboxTransparency = v/100 end)

    -- Hitbox LEGIT
    local legit = miscTab:AddCard("Hitbox LEGIT")
    legit:AddToggle("Ativar", false, function(s)
        legitAtivo = s
        if not s then
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LP and plr.Character then
                    local torso = plr.Character:FindFirstChild("UpperTorso")
                    if torso and originaisLegit[torso] then
                        pcall(function()
                            torso.Size = originaisLegit[torso].Size
                            torso.Transparency = originaisLegit[torso].Transparency
                        end)
                        if visualPartsLegit[torso] then pcall(function() visualPartsLegit[torso]:Destroy() end) end
                        originaisLegit[torso] = nil
                    end
                end
            end
        end
    end)
    legit:AddSlider("Tamanho LEGIT", 4, 15, legitTam, function(v) legitTam = v end)

    -- Áudio
    local audio = miscTab:AddCard("Áudio")
    audio:AddToggle("Melhorar Áudio (reduz tiros, aumenta passos)", false, function(s) audioEnhancerEnabled = s end)
    audio:AddToggle("Hit Sound", false, function(s) if s then audioEnhancerEnabled = true end end)

    -- Head Size
    local headCard = miscTab:AddCard("Head Size (próprio personagem)")
    headCard:AddToggle("Ativar Head Size", false, function(s)
        headSizeEnabled = s
        if s then
            startHeadSizeLoop()
            applyHeadSize()
        else
            local char = LP.Character
            if char and char:FindFirstChild("Head") then
                pcall(function()
                    char.Head.Size = Vector3.new(1, 1, 1)
                    char.Head.Transparency = 0
                end)
            end
        end
    end)
    headCard:AddSlider("Tamanho da cabeça", 1, 5, headSizeValue, function(v)
        headSizeValue = v
        if headSizeEnabled then applyHeadSize() end
    end)
    headCard:AddSlider("Transparência da cabeça (%)", 0, 100, headTransparency*100, function(v)
        headTransparency = v / 100
        if headSizeEnabled then applyHeadSize() end
    end)

    -- Noclip, Anti-Sit, Anti-AFK
    local miscExtra = miscTab:AddCard("Utilidades")
    miscExtra:AddToggle("Noclip", false, ToggleNoclip)
    miscExtra:AddToggle("Anti-Sit", false, ToggleAntiSit)
    miscExtra:AddToggle("Anti-AFK", false, ToggleAntiAfk)
end

-- Aba Auto Farm
local farmTab = hub:AddTab("Auto Farm", nil)
do
    local c = farmTab:AddCard("Coleta de Lixo (Gari)")
    c:AddToggle("Auto Farm Gari (caminhando + desvio)", false, toggleAutoFarm)
    c:AddSlider("Delay entre coletas (s)", 2, 30, TELEPORT_DELAY_SECONDS, function(v)
        TELEPORT_DELAY_SECONDS = v
        notify("Auto Farm", "Delay ajustado para "..v.." segundos", 2)
    end)
    local info = farmTab:AddCard("Info")
    info:AddInfo("Funcionamento", "O personagem anda até o lixo e desvia de obstáculos automaticamente.")
end

-- Aba Veículos
local vehTab = hub:AddTab("Veículos", nil)
do
    local vCard = vehTab:AddCard("Controles")
    vCard:AddToggle("Speed Car (W/S/Q)", false, function(s) speedCarEnabled = s; if not s then carVelocity = 0 end end)
    vCard:AddToggle("Fly Car (Tecla F)", false, function(s) vFlyEnabled = s end)
    vCard:AddSlider("Sensibilidade Carro", 0.5, 10, carSensitivity, function(val) carSensitivity=val end)
    vCard:AddToggle("Nitro Automático (Tecla N / Botão Mobile)", false, toggleNitro)
    vCard:AddSlider("Força do Nitro", 20, 150, nitroForce, function(val) nitroForce = val; notify("Nitro", "Força ajustada para: "..val, 1.6) end)
end

-- Aba Config (Amigos, Tema, Save/Load, Unload)
local cfgTab = hub:AddTab("Config", nil)
do
    local friendCard = cfgTab:AddCard("Amigos (não mirar)")
    local listFrame = nil
    local function rebuildFriendEntries()
        if not listFrame then return end
        for _,c in ipairs(listFrame:GetChildren()) do if not c:IsA("UIListLayout") then c:Destroy() end end
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LP then
                local row = Instance.new("Frame")
                row.Size = UDim2.new(1, -12, 0, 28)
                row.BackgroundColor3 = THEME.SurfaceAlt
                row.Parent = listFrame
                corner(row, CORNER_SM)
                local lbl = Instance.new("TextLabel", row)
                lbl.Size = UDim2.new(0.65, 0, 1, 0)
                lbl.Position = UDim2.new(0, 8, 0, 0)
                lbl.BackgroundTransparency = 1
                lbl.Font = Enum.Font.Gotham
                lbl.Text = p.Name
                lbl.TextColor3 = THEME.Text
                lbl.TextSize = 14
                lbl.TextXAlignment = Enum.TextXAlignment.Left
                local btn = Instance.new("TextButton", row)
                btn.Size = UDim2.new(0, 80, 0, 20)
                btn.Position = UDim2.new(1, -92, 0.5, -10)
                btn.BackgroundColor3 = (friendSet[p.UserId] and THEME.Accent) or Color3.fromRGB(48,42,58)
                btn.Font = Enum.Font.GothamBold
                btn.Text = (friendSet[p.UserId] and "Amigo") or "Marcar"
                btn.TextColor3 = Color3.fromRGB(255,255,255)
                btn.AutoButtonColor = true
                corner(btn, CORNER_SM)
                btn.MouseButton1Click:Connect(function()
                    friendSet[p.UserId] = not friendSet[p.UserId] and true or nil
                    btn.BackgroundColor3 = (friendSet[p.UserId] and THEME.Accent) or Color3.fromRGB(48,42,58)
                    btn.Text = (friendSet[p.UserId] and "Amigo") or "Marcar"
                end)
            end
        end
    end
    local scroll = Instance.new("ScrollingFrame")
    scroll.Size = UDim2.new(1, -12, 0, 200)
    scroll.BackgroundTransparency = 1
    scroll.ScrollBarThickness = 6
    scroll.Active = true
    scroll.ScrollingEnabled = true
    scroll.Parent = friendCard.Frame
    listFrame = scroll
    local layout = Instance.new("UIListLayout", scroll)
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.Padding = UDim.new(0, 6)
    rebuildFriendEntries()
    Players.PlayerAdded:Connect(rebuildFriendEntries)
    Players.PlayerRemoving:Connect(rebuildFriendEntries)
    friendCard:AddButton("Limpar Amigos", function()
        friendSet = {}
        rebuildFriendEntries()
    end)

    -- Temas
    local themeCard = cfgTab:AddCard("Tema do Menu")
    local themeNames = {"CLS Pink","Midnight","Lurs Style","Onyx Blue","Emerald","Sunset"}
    themeCard:AddDropdown("Escolher tema", themeNames, function(v)
        hub:ApplyTheme(v)
        hub:Notify("Tema", "Aplicado: "..v, 2)
    end)

    -- Save/Load
    local saveCard = cfgTab:AddCard("Salvar / Carregar")
    local CONFIG_FILE = "FluxoVip_Config.json"
    local function hasFS() return type(writefile)=="function" and type(readfile)=="function" and type(isfile)=="function" end
    local function snapshotConfig()
        local data = {
            aimbotEnabled = aimbotEnabled,
            FOVRadius = FOVRadius,
            fovVisible = fovVisible,
            killCheckEnabled = killCheckEnabled,
            wallCheckEnabled = wallCheckEnabled,
            HeadOffset = HeadOffset,
            bodyShotModeEnabled = bodyShotModeEnabled,
            bodyShotThreshold = bodyShotThreshold,
            rapidFireEnabled = rapidFireEnabled,
            rapidFireDelay = rapidFireDelay,
            holdToAimEnabled = holdToAimEnabled,
            aimKey = aimKey.Name,
            aimbotSmoothEnabled = aimbotSmoothEnabled,
            smoothSpeed = smoothSpeed,
            aimbotMobileEnabled = aimbotMobileEnabled,
            aimbotMobileLookEnabled = aimbotMobileLookEnabled,
            aimbotHotkeyEnabled = aimbotHotkeyEnabled,
            hotkeyBind = hotkeyBind,
            espEnabled = espEnabled,
            espBoxEnabled = espBoxEnabled,
            espLineEnabled = espLineEnabled,
            espSkeletonEnabled = espSkeletonEnabled,
            espNameEnabled = espNameEnabled,
            espStudsEnabled = espStudsEnabled,
            espInventoryEnabled = espInventoryEnabled,
            espTracerEnabled = espTracerEnabled,
            tracersEnabled = tracersEnabled,
            distanceEnabled = distanceEnabled,
            rainbowESPEnabled = rainbowESPEnabled,
            box3dEnabled = box3dEnabled,
            espColor = {espColor.R, espColor.G, espColor.B},
            billboardEnabled = billboardEnabled,
            spinbotEnabled = spinbotEnabled,
            spinSpeed = spinSpeed,
            fakeDashEnabled = fakeDashEnabled,
            DashDistance = DashDistance,
            DashCooldown = DashCooldown,
            fakeLagEnabled = fakeLagEnabled,
            LagDistance = LagDistance,
            LagCooldown = LagCooldown,
            LagDistanceVariacoes = LagDistanceVariacoes,
            freezePlayer = freezePlayer,
            hitboxExpanded = hitboxExpanded,
            hitboxSize = hitboxSize,
            hitboxTransparency = hitboxTransparency,
            legitAtivo = legitAtivo,
            legitTam = legitTam,
            audioEnhancerEnabled = audioEnhancerEnabled,
            noclipEnabled = noclipEnabled,
            antiSitEnabled = antiSitEnabled,
            antiAfkEnabled = antiAfkEnabled,
            autoFarmGari = autoFarmGari,
            TELEPORT_DELAY_SECONDS = TELEPORT_DELAY_SECONDS,
            nitroAuto = nitroAuto,
            nitroForce = nitroForce,
            speedCarEnabled = speedCarEnabled,
            vFlyEnabled = vFlyEnabled,
            carSensitivity = carSensitivity,
            headSizeEnabled = headSizeEnabled,
            headSizeValue = headSizeValue,
            headTransparency = headTransparency,
            friendSet = {},
            NotificationsEnabled = hub.NotificationsEnabled,
        }
        for uid,_ in pairs(friendSet) do table.insert(data.friendSet, uid) end
        return data
    end
    local function applyConfig(data)
        if type(data) ~= "table" then return end
        aimbotEnabled = data.aimbotEnabled or false
        FOVRadius = data.FOVRadius or 200
        fovVisible = data.fovVisible == nil and true or data.fovVisible
        killCheckEnabled = data.killCheckEnabled == nil and true or data.killCheckEnabled
        wallCheckEnabled = data.wallCheckEnabled == nil and true or data.wallCheckEnabled
        HeadOffset = data.HeadOffset or 1
        bodyShotModeEnabled = data.bodyShotModeEnabled or false
        bodyShotThreshold = data.bodyShotThreshold or 2
        rapidFireEnabled = data.rapidFireEnabled or false
        rapidFireDelay = data.rapidFireDelay or 0.06
        holdToAimEnabled = data.holdToAimEnabled or false
        if data.aimKey then aimKey = Enum.KeyCode[data.aimKey] or Enum.KeyCode.E end
        aimbotSmoothEnabled = data.aimbotSmoothEnabled or false
        smoothSpeed = data.smoothSpeed or 0.15
        aimbotMobileEnabled = data.aimbotMobileEnabled or false
        aimbotMobileLookEnabled = data.aimbotMobileLookEnabled or false
        aimbotHotkeyEnabled = data.aimbotHotkeyEnabled or false
        if data.hotkeyBind then hotkeyBind = data.hotkeyBind end
        espEnabled = data.espEnabled or false
        espBoxEnabled = data.espBoxEnabled or false
        espLineEnabled = data.espLineEnabled or false
        espSkeletonEnabled = data.espSkeletonEnabled or false
        espNameEnabled = data.espNameEnabled or false
        espStudsEnabled = data.espStudsEnabled or false
        espInventoryEnabled = data.espInventoryEnabled or false
        espTracerEnabled = data.espTracerEnabled or false
        tracersEnabled = data.tracersEnabled or false
        distanceEnabled = data.distanceEnabled or false
        rainbowESPEnabled = data.rainbowESPEnabled or false
        box3dEnabled = data.box3dEnabled or false
        if data.espColor then espColor = Color3.new(data.espColor[1], data.espColor[2], data.espColor[3]) end
        billboardEnabled = data.billboardEnabled or false
        spinbotEnabled = data.spinbotEnabled or false; ToggleSpinbot(spinbotEnabled)
        spinSpeed = data.spinSpeed or 50
        fakeDashEnabled = data.fakeDashEnabled or false
        DashDistance = data.DashDistance or 8
        DashCooldown = data.DashCooldown or 0.25
        fakeLagEnabled = data.fakeLagEnabled or false
        LagDistance = data.LagDistance or 15
        LagCooldown = data.LagCooldown or 1.0
        if data.LagDistanceVariacoes then LagDistanceVariacoes = data.LagDistanceVariacoes end
        freezePlayer = data.freezePlayer or false
        hitboxExpanded = data.hitboxExpanded or false
        hitboxSize = data.hitboxSize or 15
        hitboxTransparency = data.hitboxTransparency or 0.7
        legitAtivo = data.legitAtivo or false
        legitTam = data.legitTam or 6
        audioEnhancerEnabled = data.audioEnhancerEnabled or false
        noclipEnabled = data.noclipEnabled or false
        antiSitEnabled = data.antiSitEnabled or false
        antiAfkEnabled = data.antiAfkEnabled or false
        autoFarmGari = data.autoFarmGari or false
        TELEPORT_DELAY_SECONDS = data.TELEPORT_DELAY_SECONDS or 10
        nitroAuto = data.nitroAuto or false
        nitroForce = data.nitroForce or 50
        speedCarEnabled = data.speedCarEnabled or false
        vFlyEnabled = data.vFlyEnabled or false
        carSensitivity = data.carSensitivity or 2
        headSizeEnabled = data.headSizeEnabled or false
        headSizeValue = data.headSizeValue or 1
        headTransparency = data.headTransparency or 0
        if data.friendSet then
            friendSet = {}
            for _,uid in ipairs(data.friendSet) do friendSet[tonumber(uid)] = true end
        end
        if data.NotificationsEnabled ~= nil then hub.NotificationsEnabled = data.NotificationsEnabled end
        -- Aplicar toggles que precisam de loops
        if headSizeEnabled then startHeadSizeLoop(); applyHeadSize() else
            local char = LP.Character
            if char and char:FindFirstChild("Head") then
                pcall(function()
                    char.Head.Size = Vector3.new(1,1,1)
                    char.Head.Transparency = 0
                end)
            end
        end
        if noclipEnabled then ToggleNoclip(true) end
        if antiSitEnabled then ToggleAntiSit(true) end
        if antiAfkEnabled then ToggleAntiAfk(true) end
        if autoFarmGari then toggleAutoFarm(true) end
        if nitroAuto then toggleNitro(true) end
        if aimbotMobileEnabled then
            if mobileAutoAimConn then mobileAutoAimConn:Disconnect() end
            mobileAutoAimConn = SetupMobileAutoAim()
        end
        if aimbotMobileLookEnabled then
            if mobileLookConn then mobileLookConn:Disconnect() end
            mobileLookConn = SetupMobileLookAim()
        end
        if fakeDashEnabled then startDashLoop() end
        if fakeLagEnabled then startLagLoop() end
        rebuildFriendEntries()
        updateFOVVisual()
        if billboardEnabled then
            for _, plr in ipairs(Players:GetPlayers()) do if plr ~= LP and plr.Character then createBillboardForCharacter(plr.Character, plr) end end
        end
        if espEnabled and drawingSupported then
            for _, plr in ipairs(Players:GetPlayers()) do if plr ~= LP then createESPObjectsForPlayer(plr) end end
        end
    end
    saveCard:AddButton("Salvar Configuração", function()
        if not hasFS() then hub:Notify("Config", "Executor sem suporte a arquivos") return end
        local ok, json = pcall(function() return HttpService:JSONEncode(snapshotConfig()) end)
        if ok then pcall(function() writefile(CONFIG_FILE, json) end); hub:Notify("Config", "Salvo") end
    end)
    saveCard:AddButton("Carregar Configuração", function()
        if not hasFS() then hub:Notify("Config", "Executor sem suporte") return end
        if not isfile(CONFIG_FILE) then hub:Notify("Config", "Nenhum arquivo") return end
        local ok, raw = pcall(readfile, CONFIG_FILE)
        if ok then
            local okd, data = pcall(HttpService.JSONDecode, HttpService, raw)
            if okd then applyConfig(data); hub:Notify("Config", "Carregado") end
        end
    end)
    saveCard:AddButton("Apagar Config", function()
        if hasFS() and isfile(CONFIG_FILE) then pcall(delfile, CONFIG_FILE); hub:Notify("Config", "Apagado") end
    end)

    -- Unload
    local unloadCard = cfgTab:AddCard("Menu")
    unloadCard:AddButton("Unload (remover GUI)", function()
        if hub.Gui then hub.Gui:Destroy() end
        if drawingFOV then pcall(function() drawingFOV:Remove() end) end
        for plr,_ in pairs(ESPS) do removeESPObjects(plr) end
        if dashLoop then dashLoop:Disconnect() end
        if lagLoop then lagLoop:Disconnect() end
        if spinbotConnection then spinbotConnection:Disconnect() end
        if bhopConn then bhopConn:Disconnect() end
        if headSizeConnection then headSizeConnection:Disconnect() end
        if noclipConn then noclipConn:Disconnect() end
        if antiSitConn then antiSitConn:Disconnect() end
        if antiAfkThread then task.cancel(antiAfkThread) end
        if mobileAutoAimConn then mobileAutoAimConn:Disconnect() end
        if mobileLookConn then mobileLookConn:Disconnect() end
        hitboxOriginais = {}; originaisLegit = {}; visualPartsLegit = {}
        hub = nil
    end)
end

-- Aba Outros (Bypass, Copiar roupas, Server Hop, Rejoin)
local outTab = hub:AddTab("Outros", nil)
do
    outTab:AddCard("Bypass"):AddToggle("Bypass (ocultar menu)", false, function(s)
        if s then enableBypass() else disableBypass() end
    end)

    local copyCard = outTab:AddCard("Copiar Roupas")
    local selectedLabel = copyCard:AddInfo("Jogador selecionado", "Nenhum")
    local function updateSelectList()
        for _,c in ipairs(copyCard.Frame:GetDescendants()) do if c:IsA("TextButton") and c.Name == "PlayerSelect" then c:Destroy() end end
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LP then
                local btn = Instance.new("TextButton")
                btn.Name = "PlayerSelect"
                btn.Size = UDim2.new(1, -24, 0, 28)
                btn.BackgroundColor3 = THEME.SurfaceAlt
                btn.Font = Enum.Font.Gotham
                btn.Text = p.Name
                btn.TextColor3 = THEME.Text
                btn.AutoButtonColor = true
                corner(btn, CORNER_SM)
                btn.Parent = copyCard.Frame
                btn.MouseButton1Click:Connect(function()
                    selectedCopyTarget = p
                    selectedLabel.Text = p.Name
                    hub:Notify("Selecionado", p.Name, 1.5)
                end)
            end
        end
    end
    copyCard:AddButton("Atualizar lista de players", updateSelectList)
    copyCard:AddButton("Copiar roupas", copyClothes)
    updateSelectList()

    local servCard = outTab:AddCard("Servidor")
    servCard:AddButton("Server Hop", function()
        pcall(function()
            local res = game:HttpGet("https://games.roblox.com/v1/games/"..game.PlaceId.."/servers/Public?sortOrder=Asc&limit=100")
            local data = HttpService:JSONDecode(res)
            local servers = {}
            if data and data.data then
                for _,sv in ipairs(data.data) do
                    if sv.playing < sv.maxPlayers and sv.id ~= game.JobId then table.insert(servers, sv.id) end
                end
            end
            if #servers > 0 then TeleportService:TeleportToPlaceInstance(game.PlaceId, servers[math.random(1,#servers)], LP) end
        end)
    end)
    servCard:AddButton("Rejoin", function() TeleportService:Teleport(game.PlaceId, LP) end)
end

-- Notificação inicial
hub:Notify("FLUXOVIP+", "Menu carregado com sucesso! Todas as funções iniciaram desligadas.", 4)

return hub
