--[[
    Kaio Hub - Script UI Moderno para Roblox
    Criado do zero com design profissional
--]]

-- Serviços
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local VirtualUser = game:GetService("VirtualUser")

local Player = Players.LocalPlayer
local Character = Player.Character or Player.CharacterAdded:Wait()
local Mouse = Player:GetMouse()

-- Variáveis de estado
local PlayerSettings = {
    AutoFarm = false,
    AutoAttack = false,
    AutoMove = false,
    GodMode = false,
    NoClip = false,
    Speed = 16,
    JumpPower = 50
}

-- Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "KaioHubGUI"
ScreenGui.Parent = Player.PlayerGui
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- Criar botão toggle (abrir/fechar)
local ToggleButton = Instance.new("ImageButton")
ToggleButton.Name = "ToggleButton"
ToggleButton.Size = UDim2.new(0, 50, 0, 50)
ToggleButton.Position = UDim2.new(0, 20, 0.5, -25)
ToggleButton.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
ToggleButton.BackgroundTransparency = 0
ToggleButton.BorderSizePix = 0
ToggleButton.Image = "rbxassetid://3926305904"
ToggleButton.ImageColor3 = Color3.fromRGB(100, 100, 255)
ToggleButton.Parent = ScreenGui

-- Adicionar sombra ao botão toggle
local ToggleShadow = Instance.new("UICorner")
ToggleShadow.CornerRadius = UDim.new(0, 25)
ToggleShadow.Parent = ToggleButton

-- Criar UI principal
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 500, 0, 600)
MainFrame.Position = UDim2.new(0.5, -250, 0.5, -300)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
MainFrame.BackgroundTransparency = 1
MainFrame.BorderSizePix = 0
MainFrame.Parent = ScreenGui
MainFrame.Visible = false

-- Arredondar cantos do MainFrame
local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 12)
MainCorner.Parent = MainFrame

-- Adicionar sombra
local ShadowEffect = Instance.new("UICorner")
ShadowEffect.CornerRadius = UDim.new(0, 12)
ShadowEffect.Parent = MainFrame

-- Barra de título
local TitleBar = Instance.new("Frame")
TitleBar.Size = UDim2.new(1, 0, 0, 50)
TitleBar.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
TitleBar.BackgroundTransparency = 0
TitleBar.BorderSizePix = 0
TitleBar.Parent = MainFrame

local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, 12)
TitleCorner.Parent = TitleBar

local TitleLabel = Instance.new("TextLabel")
TitleLabel.Size = UDim2.new(1, -60, 1, 0)
TitleLabel.Position = UDim2.new(0, 20, 0, 0)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Text = "Kaio Hub"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.TextSize = 20
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.Parent = TitleBar

-- Botão fechar
local CloseButton = Instance.new("TextButton")
CloseButton.Size = UDim2.new(0, 30, 0, 30)
CloseButton.Position = UDim2.new(1, -40, 0, 10)
CloseButton.BackgroundColor3 = Color3.fromRGB(255, 80, 80)
CloseButton.BackgroundTransparency = 0.8
CloseButton.Text = "✕"
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.TextSize = 18
CloseButton.Font = Enum.Font.GothamBold
CloseButton.BorderSizePix = 0
CloseButton.Parent = TitleBar

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 8)
CloseCorner.Parent = CloseButton

-- Sistema de abas
local TabBar = Instance.new("Frame")
TabBar.Size = UDim2.new(1, 0, 0, 45)
TabBar.Position = UDim2.new(0, 0, 0, 50)
TabBar.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
TabBar.BackgroundTransparency = 0
TabBar.BorderSizePix = 0
TabBar.Parent = MainFrame

local TabContainer = Instance.new("Frame")
TabContainer.Size = UDim2.new(1, 0, 1, -95)
TabContainer.Position = UDim2.new(0, 0, 0, 95)
TabContainer.BackgroundTransparency = 1
TabContainer.Parent = MainFrame

-- Definição das abas
local Tabs = {
    {Name = "Farm", Index = 1},
    {Name = "Combate", Index = 2},
    {Name = "Teleporte", Index = 3},
    {Name = "Player", Index = 4},
    {Name = "Configurações", Index = 5}
}

local TabButtons = {}
local CurrentTab = 1

-- Função para criar abas
local function CreateTabButton(tabName, index)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0.2, 0, 1, 0)
    button.Position = UDim2.new(0.2 * (index - 1), 0, 0, 0)
    button.BackgroundColor3 = index == 1 and Color3.fromRGB(80, 80, 255) or Color3.fromRGB(40, 40, 55)
    button.BackgroundTransparency = 0
    button.Text = tabName
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.TextSize = 14
    button.Font = Enum.Font.GothamSemibold
    button.BorderSizePix = 0
    button.Parent = TabBar
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 8)
    corner.Parent = button
    
    button.MouseButton1Click:Connect(function()
        if CurrentTab == index then return end
        
        -- Atualizar estilo das abas
        for i, btn in pairs(TabButtons) do
            btn.BackgroundColor3 = Color3.fromRGB(40, 40, 55)
        end
        button.BackgroundColor3 = Color3.fromRGB(80, 80, 255)
        
        -- Mostrar conteúdo da aba
        for _, child in pairs(TabContainer:GetChildren()) do
            if child:IsA("Frame") and child.Name == "Tab" .. CurrentTab then
                child.Visible = false
            end
        end
        
        local targetTab = TabContainer:FindFirstChild("Tab" .. index)
        if targetTab then
            targetTab.Visible = true
        end
        
        CurrentTab = index
    end)
    
    return button
