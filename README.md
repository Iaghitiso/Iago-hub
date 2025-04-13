-- Script PvP Otimizado para DeltaExploit
-- Serviços necessários
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")

-- Variáveis globais
local attacking = false
local target = nil

-- Criar GUI minimalista e otimizada
local function CreateGUI()
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "PvPGUI"
    ScreenGui.Parent = game:GetService("CoreGui")
    
    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Size = UDim2.new(0, 200, 0, 120)
    MainFrame.Position = UDim2.new(0.8, 0, 0.5, -60)
    MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
    MainFrame.BorderSizePixel = 0
    MainFrame.Parent = ScreenGui
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 6)
    UICorner.Parent = MainFrame
    
    local TitleBar = Instance.new("Frame")
    TitleBar.Name = "TitleBar"
    TitleBar.Size = UDim2.new(1, 0, 0, 25)
    TitleBar.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    TitleBar.BorderSizePixel = 0
    TitleBar.Parent = MainFrame
    
    local UICornerTitle = Instance.new("UICorner")
    UICornerTitle.CornerRadius = UDim.new(0, 6)
    UICornerTitle.Parent = TitleBar
    
    local fix = Instance.new("Frame")
    fix.Size = UDim2.new(1, 0, 0, 10)
    fix.Position = UDim2.new(0, 0, 1, -10)
    fix.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    fix.BorderSizePixel = 0
    fix.Parent = TitleBar
    
    local Title = Instance.new("TextLabel")
    Title.Name = "Title"
    Title.Size = UDim2.new(1, -25, 1, 0)
    Title.BackgroundTransparency = 1
    Title.Text = "PvP Controller"
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.TextSize = 14
    Title.Font = Enum.Font.SourceSansBold
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Position = UDim2.new(0, 10, 0, 0)
    Title.Parent = TitleBar
    
    local CloseButton = Instance.new("TextButton")
    CloseButton.Name = "CloseButton"
    CloseButton.Size = UDim2.new(0, 25, 0, 25)
    CloseButton.Position = UDim2.new(1, -25, 0, 0)
    CloseButton.BackgroundTransparency = 1
    CloseButton.Text = "×"
    CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    CloseButton.TextSize = 18
    CloseButton.Font = Enum.Font.SourceSansBold
    CloseButton.Parent = TitleBar
    
    local PvPButton = Instance.new("TextButton")
    PvPButton.Name = "PvPButton"
    PvPButton.Size = UDim2.new(0.9, 0, 0, 30)
    PvPButton.Position = UDim2.new(0.05, 0, 0.5, -15)
    PvPButton.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    PvPButton.BorderSizePixel = 0
    PvPButton.Text = "⚔️ PvP (OFF)"
    PvPButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    PvPButton.TextSize = 14
    PvPButton.Font = Enum.Font.SourceSansSemibold
    PvPButton.Parent = MainFrame
    
    local UICornerButton = Instance.new("UICorner")
    UICornerButton.CornerRadius = UDim.new(0, 4)
    UICornerButton.Parent = PvPButton
    
    local StatusLabel = Instance.new("TextLabel")
    StatusLabel.Name = "StatusLabel"
    StatusLabel.Size = UDim2.new(0.9, 0, 0, 20)
    StatusLabel.Position = UDim2.new(0.05, 0, 0.8, -10)
    StatusLabel.BackgroundTransparency = 1
    StatusLabel.Text = "Aguardando..."
    StatusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    StatusLabel.TextSize = 12
    StatusLabel.Font = Enum.Font.SourceSans
    StatusLabel.Parent = MainFrame
    
    -- Tornar a GUI arrastável
    local Dragging = false
    local DragStart = nil
    local StartPosition = nil
    
    TitleBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            Dragging = true
            DragStart = input.Position
            StartPosition = MainFrame.Position
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement and Dragging then
            local Delta = input.Position - DragStart
            MainFrame.Position = UDim2.new(
                StartPosition.X.Scale, 
                StartPosition.X.Offset + Delta.X,
                StartPosition.Y.Scale, 
                StartPosition.Y.Offset + Delta.Y
            )
        end
    end)
    
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            Dragging = false
        end
    end)
    
    -- Conectar botão PvP
    PvPButton.MouseButton1Click:Connect(function()
        attacking = not attacking
        PvPButton.Text = attacking and "⚔️ PvP (ON)" or "⚔️ PvP (OFF)"
        PvPButton.BackgroundColor3 = attacking and Color3.fromRGB(70, 130, 180) or Color3.fromRGB(45, 45, 45)
        StatusLabel.Text = attacking and "Buscando alvos..." or "Aguardando..."
        
        if attacking then 
            LockAndFollow() 
        end
    end)
    
    CloseButton.MouseButton1Click:Connect(function()
        ScreenGui:Destroy()
        attacking = false
    end)
    
    return ScreenGui, PvPButton, StatusLabel
end

-- Função principal de Lock e Follow
function LockAndFollow()
    spawn(function()
        local StatusLabel = game:GetService("CoreGui"):FindFirstChild("PvPGUI").MainFrame.StatusLabel
        
        while attacking do
            local closestDist = math.huge
            local cam = workspace.CurrentCamera
            target = nil
            
            pcall(function()
                -- Buscar inimigo mais próximo (lvl >= 1950)
                for _, player in pairs(Players:GetPlayers()) do
                    if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") and 
                       player:FindFirstChild("Data") and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                        
                        local lvl = tonumber(player.Data.Level.Value)
                        if lvl and lvl >= 1950 and not player.Character:FindFirstChild("ForceField") then
                            local dist = (player.Character.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
                            if dist < closestDist and dist < 300 then
                                closestDist = dist
                                target = player
                            end
                        end
                    end
                end
                
                -- Lockar câmera e seguir
                if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
                    local enemyHRP = target.Character.HumanoidRootPart
                    cam.CFrame = CFrame.new(cam.CFrame.Position, enemyHRP.Position) -- Lock câmera
                    LocalPlayer.Character:MoveTo(enemyHRP.Position + Vector3.new(0, 0, -4)) -- Segue
                    
                    if StatusLabel then
                        StatusLabel.Text = "Alvo: " .. target.Name .. " [" .. math.floor(closestDist) .. "m]"
                    end
                else
                    if StatusLabel then
                        StatusLabel.Text = "Procurando alvos..."
                    end
                end
            end)
            
            wait(0.1) -- Otimizado: intervalo reduzido para resposta mais rápida
        end
    end)
end

-- Criar a GUI
local GUI, PvPButton, StatusLabel = CreateGUI()

-- Opcional: Auto-reinício quando o personagem morre
LocalPlayer.CharacterAdded:Connect(function()
    if attacking then
        wait(1)
        LockAndFollow()
    end
end)

print("PvP Script carregado com sucesso!")
