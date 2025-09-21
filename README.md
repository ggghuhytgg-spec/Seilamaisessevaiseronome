-- FloatSupermanGUI.lua
-- LocalScript (StarterPlayer > StarterPlayerScripts)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local char, hrp, humanoid

-- CONFIG DEFAULT
local MOVE_SPEED_AIR = 24
local subidaSpeed = 10 -- velocidade padrão da subida infinita

-- FLAGS
local airwalk = false
local godmode = false
local subidaInfinita = false

-- GUI
local gui = Instance.new("ScreenGui")
gui.Name = "FloatSupermanGUI"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local main = Instance.new("Frame")
main.Size = UDim2.new(0, 220, 0, 250)
main.Position = UDim2.new(0.7, 0, 0.2, 0)
main.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
main.BackgroundTransparency = 0.1
main.BorderSizePixel = 0
main.Active = true
main.Draggable = true
main.Parent = gui

Instance.new("UICorner", main).CornerRadius = UDim.new(0,16)
local shadow = Instance.new("UIStroke", main)
shadow.Thickness = 2
shadow.Color = Color3.fromRGB(0,150,255)

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1,0,0,30)
title.BackgroundTransparency = 1
title.Text = "⚡ Float Superman"
title.TextColor3 = Color3.fromRGB(255,255,255)
title.Font = Enum.Font.GothamBold
title.TextSize = 16
title.Parent = main

-- Função criar botão
local function createButton(text,posY,color)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0,180,0,30)
    btn.Position = UDim2.new(0.5,-90,0,posY)
    btn.Text = text
    btn.BackgroundColor3 = color
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 14
    btn.Parent = main
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0,12)
    return btn
end

-- Função criar slider
local function createSlider(name,posY,default,callback)
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0,100,0,20)
    label.Position = UDim2.new(0,10,0,posY)
    label.BackgroundTransparency = 1
    label.Text = name
    label.TextColor3 = Color3.fromRGB(255,255,255)
    label.Font = Enum.Font.Gotham
    label.TextSize = 14
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = main

    local box = Instance.new("TextBox")
    box.Size = UDim2.new(0,80,0,20)
    box.Position = UDim2.new(0,120,0,posY)
    box.BackgroundColor3 = Color3.fromRGB(40,40,40)
    box.TextColor3 = Color3.fromRGB(255,255,255)
    box.Font = Enum.Font.Gotham
    box.TextSize = 14
    box.Text = tostring(default)
    box.Parent = main
    Instance.new("UICorner", box).CornerRadius = UDim.new(0,8)

    box.FocusLost:Connect(function()
        local num = tonumber(box.Text)
        if num then callback(num) end
    end)
end

-- Botões
local airwalkBtn = createButton("Ativar Andar no Ar",40,Color3.fromRGB(150,0,255))
local godBtn = createButton("Ativar GodMode",75,Color3.fromRGB(0,200,100))
local upBtn = createButton("Ativar Subida Infinita",110,Color3.fromRGB(255,140,0))

-- Sliders
createSlider("Vel. Andar:",170,MOVE_SPEED_AIR,function(v) MOVE_SPEED_AIR = v end)
createSlider("Vel. Subida:",200,subidaSpeed,function(v) subidaSpeed = v end)

-- Setup Character
local function setupCharacter(c)
    char = c
    humanoid = char:WaitForChild("Humanoid")
    hrp = char:WaitForChild("HumanoidRootPart")
end
if player.Character then setupCharacter(player.Character) end
player.CharacterAdded:Connect(setupCharacter)

---------------------
-- ANDAR NO AR
---------------------
airwalkBtn.MouseButton1Click:Connect(function()
    airwalk = not airwalk
    if airwalk then
        airwalkBtn.Text = "Desativar Andar no Ar"
        airwalkBtn.BackgroundColor3 = Color3.fromRGB(200,50,50)
    else
        airwalkBtn.Text = "Ativar Andar no Ar"
        airwalkBtn.BackgroundColor3 = Color3.fromRGB(150,0,255)
    end
end)

---------------------
-- GODMODE
---------------------
godBtn.MouseButton1Click:Connect(function()
    godmode = not godmode
    if godmode and humanoid then
        humanoid.MaxHealth = math.huge
        humanoid.Health = math.huge
        godBtn.Text = "Desativar GodMode"
        godBtn.BackgroundColor3 = Color3.fromRGB(200,50,50)
    elseif humanoid then
        humanoid.MaxHealth = 100
        humanoid.Health = 100
        godBtn.Text = "Ativar GodMode"
        godBtn.BackgroundColor3 = Color3.fromRGB(0,200,100)
    end
end)

---------------------
-- SUBIDA INFINITA
---------------------
upBtn.MouseButton1Click:Connect(function()
    subidaInfinita = not subidaInfinita
    if subidaInfinita then
        upBtn.Text = "Desativar Subida Infinita"
        upBtn.BackgroundColor3 = Color3.fromRGB(200,50,50)
    else
        upBtn.Text = "Ativar Subida Infinita"
        upBtn.BackgroundColor3 = Color3.fromRGB(255,140,0)
        if hrp then hrp.Velocity = Vector3.zero end
    end
end)

---------------------
-- LOOP PRINCIPAL
---------------------
RunService.Heartbeat:Connect(function(dt)
    -- Andar no ar
    if airwalk and hrp and humanoid then
        local moveDir = humanoid.MoveDirection
        local velocity = Vector3.new(moveDir.X * MOVE_SPEED_AIR, 0, moveDir.Z * MOVE_SPEED_AIR)
        hrp.Velocity = velocity
    end

    -- Subida infinita
    if subidaInfinita and hrp then
        hrp.Velocity = Vector3.new(0,subidaSpeed,0)
    end
end)

print("[Float Superman GUI] carregado com Andar no Ar, GodMode e Subida Infinita!")
