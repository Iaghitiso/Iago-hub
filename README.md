-- Criação do painel
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = game.Players.LocalPlayer.PlayerGui

-- Criando o painel principal
local panel = Instance.new("Frame")
panel.Size = UDim2.new(0, 300, 0, 500)
panel.Position = UDim2.new(1, -320, 1, -520)
panel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
panel.BorderSizePixel = 2
panel.BorderColor3 = Color3.fromRGB(0, 0, 255)  -- Azul
panel.Parent = ScreenGui

-- Barra de título
local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 30)
titleBar.BackgroundColor3 = Color3.fromRGB(0, 0, 100)
titleBar.Parent = panel

local titleText = Instance.new("TextLabel")
titleText.Size = UDim2.new(1, 0, 1, 0)
titleText.BackgroundTransparency = 1
titleText.Text = "Painel IS"
titleText.TextColor3 = Color3.fromRGB(255, 255, 255)
titleText.TextSize = 20
titleText.Parent = titleBar

-- Ícone de minimizar (quadrado redondo com "IS")
local minimizeButton = Instance.new("TextButton")
minimizeButton.Size = UDim2.new(0, 50, 0, 50)
minimizeButton.Position = UDim2.new(1, -60, 0, 0)
minimizeButton.Text = "IS"
minimizeButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
minimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
minimizeButton.TextSize = 16
minimizeButton.Font = Enum.Font.SourceSansBold
minimizeButton.BackgroundTransparency = 0.3
minimizeButton.AutoButtonColor = false
minimizeButton.ClipsDescendants = true
minimizeButton.Parent = titleBar

-- Estilo arco-íris no ícone
local function rainbowEffect(button)
    local function setRainbowColor()
        local color = Color3.fromHSV(tick() % 5 / 5, 1, 1) -- Cores arco-íris
        button.BackgroundColor3 = color
    end

    game:GetService("RunService").Heartbeat:Connect(setRainbowColor)
end

rainbowEffect(minimizeButton)

-- Função de minimizar
local function minimizePanel()
    panel.Visible = false
    local icon = Instance.new("TextButton")
    icon.Size = UDim2.new(0, 50, 0, 50)
    icon.Position = UDim2.new(1, -60, 1, -60)
    icon.Text = "IS"
    icon.BackgroundColor3 = Color3.fromRGB(255, 0, 255)
    icon.TextColor3 = Color3.fromRGB(255, 255, 255)
    icon.TextSize = 16
    icon.Font = Enum.Font.SourceSansBold
    icon.BackgroundTransparency = 0.3
    icon.AutoButtonColor = false
    icon.ClipsDescendants = true
    icon.Parent = ScreenGui

    -- Estilo arco-íris no ícone de minimizar
    rainbowEffect(icon)

    -- Ao clicar no ícone de "IS", o painel expande novamente
    icon.MouseButton1Click:Connect(function()
        panel.Visible = true
        icon:Destroy()
    end)
end

minimizeButton.MouseButton1Click:Connect(minimizePanel)

-- Botões dentro do painel
local pvpButton = Instance.new("TextButton")
pvpButton.Size = UDim2.new(0, 200, 0, 50)
pvpButton.Position = UDim2.new(0, 50, 0, 70)
pvpButton.Text = "Ativar PvP"
pvpButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
pvpButton.TextColor3 = Color3.fromRGB(255, 255, 255)
pvpButton.Parent = panel

-- Função para alternar o estado do PvP
pvpButton.MouseButton1Click:Connect(function()
    if pvpButton.Text == "Ativar PvP" then
        pvpButton.Text = "Desativar PvP"
        -- Ativar PvP aqui
    else
        pvpButton.Text = "Ativar PvP"
        -- Desativar PvP aqui
    end
end)

-- Botões de teleporte
local teleportCea1 = Instance.new("TextButton")
teleportCea1.Size = UDim2.new(0, 200, 0, 50)
teleportCea1.Position = UDim2.new(0, 50, 0, 150)
teleportCea1.Text = "Céa 1 (Pirate)"
teleportCea1.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
teleportCea1.TextColor3 = Color3.fromRGB(255, 255, 255)
teleportCea1.Parent = panel

teleportCea1.MouseButton1Click:Connect(function()
    -- Lógica de teleporte para Céa 1 (Pirate Island)
end)

-- Similar para Céa 2 e 3 (Cemetery, Great Tree)

-- Funções de roletar e armazenar frutas
-- Similar aos botões de teleporte e PvP
