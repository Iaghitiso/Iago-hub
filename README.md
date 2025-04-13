--[[
  ✅ Script Blox Fruits Mobile - GUI Estilo RedzHub - by IS
  🔰 Otimizado para Delta Executor / Mobile
  ⚔️ PvP | 🍍 Frutas | 🌍 Teleporte entre CÉAs | 🛠️ Utilitários
--]]

-- Services
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local TeleportService = game:GetService("TeleportService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer

-- Gui Creation
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "IS_BloxGUI"

-- Minimized Button (IS RGB)
local Minimized = Instance.new("TextButton", ScreenGui)
Minimized.Size = UDim2.new(0, 50, 0, 50)
Minimized.Position = UDim2.new(0, 10, 0.4, 0)
Minimized.Text = "IS"
Minimized.TextColor3 = Color3.fromRGB(255, 255, 255)
Minimized.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Minimized.BorderSizePixel = 2
Minimized.Name = "MinimizedBtn"

-- Rainbow Effect
spawn(function()
    local hue = 0
    while wait() do
        hue = hue + 1
        if hue >= 255 then hue = 0 end
        Minimized.BorderColor3 = Color3.fromHSV(hue/255,1,1)
    end
end)\n
-- Main Frame
local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
MainFrame.BorderColor3 = Color3.fromRGB(0, 162, 255)
MainFrame.Size = UDim2.new(0, 300, 0, 400)
MainFrame.Position = UDim2.new(0, 70, 0.2, 0)
MainFrame.Visible = false

-- Toggle Menu
Minimized.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
end)

-- Tab Buttons
local function createTab(name, posY)
    local tab = Instance.new("TextButton", MainFrame)
    tab.Size = UDim2.new(1, -10, 0, 30)
    tab.Position = UDim2.new(0, 5, 0, posY)
    tab.Text = name
    tab.BackgroundColor3 = Color3.fromRGB(0, 162, 255)
    tab.TextColor3 = Color3.fromRGB(255, 255, 255)
    tab.Font = Enum.Font.SourceSansBold
    tab.TextSize = 20
    return tab
end

-- Tabs
local PvPBtn = createTab("⚔️ PvP", 10)
local FruitBtn = createTab("🍍 Frutas", 50)
local TPBtn = createTab("🌍 Teleporte", 90)
local UtilBtn = createTab("🛠️ Util", 130)

-- Core PvP Function (auto-focus, aim, attack)
local attacking = false
function FocusAndAttack()
    spawn(function()
        while attacking do
            for _, player in pairs(Players:GetPlayers()) do
                if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") and not player.Character:FindFirstChild("ForceField") then
                    local lvl = tonumber(player:FindFirstChild("Data").Level.Value)
                    if lvl and lvl >= 1950 then
                        local enemyHRP = player.Character:WaitForChild("HumanoidRootPart")
                        workspace.CurrentCamera.CFrame = CFrame.new(workspace.CurrentCamera.CFrame.Position, enemyHRP.Position)
                        ReplicatedStorage.Remotes.Combat:FireServer(enemyHRP.Position)
                    end
                end
            end
            wait(1)
        end
    end)
end

PvPBtn.MouseButton1Click:Connect(function()
    attacking = not attacking
    if attacking then
        FocusAndAttack()
        PvPBtn.Text = "⚔️ PvP (ON)"
    else
        PvPBtn.Text = "⚔️ PvP (OFF)"
    end
end)

-- Teleport Functions
local ceaIDs = {
    [1] = 2753915549,
    [2] = 4442272183,
    [3] = 7449423635
}

local function teleportToCEA(id)
    TeleportService:Teleport(ceaIDs[id], LocalPlayer)
end

local CEA1 = createTab("Céa 1 (Pirate)", 170)
CEA1.MouseButton1Click:Connect(function()
    teleportToCEA(1)
end)

local CEA2 = createTab("Céa 2 (Cemetery)", 210)
CEA2.MouseButton1Click:Connect(function()
    teleportToCEA(2)
end)

local CEA3 = createTab("Céa 3 (Tree)", 250)
CEA3.MouseButton1Click:Connect(function()
    teleportToCEA(3)
end)

-- Auto-Roleta e Armazenamento (modo simples)
local function autoFruta()
    while wait(30) do
        local hasFruit = LocalPlayer.Backpack:FindFirstChild("Fruit")
        if hasFruit then
            local inv = LocalPlayer:FindFirstChild("Inventory")
            if not inv:FindFirstChild(hasFruit.Name) then
                ReplicatedStorage.Remotes.StoreFruit:InvokeServer(hasFruit)
            end
        end
    end
end

FruitBtn.MouseButton1Click:Connect(function()
    autoFruta()
end)

-- Auto-fuga se HP < 2000
UtilBtn.MouseButton1Click:Connect(function()
    spawn(function()
        while wait(3) do
            local char = LocalPlayer.Character
            if char and char:FindFirstChild("Humanoid") and char.Humanoid.Health < 2000 then
                local placeId = game.PlaceId
                if placeId == ceaIDs[1] then
                    -- Ilha segura CEA1
                    char:MoveTo(Vector3.new(1100, 50, 1500))
                elseif placeId == ceaIDs[2] then
                    -- Ilha segura CEA2
                    char:MoveTo(Vector3.new(-1400, 80, 600))
                elseif placeId == ceaIDs[3] then
                    -- Ilha segura CEA3
                    char:MoveTo(Vector3.new(2250, 25, -1200))
                end
            end
        end
    end)
end)

-- Fim do Script