end

-- Criar conteúdo da aba Farm
local function CreateFarmTab()
    local farmTab = Instance.new("Frame")
    farmTab.Name = "Tab1"
    farmTab.Size = UDim2.new(1, 0, 1, 0)
    farmTab.BackgroundTransparency = 1
    farmTab.Parent = TabContainer
    
    local ScrollFrame = Instance.new("ScrollingFrame")
    ScrollFrame.Size = UDim2.new(1, -20, 1, -20)
    ScrollFrame.Position = UDim2.new(0, 10, 0, 10)
    ScrollFrame.BackgroundTransparency = 1
    ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, 300)
    ScrollFrame.ScrollBarThickness = 6
    ScrollFrame.Parent = farmTab
    
    local UIListLayout = Instance.new("UIListLayout")
    UIListLayout.Padding = UDim.new(0, 10)
    UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
    UIListLayout.Parent = ScrollFrame
    
    -- Auto Farm Toggle
    local autoFarmToggle = CreateToggle("Auto Farm", "Ativa/Desativa o farm automático", PlayerSettings.AutoFarm, function(value)
        PlayerSettings.AutoFarm = value
        if value then
            StartAutoFarm()
        else
            StopAutoFarm()
        end
    end, ScrollFrame)
    
    -- Seletor de tipo de farm
    local farmType = CreateDropdown("Tipo de Farm", {"Inimigos", "Recursos", "Experiência"}, 1, ScrollFrame)
end

-- Função para criar toggle
function CreateToggle(title, description, defaultValue, callback, parent)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -20, 0, 60)
    frame.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
    frame.BackgroundTransparency = 0
    frame.BorderSizePix = 0
    frame.Parent = parent
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 8)
    corner.Parent = frame
    
    local titleLabel = Instance.new("TextLabel")
    titleLabel.Size = UDim2.new(1, -70, 0, 25)
    titleLabel.Position = UDim2.new(0, 15, 0, 10)
    titleLabel.BackgroundTransparency = 1
    titleLabel.Text = title
    titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    titleLabel.TextSize = 16
    titleLabel.TextXAlignment = Enum.TextXAlignment.Left
    titleLabel.Font = Enum.Font.GothamSemibold
    titleLabel.Parent = frame
    
    local descLabel = Instance.new("TextLabel")
    descLabel.Size = UDim2.new(1, -70, 0, 20)
    descLabel.Position = UDim2.new(0, 15, 0, 35)
    descLabel.BackgroundTransparency = 1
    descLabel.Text = description
    descLabel.TextColor3 = Color3.fromRGB(150, 150, 170)
    descLabel.TextSize = 12
    descLabel.TextXAlignment = Enum.TextXAlignment.Left
    descLabel.Font = Enum.Font.Gotham
    descLabel.Parent = frame
    
    local toggleButton = Instance.new("TextButton")
    toggleButton.Size = UDim2.new(0, 50, 0, 30)
    toggleButton.Position = UDim2.new(1, -65, 0, 15)
    toggleButton.BackgroundColor3 = defaultValue and Color3.fromRGB(80, 80, 255) or Color3.fromRGB(60, 60, 75)
    toggleButton.Text = defaultValue and "ON" or "OFF"
    toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    toggleButton.TextSize = 12
    toggleButton.Font = Enum.Font.GothamBold
    toggleButton.BorderSizePix = 0
    toggleButton.Parent = frame
    
    local toggleCorner = Instance.new("UICorner")
    toggleCorner.CornerRadius = UDim.new(0, 6)
    toggleCorner.Parent = toggleButton
    
    local isOn = defaultValue
    
    toggleButton.MouseButton1Click:Connect(function()
        isOn = not isOn
        toggleButton.BackgroundColor3 = isOn and Color3.fromRGB(80, 80, 255) or Color3.fromRGB(60, 60, 75)
        toggleButton.Text = isOn and "ON" or "OFF"
        callback(isOn)
    end)
    
    return frame
end

