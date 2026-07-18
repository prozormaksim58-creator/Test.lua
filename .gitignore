--[=[
░██████╗██╗░░░██╗███████╗██████╗░  ████████╗██╗░░░██╗███╗░░░███╗
██╔════╝██║░░░██║██╔════╝██╔══██╗  ╚══██╔══╝╚██╗░██╔╝████╗░████║
╚█████╗░██║░░░██║█████╗░░██████╔╝  ░░░██║░░░░╚████╔╝░██╔████╔██║
░╚═══██╗██║░░░██║██╔══╝░░██╔══██╗  ░░░██║░░░░░╚██╔╝░░██║╚██╔╝██║
██████╔╝╚██████╔╝███████╗██║░░██║  ░░░██║░░░░░░██║░░░██║░╚═╝░██║
╚═════╝░░╚═════╝░╚══════╝╚═╝░░╚═╝  ░░░╚═╝░░░░░░╚═╝░░░╚═╝░░░░░╚═╝
Скрипт: TYM TYM TYM CFG | Ultimate Aimbot + WH
Автор: palofsc
Версия: Final 3.0
Игра: Delta / FPS One Tap (Roblox)
]=]--

-- Сервисы
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer
local HttpService = game:GetService("HttpService")

-- ===== НАСТРОЙКИ ПО УМОЛЧАНИЮ =====
local Config = {
    Aimbot = {
        Enabled = true,
        FOV = 120,
        Smoothness = 2,
        TargetPart = "Head",
        TeamCheck = false,
        VisibleCheck = true
    },
    Triggerbot = {
        Enabled = true,
        Delay = 0.08,
        Radius = 30
    },
    ESP = {
        Enabled = true,
        Box = true,
        HealthBar = true,
        Name = true,
        Distance = true,
        TeamCheck = false,
        BoxColor = Color3.fromRGB(255, 30, 30),
        VisibleColor = Color3.fromRGB(30, 255, 255),
        TeamColor = Color3.fromRGB(30, 255, 30)
    },
    Misc = {
        AimKey = Enum.UserInputType.MouseButton2,
        FOVCircle = true,
        FOVCircleColor = Color3.fromRGB(255, 255, 255)
    }
}

-- ===== СОСТОЯНИЯ =====
local ESPDrawings = {}
local FOVCircle = nil
local LastFire = 0
local SavedConfigs = {}

-- ===== ФУНКЦИИ ESP =====
function ClearAllESP()
    for _, drawings in pairs(ESPDrawings) do
        for _, d in pairs(drawings) do pcall(function() d:Remove() end) end
    end
    ESPDrawings = {}
end

function CreateESP(player)
    local d = {
        BoxOutline = Drawing.new("Square"),
        Box = Drawing.new("Square"),
        HealthBG = Drawing.new("Square"),
        Health = Drawing.new("Square"),
        Name = Drawing.new("Text"),
        Distance = Drawing.new("Text")
    }
    d.BoxOutline.Thickness = 2; d.BoxOutline.Color = Color3.new(0,0,0); d.BoxOutline.Transparency = 0.8; d.BoxOutline.Filled = false
    d.Box.Thickness = 1.5; d.Box.Transparency = 0.9; d.Box.Filled = false
    d.HealthBG.Filled = true; d.HealthBG.Color = Color3.new(0,0,0)
    d.Health.Filled = true
    d.Name.Size = 13; d.Name.Color = Color3.new(1,1,1); d.Name.Center = true; d.Name.Outline = true
    d.Distance.Size = 12; d.Distance.Color = Color3.new(0.8,0.8,0.8); d.Distance.Center = true; d.Distance.Outline = true
    for _, v in pairs(d) do v.Visible = false end
    ESPDrawings[player] = d
end

