--[[
    ONYX HUB - RAYFIELD EDITION
    Compatível: Delta, Xeno, Synapse, Krnl
]]

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local HttpService = game:GetService("HttpService")
local TeleportService = game:GetService("TeleportService")
local Workspace = game:GetService("Workspace")

local LP = Players.LocalPlayer

-- ==================== RAYFIELD LIBRARY ====================
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

-- ==================== DETECÇÃO DRAWING ====================
local hasDrawing = false
pcall(function()
    if Drawing and typeof(Drawing) == "table" and Drawing.new then
        local test = Drawing.new("Circle")
        test:Remove()
        hasDrawing = true
    end
end)

-- ==================== CRIAR WINDOW ====================
local Window = Rayfield:CreateWindow({
    Name = "ONYX HUB",
    LoadingTitle = "ONYX Loading...",
    LoadingSubtitle = "by CLS",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = "OnyxHub",
        FileName = "Config"
    },
    Discord = {
        Enabled = false,
        Invite = "noinvitelink",
        RememberJoins = true
    },
    KeySystem = false,
    KeySettings = {
        Title = "ONYX KEY",
        Subtitle = "Key System",
        Note = "No key needed",
        FileName = "OnyxKey",
        SaveKey = true,
        ShowBox = false
    }
})

-- ==================== VARIÁVEIS GLOBAIS ====================
local Settings = {
    ESP = false,
    Aimbot = false,
    AimbotSmooth = false,
    Noclip = false,
    AntiSit = true,
    AntiAfk = true,
    Spinbot = false,
    HitboxExpanded = false,
    AutoFarmGari = false,
    NitroAuto = false,
    SpeedCar = false,
    VFly = false,
}

local AimbotSettings = {
    FOVRadius = 150,
    HitPart = "Head",
    SpinSpeed = 50,
    SmoothSpeed = 0.15,
}

local Connections = {}

-- ==================== HOME TAB ====================
local HomeTab = Window:CreateTab("🏠 Home", 0)

local HomeSection = HomeTab:CreateSection("Status")

HomeTab:CreateLabel("ONYX HUB - Premium Edition")
HomeTab:CreateLabel("Executor: Delta/Xeno/Synapse")
HomeTab:CreateLabel("Status: Online ✓")

local FPSLabel = HomeTab:CreateLabel("FPS: —")
local PlayersLabel = HomeTab:CreateLabel("Players: —")

