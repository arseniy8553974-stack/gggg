-- // 🧠 BRAINROT GOD | ИСПРАВЛЕННАЯ ВЕРСИЯ | НЕУЯЗВИМОСТЬ, ПОЛЁТ, АВТОСБОР //

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")
local Workspace = game:GetService("Workspace")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer

-- Настройки
local Settings = {
    GodMode = true,          -- Неуязвимость (не царапают)
    Noclip = true,
    Speed = true,
    Jump = true,
    Fly = false,
    SpeedValue = 50,
    JumpValue = 100,
    FlySpeed = 50,
    AutoCollect = true
}

-- ========== КРАСИВЫЙ GUI ==========
local ScreenGui = Instance.new("ScreenGui", CoreGui)
ScreenGui.Name = "BrainrotFixGUI"

local Main = Instance.new("Frame", ScreenGui)
Main.Size = UDim2.new(0, 280, 0, 430)
Main.Position = UDim2.new(0.5, -140, 0.5, -215)
Main.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
Main.BackgroundTransparency = 0.15
Main.Active = true
Main.Draggable = true
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 12)

-- Анимированная обводка
local stroke = Instance.new("UIStroke", Main)
stroke.Color = Color3.fromRGB(0, 255, 200)
stroke.Thickness = 3
stroke.Transparency = 0.4

spawn(function()
    while true do
        for _, col in ipairs({
            Color3.fromRGB(0, 255, 200),
            Color3.fromRGB(255, 0, 255),
            Color3.fromRGB(255, 255, 0)
        }) do
            stroke.Color = col
            task.wait(0.5)
        end
    end
end)

local Title = Instance.new("TextLabel", Main)
Title.Size = UDim2.new(1, 0, 0, 35)
Title.BackgroundTransparency = 1
Title.Text = "🧠 BRAINROT GOD"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.Font = Enum.Font.GothamBlack
Title.TextSize = 18

local Content = Instance.new("ScrollingFrame", Main)
Content.Size = UDim2.new(1, -10, 1, -40)
Content.Position = UDim2.new(0, 5, 0, 37)
Content.BackgroundTransparency = 1
Content.ScrollBarThickness = 4
Content.CanvasSize = UDim2.new(0, 0, 0, 390)

-- Функция переключателя
local function addToggle(text, setting, y, icon)
    local btn = Instance.new("TextButton", Content)
    btn.Size = UDim2.new(1, -5, 0, 35)
    btn.Position = UDim2.new(0, 2, 0, y)
    btn.BackgroundColor3 = Color3.fromRGB(40, 40, 55)
    btn.Text = icon .. " " .. text .. ": " .. (Settings[setting] and "🟢" or "🔴")
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 13
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 5)
    btn.MouseButton1Click:Connect(function()
        Settings[setting] = not Settings[setting]
        btn.Text = icon .. " " .. text .. ": " .. (Settings[setting] and "🟢" or "🔴")
    end)
    return btn
end

-- Функция кнопки действия
local function addButton(text, y, callback)
    local btn = Instance.new("TextButton", Content)
    btn.Size = UDim2.new(1, -5, 0, 35)
    btn.Position = UDim2.new(0, 2, 0, y)
    btn.BackgroundColor3 = Color3.fromRGB(70, 70, 90)
    btn.Text = text
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 14
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
    btn.MouseButton1Click:Connect(callback)
    return btn
end

addToggle("Неуязвимость", "GodMode", 5, "🛡️")
addToggle("Noclip", "Noclip", 45, "🚧")
addToggle("Скорость x3", "Speed", 85, "⚡")
addToggle("Супер-прыжок", "Jump", 125, "🦘")
local flyBtn = addToggle("Полёт (F)", "Fly", 165, "🪽")
addToggle("Авто-сбор мозгов", "AutoCollect", 205, "🧲")

addButton("📍 К чужой базе", 250, function()
    local char = LocalPlayer.Character
    if not char then return end
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return end

    local nearestBase, nearestDist = nil, math.huge
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj:IsA("Model") and obj.Name:lower():find("base") then
            -- проверяем, что база не наша
            local isOurs = false
            local owner = obj:FindFirstChild("Owner")
            if owner and owner:IsA("ObjectValue") and owner.Value == LocalPlayer then
                isOurs = true
            end
            if not isOurs then
                local primary = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")
                if primary then
                    local dist = (primary.Position - root.Position).Magnitude
                    if dist < nearestDist then
                        nearestDist = dist
                        nearestBase = primary
                    end
                end
            end
        end
    end
    if nearestBase then
        root.CFrame = CFrame.new(nearestBase.Position + Vector3.new(0, 5, 0))
    end
end)