function UpdateESP()
    if not Config.ESP.Enabled then
        for _, d in pairs(ESPDrawings) do for _, v in pairs(d) do v.Visible = false end end
        return
    end
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr == LocalPlayer then continue end
        local char = plr.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") or not char:FindFirstChild("Humanoid") then
            if ESPDrawings[plr] then for _, v in pairs(ESPDrawings[plr]) do v.Visible = false end end
            continue
        end
        local hum = char.Humanoid
        if hum.Health <= 0 then
            if ESPDrawings[plr] then for _, v in pairs(ESPDrawings[plr]) do v.Visible = false end end
            continue
        end
        if Config.ESP.TeamCheck and plr.Team == LocalPlayer.Team then
            if ESPDrawings[plr] then for _, v in pairs(ESPDrawings[plr]) do v.Visible = false end end
            continue
        end
        if not ESPDrawings[plr] then CreateESP(plr) end
        local root = char.HumanoidRootPart
        local head = char:FindFirstChild("Head")
        local top = (head and head.Position or root.Position + Vector3.new(0,2.5,0)) + Vector3.new(0,0.5,0)
        local bottom = root.Position - Vector3.new(0,3.3,0)
        local ts, tv = Camera:WorldToViewportPoint(top)
        local bs, bv = Camera:WorldToViewportPoint(bottom)
        if not tv or not bv then
            for _, v in pairs(ESPDrawings[plr]) do v.Visible = false end
            continue
        end
        local h = math.abs(ts.Y - bs.Y)
        local w = h * 0.55
        local x = ts.X - w/2
        local y = ts.Y
        local visible = false
        if Config.Aimbot.VisibleCheck and head then
            local rp = RaycastParams.new()
            rp.FilterDescendantsInstances = {char, LocalPlayer.Character}
            rp.FilterType = Enum.RaycastFilterType.Blacklist
            local ray = workspace:Raycast(Camera.CFrame.Position, (head.Position - Camera.CFrame.Position).Unit * 1000, rp)
            visible = (ray == nil)
        end
        local d = ESPDrawings[plr]
        if Config.ESP.Box then
            d.BoxOutline.Position = Vector2.new(x,y); d.BoxOutline.Size = Vector2.new(w,h); d.BoxOutline.Visible = true
            d.Box.Position = Vector2.new(x,y); d.Box.Size = Vector2.new(w,h); d.Box.Visible = true
            d.Box.Color = visible and Config.ESP.VisibleColor or Config.ESP.BoxColor
            if Config.ESP.TeamCheck and plr.Team == LocalPlayer.Team then d.Box.Color = Config.ESP.TeamColor end
        else d.BoxOutline.Visible = false; d.Box.Visible = false end
        if Config.ESP.HealthBar then
            local hp = hum.Health / hum.MaxHealth
            local bh = h * hp
            d.HealthBG.Position = Vector2.new(x-6, y); d.HealthBG.Size = Vector2.new(3, h); d.HealthBG.Visible = true
            d.Health.Position = Vector2.new(x-6, y + h - bh); d.Health.Size = Vector2.new(3, bh); d.Health.Visible = true
            d.Health.Color = hp > 0.5 and Color3.new(0,1,0) or (hp > 0.25 and Color3.new(1,1,0) or Color3.new(1,0,0))
        else d.HealthBG.Visible = false; d.Health.Visible = false end
        if Config.ESP.Name then
            d.Name.Position = Vector2.new(ts.X, ts.Y - 12); d.Name.Text = plr.Name; d.Name.Visible = true
        else d.Name.Visible = false end
        if Config.ESP.Distance then
            d.Distance.Position = Vector2.new(ts.X, bs.Y + 5)
            d.Distance.Text = string.format("%.0fM", (Camera.CFrame.Position - root.Position).Magnitude)
            d.Distance.Visible = true
        else d.Distance.Visible = false end
    end
end