-- Função para criar dropdown
function CreateDropdown(title, options, defaultIndex, parent)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -20, 0, 60)
    frame.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
    frame.BackgroundTransparency = 0
    frame.BorderSizePix = 0
    frame.Parent = parent
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 8)
    corner.Parent = frame
    
    local titleLabel = Instance.new("TextLabel")
    titleLabel.Size = UDim2.new(1, -20, 0, 25)
    titleLabel.Position = UDim2.new(0, 15, 0, 10)
    titleLabel.BackgroundTransparency = 1
    titleLabel.Text = title
    titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    titleLabel.TextSize = 14
    titleLabel.TextXAlignment = Enum.TextXAlignment.Left
    titleLabel.Font = Enum.Font.GothamSemibold
    titleLabel.Parent = frame
    
    local dropdownButton = Instance.new("TextButton")
    dropdownButton.Size = UDim2.new(0, 150, 0, 30)
    dropdownButton.Position = UDim2.new(0, 15, 0, 30)
    dropdownButton.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
    dropdownButton.Text = options[defaultIndex]
    dropdownButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    dropdownButton.TextSize = 14
    dropdownButton.Font = Enum.Font.Gotham
    dropdownButton.BorderSizePix = 0
    dropdownButton.Parent = frame
    
    local dropdownCorner = Instance.new("UICorner")
    dropdownCorner.CornerRadius = UDim.new(0, 6)
    dropdownCorner.Parent = dropdownButton
    
    return frame
end

-- Funções do Auto Farm
local farmConnection = nil

function StartAutoFarm()
    if farmConnection then return end
    
    farmConnection = RunService.Heartbeat:Connect(function()
        if not PlayerSettings.AutoFarm then return end
        
        -- Encontrar inimigos próximos
        local character = Player.Character
        if not character or not character:FindFirstChild("HumanoidRootPart") then return end
        
        local rootPart = character.HumanoidRootPart
        local nearestEnemy = nil
        local shortestDistance = 50 -- Raio de busca
        
        for _, enemy in pairs(game:GetService("Workspace"):GetDescendants()) do
            if enemy:IsA("Model") and enemy:FindFirstChild("Humanoid") and enemy ~= character then
                local humanoid = enemy:FindFirstChild("Humanoid")
                if humanoid and humanoid.Health > 0 then
                    local enemyRoot = enemy:FindFirstChild("HumanoidRootPart") or enemy:FindFirstChild("Torso")
                    if enemyRoot then
                        local distance = (rootPart.Position - enemyRoot.Position).Magnitude
                        if distance < shortestDistance then
                            shortestDistance = distance
                            nearestEnemy = enemy
                        end
                    end
                end
            end
        end
        
        if nearestEnemy then
            -- Mover até o inimigo
            local enemyRoot = nearestEnemy:FindFirstChild("HumanoidRootPart") or nearestEnemy:FindFirstChild("Torso")
            if enemyRoot and character:FindFirstChild("Humanoid") then
                local humanoid = character.Humanoid
                
                -- Movimento automático
                if shortestDistance > 5 then
                    humanoid:MoveTo(enemyRoot.Position)
                end
                
                -- Ataque automático
                if shortestDistance <= 8 then
                    local tool = character:FindFirstChildWhichIsA("Tool")
                    if tool then
                        tool:Activate()
                    end
                end
            end
        end
    end)
end

function StopAutoFarm()
    if farmConnection then
        farmConnection:Disconnect()
        farmConnection = nil
    end
end

-- Sistema de arrastar da UI
local dragging = false
local dragInput
local dragStart
local startPos

TitleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
        
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

TitleBar.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- Controles do botão toggle e fechar
local isOpen = false

ToggleButton.MouseButton1Click:Connect(function()
    isOpen = not isOpen
    MainFrame.Visible = isOpen
    
    -- Animação suave
    if isOpen then
        MainFrame.BackgroundTransparency = 0
        local tween = TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {BackgroundTransparency = 0})
        tween:Play()
    else
        local tween = TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {BackgroundTransparency = 1})
        tween:Play()
        task.wait(0.3)
        MainFrame.Visible = false
    end
end)

CloseButton.MouseButton1Click:Connect(function()
    isOpen = false
    local tween = TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {BackgroundTransparency = 1})
    tween:Play()
    task.wait(0.3)
    MainFrame.Visible = false
end)

-- Criar todas as abas
for _, tab in pairs(Tabs) do
    table.insert(TabButtons, CreateTabButton(tab.Name, tab.Index))
end

-- Criar conteúdo das abas
CreateFarmTab()
-- Adicione mais funções para as outras abas seguindo o mesmo padrão

-- Sistema de God Mode e No Clip (exemplo)
local function SetupCharacterChecks()
    Player.CharacterAdded:Connect(function(newCharacter)
        Character = newCharacter
        
        if PlayerSettings.GodMode then
            local humanoid = Character:FindFirstChild("Humanoid")
            if humanoid then
                humanoid.MaxHealth = math.huge
                humanoid.Health = math.huge
                humanoid.BreakJointsOnDeath = false
            end
        end
        
        if PlayerSettings.NoClip then
            for _, part in pairs(Character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = false
                end
            end
        end
    end)
end

-- Inicializar verificações
SetupCharacterChecks()

print("Kaio Hub carregado com sucesso!")
