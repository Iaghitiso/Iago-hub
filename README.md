local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

-- Variáveis globais
local attacking = false
local target = nil
local minimized = false
local ScreenGui, MainFrame, MinimizeCircle

-- Criar GUI
local function CreateGUI()
    -- Remover GUI anterior se existir
    if game:GetService("CoreGui"):FindFirstChild("PvPGUI") then
        game:GetService("CoreGui"):FindFirstChild("PvPGUI"):Destroy()
    end
    
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "PvPGUI"
    ScreenGui.Parent = game:GetService("CoreGui")
    
    -- Frame principal (centralizado)
    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Size = UDim2.new(0, 250, 0, 180)
    MainFrame.Position = UDim2.new(0.5, -125, 0.5, -90) -- Centralizado
    MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    MainFrame.BorderSizePixel = 0
    MainFrame.Parent = ScreenGui
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 8)
    UICorner.Parent = MainFrame
    
    -- Barra de título
    local TitleBar = Instance.new("Frame")
    TitleBar.Name = "TitleBar"
    TitleBar.Size = UDim2.new(1, 0, 0, 30)
    TitleBar.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    TitleBar.BorderSizePixel = 0
    TitleBar.Parent = MainFrame
    
    local UICornerTitle = Instance.new("UICorner")
    UICornerTitle.CornerRadius = UDim.new(0, 8)
    UICornerTitle.Parent = TitleBar
    
    -- Correção para cantos da barra de título
    local FixCorners = Instance.new("Frame")
    FixCorners.Size = UDim2.new(1, 0, 0.5, 0)
    FixCorners.Position = UDim2.new(0, 0, 0.5, 0)
    FixCorners.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    FixCorners.BorderSizePixel = 0
    FixCorners.Parent = TitleBar
    
    -- Título
    local Title = Instance.new("TextLabel")
    Title.Name = "Title"
    Title.Size = UDim2.new(1, -60, 1, 0)
    Title.BackgroundTransparency = 1
    Title.Text = "PvP Controller"
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.TextSize = 16
    Title.Font = Enum.Font.SourceSansBold
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Position = UDim2.new(0, 10, 0, 0)
    Title.Parent = TitleBar
    
    -- Botão de fechar
    local CloseButton = Instance.new("TextButton")
    CloseButton.Name = "CloseButton"
    CloseButton.Size = UDim2.new(0, 30, 0, 30)
    CloseButton.Position = UDim2.new(1, -30, 0, 0)
    CloseButton.BackgroundTransparency = 1
    CloseButton.Text = "×"
    CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    CloseButton.TextSize = 20
    CloseButton.Font = Enum.Font.SourceSansBold
    CloseButton.Parent = TitleBar
    
    -- Botão de minimizar
    local MinimizeButton = Instance.new("TextButton")
    MinimizeButton.Name = "MinimizeButton"
    MinimizeButton.Size = UDim2.new(0, 30, 0, 30)
    MinimizeButton.Position = UDim2.new(1, -60, 0, 0)
    MinimizeButton.BackgroundTransparency = 1
    MinimizeButton.Text = "-"
    MinimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    MinimizeButton.TextSize = 20
    MinimizeButton.Font = Enum.Font.SourceSansBold
    MinimizeButton.Parent = TitleBar
    
    -- Botão PvP principal
    local PvPButton = Instance.new("TextButton")
    PvPButton.Name = "PvPButton"
    PvPButton.Size = UDim2.new(0.85, 0, 0, 40)
    PvPButton.Position = UDim2.new(0.5, -PvPButton.Size.X.Offset/2, 0, 45)
    PvPButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    PvPButton.BorderSizePixel = 0
    PvPButton.Text = "⚔️ PvP (OFF)"
    PvPButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    PvPButton.TextSize = 16
    PvPButton.Font = Enum.Font.SourceSansSemibold
    PvPButton.Parent = MainFrame
    
    local UICornerButton = Instance.new("UICorner")
    UICornerButton.CornerRadius = UDim.new(0, 6)
    UICornerButton.Parent = PvPButton
    
    -- Status label
    local StatusLabel = Instance.new("TextLabel")
    StatusLabel.Name = "StatusLabel"
    StatusLabel.Size = UDim2.new(0.85, 0, 0, 20)
    StatusLabel.Position = UDim2.new(0.5, -StatusLabel.Size.X.Offset/2, 0, 95)
    StatusLabel.BackgroundTransparency = 1
    StatusLabel.Text = "Aguardando..."
    StatusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    StatusLabel.TextSize = 14
    StatusLabel.Font = Enum.Font.SourceSans
    StatusLabel.Parent = MainFrame
    
    -- Configuração de nível
    local LevelLabel = Instance.new("TextLabel")
    LevelLabel.Name = "LevelLabel"
    LevelLabel.Size = UDim2.new(0.55, 0, 0, 20)
    LevelLabel.Position = UDim2.new(0.07, 0, 0, 125)
    LevelLabel.BackgroundTransparency = 1
    LevelLabel.Text = "Nível mínimo:"
    LevelLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    LevelLabel.TextSize = 14
    LevelLabel.TextXAlignment = Enum.TextXAlignment.Left
    LevelLabel.Font = Enum.Font.SourceSans
    LevelLabel.Parent = MainFrame
    
    local LevelValue = Instance.new("TextLabel")
    LevelValue.Name = "LevelValue"
    LevelValue.Size = UDim2.new(0.3, 0, 0, 20)
    LevelValue.Position = UDim2.new(0.63, 0, 0, 125)
    LevelValue.BackgroundTransparency = 1
    LevelValue.Text = "1950"
    LevelValue.TextColor3 = Color3.fromRGB(255, 255, 255)
    LevelValue.TextSize = 14
    LevelValue.TextXAlignment = Enum.TextXAlignment.Left
    LevelValue.Font = Enum.Font.SourceSansBold
    LevelValue.Parent = MainFrame
    
    -- Criar o círculo de minimização (inicialmente invisível)
    local MinimizeCircle = Instance.new("Frame")
    MinimizeCircle.Name = "MinimizeCircle"
    MinimizeCircle.Size = UDim2.new(0, 40, 0, 40)
    MinimizeCircle.Position = UDim2.new(0.1, 0, 0.5, 0)
    MinimizeCircle.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    MinimizeCircle.BorderSizePixel = 0
    MinimizeCircle.Visible = false
    MinimizeCircle.Parent = ScreenGui
    
    local CircleCorner = Instance.new("UICorner")
    CircleCorner.CornerRadius = UDim.new(1, 0)
    CircleCorner.Parent = MinimizeCircle
    
    local CircleIcon = Instance.new("TextLabel")
    CircleIcon.Name = "CircleIcon"
    CircleIcon.Size = UDim2.new(1, 0, 1, 0)
    CircleIcon.BackgroundTransparency = 1
    CircleIcon.Text = "⚔️"
    CircleIcon.TextColor3 = Color3.fromRGB(255, 255, 255)
    CircleIcon.TextSize = 20
    CircleIcon.Font = Enum.Font.SourceSansBold
    CircleIcon.Parent = MinimizeCircle
    
    -- Tornar os elementos arrastáveis
    local function makeDraggable(gui, handle)
        local dragging = false
        local dragStart, startPos
        
        handle.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                dragging = true
                dragStart = input.Position
                startPos = gui.Position
            end
        end)
        
        UserInputService.InputChanged:Connect(function(input)
            if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
                local delta = input.Position - dragStart
                gui.Position = UDim2.new(
                    startPos.X.Scale, 
                    startPos.X.Offset + delta.X,
                    startPos.Y.Scale, 
                    startPos.Y.Offset + delta.Y
                )
            end
        end)
        
        UserInputService.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                dragging = false
            end
        end)
    end
    
    makeDraggable(MainFrame, TitleBar)
    makeDraggable(MinimizeCircle, MinimizeCircle)
    
    -- Conectar eventos
    CloseButton.MouseButton1Click:Connect(function()
        ScreenGui:Destroy()
        attacking = false
    end)
    
    MinimizeButton.MouseButton1Click:Connect(function()
        minimized = true
        MainFrame.Visible = false
        MinimizeCircle.Visible = true
        MinimizeCircle.Position = MainFrame.Position
    end)
    
    MinimizeCircle.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            if minimized then
                -- Verifica se é apenas para arrastar ou para expandir
                local startTime = tick()
                
                MinimizeCircle.InputEnded:Connect(function(endInput)
                    if endInput.UserInputType == Enum.UserInputType.MouseButton1 then
                        local endTime = tick()
                        if endTime - startTime < 0.3 then -- Se for um clique rápido (menos de 0.3 segundos)
                            minimized = false
                            MainFrame.Visible = true
                            MinimizeCircle.Visible = false
                            MainFrame.Position = MinimizeCircle.Position
                        end
                    end
                end)
            end
        end
    end)
    
    PvPButton.MouseButton1Click:Connect(function()
        attacking = not attacking
        PvPButton.Text = attacking and "⚔️ PvP (ON)" or "⚔️ PvP (OFF)"
        PvPButton.BackgroundColor3 = attacking and Color3.fromRGB(70, 130, 180) or Color3.fromRGB(50, 50, 50)
        CircleIcon.TextColor3 = attacking and Color3.fromRGB(255, 100, 100) or Color3.fromRGB(255, 255, 255)
        StatusLabel.Text = attacking and "Buscando alvos..." or "Aguardando..."
        
        if attacking then 
            FocusCamera() 
        end
    end)
    
    return ScreenGui, MainFrame, MinimizeCircle, PvPButton, StatusLabel