-- ===== AIMBOT =====
function GetTarget()
    local best = nil; local bestDist = Config.Aimbot.FOV
    local mPos = UserInputService:GetMouseLocation()
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr == LocalPlayer then continue end
        local char = plr.Character
        if not char or not char:FindFirstChild("Humanoid") or not char:FindFirstChild("HumanoidRootPart") then continue end
        if char.Humanoid.Health <= 0 then continue end
        if Config.Aimbot.TeamCheck and plr.Team == LocalPlayer.Team then continue end
        local part = char:FindFirstChild(Config.Aimbot.TargetPart) or char:FindFirstChild("Head") or char.HumanoidRootPart
        local sp, onScr = Camera:WorldToViewportPoint(part.Position)
        if not onScr then continue end
        local dist = (Vector2.new(sp.X, sp.Y) - mPos).Magnitude
        if dist < bestDist then bestDist = dist; best = plr end
    end
    return best
end

function Aim(target)
    if not target or not target.Character then return end
    local part = target.Character:FindFirstChild(Config.Aimbot.TargetPart) or target.Character:FindFirstChild("Head")
    if not part then return end
    local cf = CFrame.new(Camera.CFrame.Position, part.Position)
    Camera.CFrame = Config.Aimbot.Smoothness > 1 and Camera.CFrame:Lerp(cf, 1/Config.Aimbot.Smoothness) or cf
end

function Shoot()
    local now = tick()
    if now - LastFire < Config.Triggerbot.Delay then return end
    LastFire = now
    mouse1press()
    task.wait(0.05)
    mouse1release()
end

-- ===== FOV CIRCLE =====
function UpdateFOVCircle()
    if FOVCircle then FOVCircle:Remove() end
    if not Config.Misc.FOVCircle then return end
    FOVCircle = Drawing.new("Circle")
    FOVCircle.Color = Config.Misc.FOVCircleColor
    FOVCircle.Thickness = 1.2; FOVCircle.Transparency = 0.65
    FOVCircle.Radius = Config.Aimbot.FOV
    FOVCircle.Visible = Config.Aimbot.Enabled
end
UpdateFOVCircle()

-- ===== КОНФИГ СИСТЕМА =====
function SaveConfig(name)
    SavedConfigs[name] = HttpService:JSONEncode(Config)
    return name .. " сохранён"
end

function LoadConfig(name)
    if SavedConfigs[name] then
        local data = HttpService:JSONDecode(SavedConfigs[name])
        for k, v in pairs(data) do Config[k] = v end
        return name .. " загружен"
    end
    return "Конфиг не найден"
end

-- ===== GUI МЕНЮ TYM TYM TYM =====
local UI = Instance.new("ScreenGui", game.CoreGui)
UI.Name = "TYM_TYM_TYM_CFG"
UI.ResetOnSpawn = false

-- Главное окно
local Main = Instance.new("Frame", UI)
Main.Size = UDim2.new(0, 320, 0, 420)
Main.Position = UDim2.new(0.5, -160, 0.4, -180)
Main.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
Main.BorderSizePixel = 0
Main.Active = true; Main.Draggable = true
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 8)

-- Заголовок
local Header = Instance.new("Frame", Main)
Header.Size = UDim2.new(1, 0, 0, 40)
Header.BackgroundColor3 = Color3.fromRGB(140, 20, 20)
Instance.new("UICorner", Header).CornerRadius = UDim.new(0, 8)
local HeaderText = Instance.new("TextLabel", Header)
HeaderText.Size = UDim2.new(1,0,1,0); HeaderText.Text = "SUPER TYM TYM TYM CFG v3.0"
HeaderText.TextColor3 = Color3.new(1,1,1); HeaderText.Font = Enum.Font.GothamBold; HeaderText.TextSize = 17
HeaderText.BackgroundTransparency = 1

-- Вкладки
local Tabs = Instance.new("Frame", Main)
Tabs.Size = UDim2.new(1, 0, 0, 32); Tabs.Position = UDim2.new(0, 0, 0, 42)
Tabs.BackgroundColor3 = Color3.fromRGB(28, 28, 28)