addButton("🏠 К своей базе", 295, function()
    local char = LocalPlayer.Character
    if not char then return end
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return end

    -- ищем базу, принадлежащую LocalPlayer
    local myBase = nil
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj:IsA("Model") and obj.Name:lower():find("base") then
            local owner = obj:FindFirstChild("Owner")
            if owner and owner:IsA("ObjectValue") and owner.Value == LocalPlayer then
                local primary = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")
                if primary then
                    myBase = primary
                    break
                end
            end
        end
    end
    if myBase then
        root.CFrame = CFrame.new(myBase.Position + Vector3.new(0, 5, 0))
    else
        -- запасной вариант: телепорт к месту появления
        local spawn = Workspace:FindFirstChild("SpawnLocation")
        if spawn then
            root.CFrame = spawn.CFrame + Vector3.new(0, 5, 0)
        end
    end
end)

addButton("💨 Быстрый сбор (разово)", 340, function()
    autoCollectOnce()
end)

-- ========== ИГРОВАЯ ЛОГИКА ==========

-- Неуязвимость (God Mode)
LocalPlayer.CharacterAdded:Connect(function(char)
    local hum = char:WaitForChild("Humanoid")
    hum:GetPropertyChangedSignal("Health"):Connect(function()
        if Settings.GodMode and hum.Health > 0 and hum.Health < hum.MaxHealth then
            hum.Health = hum.MaxHealth
        end
    end)
    if Settings.Speed then hum.WalkSpeed = Settings.SpeedValue end
    if Settings.Jump then hum.JumpPower = Settings.JumpValue end
end)

if LocalPlayer.Character then
    local hum = LocalPlayer.Character:FindFirstChild("Humanoid")
    if hum then
        if Settings.Speed then hum.WalkSpeed = Settings.SpeedValue end
        if Settings.Jump then hum.JumpPower = Settings.JumpValue end
    end
end

-- Noclip
RunService.Stepped:Connect(function()
    if Settings.Noclip and LocalPlayer.Character then
        for _, part in ipairs(LocalPlayer.Character:GetDescendants()) do
            if part:IsA("BasePart") then part.CanCollide = false end
        end
    end
end)

-- Полёт (исправлен, без рывков)
local flying = false
local bg, bv
local flySpeed

local function startFly()
    local char = LocalPlayer.Character
    if not char then return end
    local root = char:FindFirstChild("HumanoidRootPart")
    local hum = char:FindFirstChild("Humanoid")
    if not root or not hum then return end
    flying = true
    flySpeed = Settings.FlySpeed
    bg = Instance.new("BodyGyro", root)
    bg.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
    bg.CFrame = root.CFrame
    bv = Instance.new("BodyVelocity", root)
    bv.MaxForce = Vector3.new(9e9, 9e9, 9e9)
    hum.PlatformStand = true
    -- Отключаем столкновения персонажа
    for _, p in ipairs(char:GetDescendants()) do
        if p:IsA("BasePart") then p.CanCollide = false end
    end
end

local function stopFly()
    flying = false
    if bg then bg:Destroy() end
    if bv then bv:Destroy() end
    local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid")
    if hum then hum.PlatformStand = false end
end

UserInputService.InputBegan:Connect(function(input, gpe)
    if gpe then return end
    if input.KeyCode == Enum.KeyCode.F and Settings.Fly then
        if flying then stopFly() else startFly() end
    end
end)

RunService.RenderStepped:Connect(function()
    if flying and Settings.Fly and LocalPlayer.Character then
        local root = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if root then
            local cam = Workspace.CurrentCamera
            local move = Vector3.zero
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then move += cam.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then move -= cam.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then move -= cam.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then move += cam.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then move += Vector3.new(0, 1, 0) end
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then move -= Vector3.new(0, 1, 0) end
            -- Плавное управление: применяем скорость сразу, без рывков
            if bv then bv.Velocity = move * flySpeed end
            if bg then bg.CFrame = cam.CFrame end
        end
    end
end)

-- Автосбор мозгов
local function autoCollectOnce()
    if not LocalPlayer.Character then return end
    local root = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end

    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj:IsA("Tool") or obj:IsA("Model") then
            local name = obj.Name:lower()
            if name:find("brain") or name:find("rot") or name:find("мозг") then
                if obj:IsA("Tool") then
                    local handle = obj:FindFirstChild("Handle")
                    if handle then
                        firetouchinterest(root, handle, 0)
                        firetouchinterest(root, handle, 1)
                    end
                elseif obj:IsA("Model") then
                    local primary = obj.PrimaryPart
                    if primary then
                        firetouchinterest(root, primary, 0)
                        firetouchinterest(root, primary, 1)
                    end
                end
            end
        end
    end
end

-- Цикл автосбора
spawn(function()
    while true do
        if Settings.AutoCollect then
            autoCollectOnce()
        end
        task.wait(0.8) -- оптимальный интервал
    end
end)

print("✅ BRAINROT GOD активирован! Все функции исправлены.")