end

-- Função para focar a câmera no inimigo sem mover o personagem
function FocusCamera()
    spawn(function()
        local StatusLabel = game:GetService("CoreGui"):FindFirstChild("PvPGUI").MainFrame.StatusLabel
        local camType = workspace.CurrentCamera.CameraType
        
        -- Salvar o tipo de câmera atual antes de alterar
        local originalCamType = workspace.CurrentCamera.CameraType
        
        while attacking do
            local closestDist = math.huge
            local cam = workspace.CurrentCamera
            target = nil
            
            pcall(function()
                -- Buscar inimigo mais próximo (lvl >= 1950)
                for _, player in pairs(Players:GetPlayers()) do
                    if player ~= LocalPlayer and 
                       player.Character and 
                       player.Character:FindFirstChild("HumanoidRootPart") and 
                       player:FindFirstChild("Data") and 
                       LocalPlayer.Character and 
                       LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                        
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
                
                -- Apenas focar a câmera no alvo, sem mover o personagem
                if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
                    local enemyHRP = target.Character.HumanoidRootPart
                    
                    -- Se o personagem do jogador existir
                    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                        -- Definir a câmera para scriptável para ter controle total
                        workspace.CurrentCamera.CameraType = Enum.CameraType.Scriptable
                        
                        -- Calcular posição da câmera (mantém a mesma distância do personagem)
                        local playerHRP = LocalPlayer.Character.HumanoidRootPart
                        local currentCamPos = cam.CFrame.Position
                        
                        -- Manter a mesma posição da câmera, apenas alterar a direção para olhar para o alvo
                        cam.CFrame = CFrame.new(currentCamPos, enemyHRP.Position)
                    end
                    
                    if StatusLabel and StatusLabel.Parent and StatusLabel.Parent.Visible then
                        StatusLabel.Text = "Alvo: " .. target.Name .. " [" .. math.floor(closestDist) .. "m]"
                    end
                else
                    if StatusLabel and StatusLabel.Parent and StatusLabel.Parent.Visible then
                        StatusLabel.Text = "Procurando alvos..."
                    end
                end
            end)
            
            wait(0.01) -- Intervalo reduzido para resposta mais rápida e fluidez da câmera
        end
        
        -- Restaurar a câmera para o tipo original quando desativar
        workspace.CurrentCamera.CameraType = originalCamType
    end)
end

-- Criar a GUI
ScreenGui, MainFrame, MinimizeCircle = CreateGUI()

-- Auto-reinício quando o personagem morre
LocalPlayer.CharacterAdded:Connect(function()
    if attacking then
        wait(1)
        FocusCamera()
    end
end)

print("PvP Script carregado com sucesso!")
print("Clique no botão PvP para ativar/desativar")
print("Use o botão de minimizar para transformar em círculo")
print("A câmera agora apenas foca no alvo sem mover seu personagem")