local currentTab = "AIMBOT"
local function MakeTab(name, pos)
    local btn = Instance.new("TextButton", Tabs)
    btn.Size = UDim2.new(0.33, -4, 1, -4); btn.Position = UDim2.new(pos, 2, 0, 2)
    btn.Text = name; btn.TextColor3 = Color3.new(1,1,1); btn.BackgroundColor3 = Color3.fromRGB(45,45,45)
    btn.Font = Enum.Font.GothamSemibold; btn.TextSize = 13
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 4)
    btn.MouseButton1Click:Connect(function()
        currentTab = name
        for _, b in ipairs(Tabs:GetChildren()) do if b:IsA("TextButton") then b.BackgroundColor3 = Color3.fromRGB(45,45,45) end end
        btn.BackgroundColor3 = Color3.fromRGB(140,20,20)
        UpdateTabContent()
    end)
    if name == "AIMBOT" then btn.BackgroundColor3 = Color3.fromRGB(140,20,20) end
    return btn
end
MakeTab("AIMBOT", 0); MakeTab("ESP", 0.33); MakeTab("CONFIG", 0.66)

-- Контент
local Content = Instance.new("ScrollingFrame", Main)
Content.Size = UDim2.new(1, -12, 1, -80); Content.Position = UDim2.new(0, 6, 0, 76)
Content.BackgroundTransparency = 1; Content.ScrollBarThickness = 6
Content.CanvasSize = UDim2.new(0,0,0,500); Content.ScrollingDirection = Enum.ScrollingDirection.Y

local function AddToggle(parent, text, default, callback)
    local frame = Instance.new("Frame", parent)
    frame.Size = UDim2.new(1, -10, 0, 30); frame.BackgroundTransparency = 1
    local label = Instance.new("TextLabel", frame)
    label.Size = UDim2.new(0.6, 0, 1, 0); label.Text = text; label.TextColor3 = Color3.new(0.85,0.85,0.85)
    label.BackgroundTransparency = 1; label.Font = Enum.Font.Gotham; label.TextSize = 13; label.TextXAlignment = Enum.TextXAlignment.Left
    local btn = Instance.new("TextButton", frame)
    btn.Size = UDim2.new(0.35, 0, 0.7, 0); btn.Position = UDim2.new(0.65, 0, 0.15, 0)
    btn.Text = default and "ON" or "OFF"; btn.TextColor3 = Color3.new(1,1,1)
    btn.BackgroundColor3 = default and Color3.fromRGB(30,160,30) or Color3.fromRGB(160,30,30)
    btn.Font = Enum.Font.GothamBold; btn.TextSize = 12
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 4)
    btn.MouseButton1Click:Connect(function()
        default = not default
        btn.Text = default and "ON" or "OFF"
        btn.BackgroundColor3 = default and Color3.fromRGB(30,160,30) or Color3.fromRGB(160,30,30)
        callback(default)
    end)
    return frame
end

local function AddSlider(parent, text, min, max, current, callback)
    local frame = Instance.new("Frame", parent)
    frame.Size = UDim2.new(1, -10, 0, 50); frame.BackgroundTransparency = 1
    local label = Instance.new("TextLabel", frame)
    label.Size = UDim2.new(1, 0, 0, 18); label.Text = text .. ": " .. current; label.TextColor3 = Color3.new(0.8,0.8,0.8)
    label.BackgroundTransparency = 1; label.Font = Enum.Font.Gotham; label.TextSize = 12; label.TextXAlignment = Enum.TextXAlignment.Left
    local dec = Instance.new("TextButton", frame)
    dec.Size = UDim2.new(0.15, 0, 0, 22); dec.Position = UDim2.new(0, 0, 0.45, 0); dec.Text = "-"
    dec.BackgroundColor3 = Color3.fromRGB(50,50,50); dec.TextColor3 = Color3.new(1,1,1); dec.Font = Enum.Font.GothamBold
    Instance.new("UICorner", dec).CornerRadius = UDim.new(0, 4)
    local inc = Instance.new("TextButton", frame)
    inc.Size = UDim2.new(0.15, 0, 0, 22); inc.Position = UDim2.new(0.85, 0, 0.45, 0); inc.Text = "+"
    inc.BackgroundColor3 = Color3.fromRGB(50,50,50); inc.TextColor3 = Color3.new(1,1,1); inc.Font = Enum.Font.GothamBold
    Instance.new("UICorner", inc).CornerRadius = UDim.new(0, 4)
    dec.MouseButton1Click:Connect(function() current = math.max(min, current - 1); label.Text = text..": "..current; callback(current) end)
    inc.MouseButton1Click:Connect(function() current = math.min(max, current + 1); label.Text = text..": "..current; callback(current) end)
    return frame