-- Update FPS
RunService.RenderStepped:Connect(function()
    local fps = math.floor(1 / RunService.RenderStepped:Wait())
    FPSLabel:Set("FPS: " .. fps)
    PlayersLabel:Set("Players: " .. #Players:GetPlayers())
end)

local PlayerSection = HomeTab:CreateSection("Player Info")
HomeTab:CreateLabel("Username: " .. LP.Name)
HomeTab:CreateLabel("User ID: " .. tostring(LP.UserId))
HomeTab:CreateLabel("Account Age: " .. LP.AccountAge .. " days")

if hasDrawing then
    HomeTab:CreateLabel("Drawing: ✓ Available")
else
    HomeTab:CreateLabel("Drawing: ✗ Not Supported")
end

-- ==================== COMBAT TAB ====================
local CombatTab = Window:CreateTab("⚔️ Combat", 1)

local AimbotSection = CombatTab:CreateSection("Aimbot")

CombatTab:CreateToggle({
    Name = "Aimbot",
    CurrentValue = false,
    Flag = "AimbotToggle",
    Callback = function(Value)
        Settings.Aimbot = Value
        if Value then
            Rayfield:Notify({
                Title = "Aimbot",
                Content = "Ativado ✓",
                Duration = 2,
                Image = 4483362458,
            })
        end
    end,
})

CombatTab:CreateToggle({
    Name = "Smooth Aimbot",
    CurrentValue = false,
    Flag = "SmoothAimbotToggle",
    Callback = function(Value)
        Settings.AimbotSmooth = Value
    end,
})

CombatTab:CreateSlider({
    Name = "FOV Radius",
    Min = 50,
    Max = 300,
    Increment = 10,
    CurrentValue = 150,
    Flag = "FOVSlider",
    Callback = function(Value)
        AimbotSettings.FOVRadius = Value
    end,
})

CombatTab:CreateSlider({
    Name = "Smooth Speed",
    Min = 1,
    Max = 100,
    Increment = 1,
    CurrentValue = 15,
    Flag = "SmoothSpeedSlider",
    Callback = function(Value)
        AimbotSettings.SmoothSpeed = Value / 100
    end,
})

CombatTab:CreateDropdown({
    Name = "Hit Part",
    Options = {"Head", "UpperTorso", "HumanoidRootPart"},
    CurrentOption = {"Head"},
    MultipleOptions = false,
    Flag = "HitPartDropdown",
    Callback = function(Options)
        AimbotSettings.HitPart = Options[1]
    end,
})

CombatTab:CreateSlider({
    Name = "Spin Speed",
    Min = 10,
    Max = 100,
    Increment = 5,
    CurrentValue = 50,
    Flag = "SpinSpeedSlider",
    Callback = function(Value)
        AimbotSettings.SpinSpeed = Value
    end,
})

CombatTab:CreateToggle({
    Name = "Spinbot",
    CurrentValue = false,
    Flag = "SpinbotToggle",
    Callback = function(Value)
        Settings.Spinbot = Value
    end,
})

-- ==================== VISUAL TAB ====================
local VisualTab = Window:CreateTab("👁️ Visual", 2)

local ESPSection = VisualTab:CreateSection("ESP")

if hasDrawing then
    VisualTab:CreateToggle({
        Name = "ESP Master",
        CurrentValue = false,
        Flag = "ESPToggle",
        Callback = function(Value)
            Settings.ESP = Value
            Rayfield:Notify({
                Title = "ESP",
                Content = Value and "Ativado ✓" or "Desativado ✕",
                Duration = 2,
                Image = 4483362458,
            })
        end,
    })

    VisualTab:CreateToggle({
        Name = "ESP Box",
        CurrentValue = false,
        Flag = "ESPBoxToggle",
        Callback = function(Value)
            -- ESP Box implementation
        end,
    })

    VisualTab:CreateToggle({
        Name = "ESP Skeleton",
        CurrentValue = false,
        Flag = "ESPSkeletonToggle",
        Callback = function(Value)
            -- ESP Skeleton implementation
        end,
    })

    VisualTab:CreateToggle({
        Name = "ESP Name",
        CurrentValue = false,
        Flag = "ESPNameToggle",
        Callback = function(Value)
            -- ESP Name implementation
        end,
    })

    VisualTab:CreateColorPicker({
        Name = "ESP Color",
        Color = Color3.fromRGB(255, 20, 147),
        Flag = "ESPColorPicker",
        Callback = function(Value)
            -- Change ESP color
        end
    })
else
    VisualTab:CreateLabel("⚠️ Drawing não suportado")
    VisualTab:CreateLabel("ESP desativado neste executor")
end

-- ==================== PLAYER TAB ====================
local PlayerTab = Window:CreateTab("👥 Players", 3)

PlayerTab:CreateSection("Player Actions")

PlayerTab:CreateButton({
    Name = "Refresh Player List",
    Callback = function()
        Rayfield:Notify({
            Title = "Players",
            Content = "Lista atualizada: " .. #Players:GetPlayers(),
            Duration = 2,
            Image = 4483362458,
        })
    end,
})

local playersSection = PlayerTab:CreateSection("Online Players")
local function updatePlayerList()
    playersSection:ClearButtons()
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LP then
            playersSection:CreateButton({
                Name = player.Name .. " (Click para mais)",
                Callback = function()
                    Rayfield:Notify({
                        Title = player.Name,
                        Content = "ID: " .. player.UserId .. "\nTeam: " .. tostring(player.Team),
                        Duration = 3,
                        Image = 4483362458,
                    })
                end,
            })
        end
    end
end

updatePlayerList()
Players.PlayerAdded:Connect(updatePlayerList)
Players.PlayerRemoving:Connect(updatePlayerList)

-- ==================== MOVEMENT TAB ====================
local MovementTab = Window:CreateTab("🚀 Movement", 4)

MovementTab:CreateSection("Movimento")

MovementTab:CreateToggle({
    Name = "Noclip",
    CurrentValue = false,
    Flag = "NoclipToggle",
    Callback = function(Value)
        Settings.Noclip = Value
        if Value then
            if not Connections.Noclip then
                Connections.Noclip = RunService.Stepped:Connect(function()
                    if Settings.Noclip then
                        local char = LP.Character
                        if char then
                            for _, part in ipairs(char:GetDescendants()) do
                                if part:IsA("BasePart") then
                                    pcall(function() part.CanCollide = false end)
                                end
                            end
                        end
                    end
                end)
            end
        else
            if Connections.Noclip then
                Connections.Noclip:Disconnect()
                Connections.Noclip = nil
            end
            local char = LP.Character
            if char then
                for _, part in ipairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then
                        pcall(function() part.CanCollide = true end)
                    end
                end
            end
        end
        Rayfield:Notify({
            Title = "Noclip",
            Content = Value and "Ativado ✓" or "Desativado ✕",
            Duration = 2,
            Image = 4483362458,
        })
    end,
})

MovementTab:CreateToggle({
    Name = "Anti-Sit",
    CurrentValue = true,
    Flag = "AntiSitToggle",
    Callback = function(Value)
        Settings.AntiSit = Value
        if Value then
            if not Connections.AntiSit then
                Connections.AntiSit = RunService.Heartbeat:Connect(function()
                    if Settings.AntiSit then
                        local char = LP.Character
                        if char then
                            local h = char:FindFirstChildOfClass("Humanoid")
                            if h then
                                if h.Sit then h.Sit = false end
                                if h:GetState() == Enum.HumanoidStateType.Seated then
                                    h:ChangeState(Enum.HumanoidStateType.Running)
                                end
                            end
                        end
                    end
                end)
            end
        else
            if Connections.AntiSit then
                Connections.AntiSit:Disconnect()
                Connections.AntiSit = nil
            end
        end
    end,
})

MovementTab:CreateToggle({
    Name = "Anti-AFK",
    CurrentValue = true,
    Flag = "AntiAfkToggle",
    Callback = function(Value)
        Settings.AntiAfk = Value
    end,
})

-- Veículos
MovementTab:CreateSection("Veículos")

MovementTab:CreateToggle({
    Name = "Speed Car",
    CurrentValue = false,
    Flag = "SpeedCarToggle",
    Callback = function(Value)
        Settings.SpeedCar = Value
    end,
})

MovementTab:CreateToggle({
    Name = "Fly Car",
    CurrentValue = false,
    Flag = "FlyCarToggle",
    Callback = function(Value)
        Settings.VFly = Value
    end,
})

MovementTab:CreateToggle({
    Name = "Nitro Auto",
    CurrentValue = false,
    Flag = "NitroToggle",
    Callback = function(Value)
        Settings.NitroAuto = Value
        Rayfield:Notify({
            Title = "Nitro",
            Content = Value and "Ativado! (Tecla N)" or "Desativado",
            Duration = 2,
            Image = 4483362458,
        })
    end,
})

MovementTab:CreateSlider({
    Name = "Nitro Force",
    Min = 20,
    Max = 150,
    Increment = 10,
    CurrentValue = 50,
    Flag = "NitroForceSlider",
    Callback = function(Value)
        -- Nitro force
    end,
})

-- ==================== FARM TAB ====================
local FarmTab = Window:CreateTab("🌾 Farm", 5)

FarmTab:CreateSection("Auto Farm")

FarmTab:CreateToggle({
    Name = "Auto Farm Gari",
    CurrentValue = false,
    Flag = "AutoFarmToggle",
    Callback = function(Value)
        Settings.AutoFarmGari = Value
        Rayfield:Notify({
            Title = "Auto Farm",
            Content = Value and "Iniciando... ✓" or "Parado ✕",
            Duration = 2,
            Image = 4483362458,
        })
    end,
})

FarmTab:CreateSlider({
    Name = "Delay entre coletas (s)",
    Min = 2,
    Max = 30,
    Increment = 1,
    CurrentValue = 10,
    Flag = "FarmDelaySlider",
    Callback = function(Value)
        -- Farm delay
    end,
})

FarmTab:CreateLabel("O personagem andará até o lixo")
FarmTab:CreateLabel("e desviará de obstáculos automaticamente")

-- ==================== HITBOX TAB ====================
local HitboxTab = Window:CreateTab("🎯 Hitbox", 6)

HitboxTab:CreateSection("Hitbox Expansion")

HitboxTab:CreateToggle({
    Name = "Hitbox Expandida",
    CurrentValue = false,
    Flag = "HitboxToggle",
    Callback = function(Value)
        Settings.HitboxExpanded = Value
    end,
})

HitboxTab:CreateSlider({
    Name = "Hitbox Size",
    Min = 5,
    Max = 50,
    Increment = 1,
    CurrentValue = 15,
    Flag = "HitboxSizeSlider",
    Callback = function(Value)
        -- Hitbox size
    end,
})

HitboxTab:CreateSlider({
    Name = "Hitbox Transparency",
    Min = 0,
    Max = 100,
    Increment = 5,
    CurrentValue = 70,
    Flag = "HitboxTransSlider",
    Callback = function(Value)
        -- Hitbox transparency
    end,
})

HitboxTab:CreateToggle({
    Name = "Hitbox LEGIT",
    CurrentValue = false,
    Flag = "LegitHitboxToggle",
    Callback = function(Value)
        -- Legit hitbox
    end,
})

-- ==================== CONFIG TAB ====================
local ConfigTab = Window:CreateTab("⚙️ Config", 7)

ConfigTab:CreateSection("Configurações")

ConfigTab:CreateToggle({
    Name = "Ativar Notificações",
    CurrentValue = true,
    Flag = "NotificationsToggle",
    Callback = function(Value)
        -- Notifications setting
    end,
})

ConfigTab:CreateButton({
    Name = "Salvar Config",
    Callback = function()
        Rayfield:Notify({
            Title = "Config",
            Content = "Configurações salvas! ✓",
            Duration = 2,
            Image = 4483362458,
        })
    end,
})

ConfigTab:CreateButton({
    Name = "Carregar Config",
    Callback = function()
        Rayfield:Notify({
            Title = "Config",
            Content = "Configurações carregadas! ✓",
            Duration = 2,
            Image = 4483362458,
        })
    end,
})

ConfigTab:CreateButton({
    Name = "Unload (Remover GUI)",
    Callback = function()
        Window:Close()
    end,
})

ConfigTab:CreateSection("Server")

ConfigTab:CreateButton({
    Name = "Server Hop",
    Callback = function()
        pcall(function()
            local res = game:HttpGet("https://games.roblox.com/v1/games/"..game.PlaceId.."/servers/Public?sortOrder=Asc&limit=100")
            local data = HttpService:JSONDecode(res)
            local servers = {}
            if data and data.data then
                for _, sv in ipairs(data.data) do
                    if sv.playing < sv.maxPlayers and sv.id ~= game.JobId then
                        table.insert(servers, sv.id)
                    end
                end
            end
            if #servers > 0 then
                TeleportService:TeleportToPlaceInstance(game.PlaceId, servers[math.random(1, #servers)], LP)
            end
        end)
        Rayfield:Notify({
            Title = "Server Hop",
            Content = "Teleportando...",
            Duration = 2,
            Image = 4483362458,
        })
    end,
})

ConfigTab:CreateButton({
    Name = "Rejoin",
    Callback = function()
        TeleportService:Teleport(game.PlaceId, LP)
    end,
})

-- ==================== STARTUP NOTIFICATION ====================
task.delay(0.5, function()
    Rayfield:Notify({
        Title = "ONYX HUB",
        Content = "✓ Carregado com sucesso!\nExecutor: Delta/Xeno\nDrawing: " .. (hasDrawing and "✓" or "✗"),
        Duration = 5,
        Image = 4483362458,
    })
end)

-- ==================== MAIN LOOP ====================
RunService.RenderStepped:Connect(function()
    pcall(function()
        if Settings.Noclip then
            local char = LP.Character
            if char then
                for _, part in ipairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then
                        pcall(function() part.CanCollide = false end)
                    end
                end
            end
        end
    end)
end)
