--[[
  ✅ Script Blox Fruits Mobile - GUI Estilo RedzHub - by IS
  🔰 Otimizado para Delta Executor / Mobile
  ⚔️ PvP | 🍍 Frutas | 🌍 Teleporte entre CÉAs | 🛠️ Utilitários
--]]

local Players, ReplicatedStorage, TeleportService, LocalPlayer = game:GetService("Players"), game:GetService("ReplicatedStorage"), game:GetService("TeleportService"), game.Players.LocalPlayer

-- Gui Creation
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "IS_BloxGUI"

-- Minimized Button
local Minimized = Instance.new("TextButton", ScreenGui)
Minimized.Size, Minimized.Position, Minimized.Text = UDim2.new(0, 50, 0, 50), UDim2.new(0, 10, 0.4, 0), "IS"
Minimized.TextColor3, Minimized.BackgroundColor3, Minimized.BorderSizePixel = Color3.fromRGB(255, 255, 255), Color3.fromRGB(0, 0, 0), 2
Minimized.Name = "MinimizedBtn"

-- Rainbow Effect
spawn(function()
    local hue = 0
    while wait() do
        Minimized.BorderColor3 = Color3.fromHSV(hue/255, 1, 1)
        hue = (hue + 1) % 255
    end
end)

-- Main Frame
local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.BackgroundColor3, MainFrame.BorderColor3, MainFrame.Size, MainFrame.Position = Color3.fromRGB(0, 0, 0), Color3.fromRGB(0, 162, 255), UDim2.new(0, 300, 0, 400), UDim2.new(0, 70, 0.2, 0)
MainFrame.Visible = false

-- Toggle Menu
Minimized.MouseButton1Click:Connect(function() MainFrame.Visible = not MainFrame.Visible end)

-- Tab Buttons Creation
local function createTab(name, posY)
    local tab = Instance.new("TextButton", MainFrame)
    tab.Size, tab.Position, tab.Text, tab.BackgroundColor3, tab.TextColor3, tab.Font, tab.TextSize = UDim2.new(1, -10, 0, 30), UDim2.new(0, 5, 0, posY), name, Color3.fromRGB(0, 162, 255), Color3.fromRGB(255, 255, 255), Enum.Font.SourceSansBold, 20
    return tab
end

-- Tabs
local PvPBtn, FruitBtn, TPBtn, UtilBtn = createTab("⚔️ PvP", 10), createTab("🍍 Frutas", 50), createTab("🌍 Teleporte", 90), createTab("🛠️ Util", 130)

-- PvP Toggle
local attacking = false
function FocusAndAttack()
    spawn(function()
        while attacking do
            for _, player in pairs(Players:GetPlayers()) do
                if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                    local lvl = tonumber(player:FindFirstChild("Data") and player.Data.Level.Value)
                    if lvl and lvl >= 1950 then
                        workspace.CurrentCamera.CFrame = CFrame.new(workspace.CurrentCamera.CFrame.Position, player.Character.HumanoidRootPart.Position)
                        ReplicatedStorage.Remotes.Combat:FireServer(player.Character.HumanoidRootPart.Position)
                    end
                end
            end
            wait(1)
        end
    end)
end

PvPBtn.MouseButton1Click:Connect(function()
    attacking = not attacking
    PvPBtn.Text = attacking and "⚔️ PvP (ON)" or "⚔️ PvP (OFF)"
    if attacking then FocusAndAttack() end
end)

-- Teleport Functions
local ceaIDs = {2753915549, 4442272183, 7449423635}
local function teleportToCEA(id) TeleportService:Teleport(ceaIDs[id], LocalPlayer) end

createTab("Céa 1 (Pirate)", 170).MouseButton1Click:Connect(function() teleportToCEA(1) end)
createTab("Céa 2 (Cemetery)", 210).MouseButton1Click:Connect(function() teleportToCEA(2) end)
createTab("Céa 3 (Tree)", 250).MouseButton1Click:Connect(function() teleportToCEA(3) end)

-- Auto-Roleta e Armazenamento
local function autoFruta()
    while wait(30) do
        local fruit = LocalPlayer.Backpack:FindFirstChild("Fruit")
        if fruit and not LocalPlayer.Inventory:FindFirstChild(fruit.Name) then
            ReplicatedStorage.Remotes.StoreFruit:InvokeServer(fruit)
        end
    end
end

FruitBtn.MouseButton1Click:Connect(autoFruta)

-- Auto-fuga se HP < 2000
UtilBtn.MouseButton1Click:Connect(function()
    spawn(function()
        while wait(3) do
            local char = LocalPlayer.Character
            if char and char:FindFirstChild("Humanoid") and char.Humanoid.Health < 2000 then
                local pos = {
                    [ceaIDs[1]] = Vector3.new(1100, 50, 1500),
                    [ceaIDs[2]] = Vector3.new(-1400, 80, 600),
                    [ceaIDs[3]] = Vector3.new(2250, 25, -1200)
                }
                char:MoveTo(pos[game.PlaceId] or char.HumanoidRootPart.Position)
            end
        end
    end)
end)

-- Fim do Script