end

local function AddDropdown(parent, text, options, callback)
    local frame = Instance.new("Frame", parent)
    frame.Size = UDim2.new(1, -10, 0, 30); frame.BackgroundTransparency = 1
    local label = Instance.new("TextLabel", frame)
    label.Size = UDim2.new(0.45, 0, 1, 0); label.Text = text; label.TextColor3 = Color3.new(0.85,0.85,0.85)
    label.BackgroundTransparency = 1; label.Font = Enum.Font.Gotham; label.TextSize = 13; label.TextXAlignment = Enum.TextXAlignment.Left
    local dropdown = Instance.new("TextButton", frame)
    dropdown.Size = UDim2.new(0.5, 0, 0.8, 0); dropdown.Position = UDim2.new(0.5, 0, 0.1, 0)
    dropdown.Text = options[1]; dropdown.BackgroundColor3 = Color3.fromRGB(50,50,50)
    dropdown.TextColor3 = Color3.new(1,1,1); dropdown.Font = Enum.Font.Gotham; dropdown.TextSize = 12
    Instance.new("UICorner", dropdown).CornerRadius = UDim.new(0, 4)
    local idx = 1
    dropdown.MouseButton1Click:Connect(function()
        idx = idx % #options + 1; dropdown.Text = options[idx]; callback(options[idx])
    end)
    return frame
end

function UpdateTabContent()
    for _, v in ipairs(Content:GetChildren()) do v:Destroy() end
    local list = Instance.new("UIListLayout", Content)
    list.Padding = UDim.new(0, 5)
    if currentTab == "AIMBOT" then
        AddToggle(Content, "Aimbot", Config.Aimbot.Enabled, function(v) Config.Aimbot.Enabled = v end)
        AddToggle(Content, "Visible Check", Config.Aimbot.VisibleCheck, function(v) Config.Aimbot.VisibleCheck = v end)
        AddToggle(Content, "Team Check", Config.Aimbot.TeamCheck, function(v) Config.Aimbot.TeamCheck = v end)
        AddToggle(Content, "FOV Circle", Config.Misc.FOVCircle, function(v) Config.Misc.FOVCircle = v; UpdateFOVCircle() end)
        AddSlider(Content, "FOV", 25, 400, Config.Aimbot.FOV, function(v) Config.Aimbot.FOV = v; UpdateFOVCircle() end)
        AddSlider(Content, "Smoothness", 1, 20, Config.Aimbot.Smoothness, function(v) Config.Aimbot.Smoothness = v end)
        AddDropdown(Content, "Target Part", {"Head", "UpperTorso", "HumanoidRootPart"}, function(v) Config.Aimbot.TargetPart = v end)
        local sep = Instance.new("Frame", Content); sep.Size = UDim2.new(1,0,0,2); sep.BackgroundTransparency = 1
        Instance.new("TextLabel", Content).Text = "TRIGGERBOT"; Instance.new("TextLabel", Content).Size = UDim2.new(1,0,0,18)
        AddToggle(Content, "Triggerbot", Config.Triggerbot.Enabled, function(v) Config.Triggerbot.Enabled = v end)
        AddSlider(Content, "Delay (ms)", 0, 500, Config.Triggerbot.Delay*1000, function(v) Config.Triggerbot.Delay = v/1000 end)
        AddSlider(Content, "Radius", 5, 100, Config.Triggerbot.Radius, function(v) Config.Triggerbot.Radius = v end)
    elseif currentTab == "ESP" then
        AddToggle(Content, "ESP", Config.ESP.Enabled, function(v) Config.ESP.Enabled = v end)
        AddToggle(Content, "Box", Config.ESP.Box, function(v) Config.ESP.Box = v end)
        AddToggle(Content, "Health Bar", Config.ESP.HealthBar, function(v) Config.ESP.HealthBar = v end)
        AddToggle(Content, "Name", Config.ESP.Name, function(v) Config.ESP.Name = v end)
        AddToggle(Content, "Distance", Config.ESP.Distance, function(v) Config.ESP.Distance = v end)
        AddToggle(Content, "Team Check", Config.ESP.TeamCheck, function(v) Config.ESP.TeamCheck = v end)
    elseif currentTab == "CONFIG" then
        local cfgNameBox = Instance.new("TextBox", Content)
        cfgNameBox.Size = UDim2.new(1, -10, 0, 30); cfgNameBox.PlaceholderText = "Название конфига..."
        cfgNameBox.BackgroundColor3 = Color3.fromRGB(35,35,35); cfgNameBox.TextColor3 = Color3.new(1,1,1)
        cfgNameBox.Font = Enum.Font.Gotham; cfgNameBox.TextSize = 13
        Instance.new("UICorner", cfgNameBox).CornerRadius = UDim.new(0, 4)
        local saveBtn = Instance.new("TextButton", Content)
        saveBtn.Size = UDim2.new(1, -10, 0, 28); saveBtn.Text = "SAVE CONFIG"
        saveBtn.BackgroundColor3 = Color3.fromRGB(30,130,30); saveBtn.TextColor3 = Color3.new(1,1,1)
        saveBtn.Font = Enum.Font.GothamBold; saveBtn.TextSize = 13
        Instance.new("UICorner", saveBtn).CornerRadius = UDim.new(0, 4)
        saveBtn.MouseButton1Click:Connect(function()
            if cfgNameBox.Text ~= "" then SaveConfig(cfgNameBox.Text) end
        end)
        local loadBtn = Instance.new("TextButton", Content)
        loadBtn.Size = UDim2.new(1, -10, 0, 28); loadBtn.Text = "LOAD CONFIG"
        loadBtn.BackgroundColor3 = Color3.fromRGB(130,100,30); loadBtn.TextColor3 = Color3.new(1,1,1)
        loadBtn.Font = Enum.Font.GothamBold; loadBtn.TextSize = 13
        Instance.new("UICorner", loadBtn).CornerRadius = UDim.new(0, 4)
        loadBtn.MouseButton1Click:Connect(function()
            if cfgNameBox.Text ~= "" then LoadConfig(cfgNameBox.Text) end
        end)
        -- Список сохранённых
        for name, _ in pairs(SavedConfigs) do
            local item = Instance.new("TextButton", Content)
            item.Size = UDim2.new(1, -10, 0, 25); item.Text = name
            item.BackgroundColor3 = Color3.fromRGB(40,40,40); item.TextColor3 = Color3.new(0.9,0.9,0.9)
            item.Font = Enum.Font.Gotham; item.TextSize = 12
            Instance.new("UICorner", item).CornerRadius = UDim.new(0, 4)
            item.MouseButton1Click:Connect(function() LoadConfig(name); UpdateTabContent() end)
        end
    end
    Content.CanvasSize = UDim2.new(0,0,0, Content.UIListLayout.AbsoluteContentSize.Y + 20)
end
UpdateTabContent()

-- Авто-обновление контента
Content.ChildAdded:Connect(function()
    Content.CanvasSize = UDim2.new(0,0,0, Content.UIListLayout.AbsoluteContentSize.Y + 20)
end)

-- ===== ГЛАВНЫЙ ЦИКЛ =====
RunService.RenderStepped:Connect(function()
    UpdateESP()
    if FOVCircle then FOVCircle.Position = UserInputService:GetMouseLocation() end
    local aiming = UserInputService:IsMouseButtonPressed(Config.Misc.AimKey)
    local target = GetTarget()
    if Config.Aimbot.Enabled and aiming and target then Aim(target) end
    if Config.Triggerbot.Enabled and target then
        local part = target.Character and (target.Character:FindFirstChild(Config.Aimbot.TargetPart) or target.Character:FindFirstChild("Head"))
     
