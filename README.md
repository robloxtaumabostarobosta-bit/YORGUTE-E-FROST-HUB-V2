local SINTONIA_ID = 115054138215106 -- ID oficial do Sintonia RP no Roblox
if game.PlaceId ~= SINTONIA_ID then 
    warn("Acesso Negado: Script exclusivo para Sintonia RP.")
    return 
end

-- Serviços Principais
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()
local Camera = workspace.CurrentCamera

-- Função para gerar nomes aleatórios (Anti-Detection)
local function RandomName()
    local chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
    local name = ""
    for i = 1, 15 do
        local r = math.random(1, #chars)
        name = name .. string.sub(chars, r, r)
    end
    return name
end

-- [[ INTERFACE CUSTOMIZADA ]]
local Screen = Instance.new("ScreenGui", CoreGui)
Screen.Name = RandomName()

local Main = Instance.new("Frame", Screen)
Main.Name = RandomName()
Main.Size = UDim2.new(0, 450, 0, 380)
Main.Position = UDim2.new(0.5, -225, 0.5, -190)
Main.BackgroundColor3 = Color3.fromRGB(15, 10, 25)
Main.BorderSizePixel = 0
Main.ClipsDescendants = true

local Stroke = Instance.new("UIStroke", Main)
Stroke.Color = Color3.fromRGB(138, 43, 226)
Stroke.Thickness = 2
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 10)

-- Barra de Título (Arrastável)
local TitleBar = Instance.new("Frame", Main)
TitleBar.Size = UDim2.new(1, 0, 0, 40)
TitleBar.BackgroundTransparency = 1

local Title = Instance.new("TextLabel", TitleBar)
Title.Size = UDim2.new(1, 0, 1, 0)
Title.Text = "YORGUTE FROST HUB SCRIPT"
Title.TextColor3 = Color3.fromRGB(200, 200, 200)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16
Title.BackgroundTransparency = 1

-- Botões de Controle
local Close = Instance.new("TextButton", Main)
Close.Size = UDim2.new(0, 25, 0, 25)
Close.Position = UDim2.new(1, -35, 0, 8)
Close.Text = "X"
Close.BackgroundColor3 = Color3.fromRGB(255, 60, 60)
Close.TextColor3 = Color3.fromRGB(255, 255, 255)
Instance.new("UICorner", Close).CornerRadius = UDim.new(1, 0)

local Min = Instance.new("TextButton", Main)
Min.Size = UDim2.new(0, 25, 0, 25)
Min.Position = UDim2.new(1, -65, 0, 8)
Min.Text = "-"
Min.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
Min.TextColor3 = Color3.fromRGB(255, 255, 255)
Instance.new("UICorner", Min).CornerRadius = UDim.new(1, 0)

-- [[ FUNÇÕES TÉCNICAS ]]
local Config = {
    Aimbot = false,
    SilentAim = false,
    AimFOV = 100,
    ShowFOV = false,
    Speed = 16
}

-- Círculo de FOV (Drawing API)
local FOVCircle = Drawing.new("Circle")
FOVCircle.Thickness = 1
FOVCircle.Color = Color3.fromRGB(138, 43, 226)
FOVCircle.Filled = false
FOVCircle.Visible = false

-- Lógica de Aimbot/Silent
local function GetClosestPlayer()
    local target = nil
    local dist = Config.AimFOV
    for _, v in pairs(Players:GetPlayers()) do
        if v ~= LocalPlayer and v.Character and v.Character:FindFirstChild("Head") then
            local pos, onScreen = Camera:WorldToViewportPoint(v.Character.Head.Position)
            if onScreen then
                local magnitude = (Vector2.new(pos.X, pos.Y) - Vector2.new(Mouse.X, Mouse.Y)).Magnitude
                if magnitude < dist then
                    dist = magnitude
                    target = v
                end
            end
        end
    end
    return target
end

-- [[ ABAS E CONTEÚDO ]]
local Content = Instance.new("ScrollingFrame", Main)
Content.Size = UDim2.new(1, -20, 1, -60)
Content.Position = UDim2.new(0, 10, 0, 50)
Content.BackgroundTransparency = 1
Content.ScrollBarThickness = 2
local Layout = Instance.new("UIListLayout", Content)
Layout.Padding = UDim.new(0, 8)

local function AddButton(text, callback)
    local b = Instance.new("TextButton", Content)
    b.Size = UDim2.new(1, 0, 0, 45)
    b.BackgroundColor3 = Color3.fromRGB(110, 30, 220)
    b.Text = text
    b.Font = Enum.Font.GothamSemibold
    b.TextColor3 = Color3.fromRGB(255, 255, 255)
    b.TextSize = 14
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 8)
    b.MouseButton1Click:Connect(callback)
end

-- Adicionando as Funções
AddButton("ESP Player", function() print("ESP Ativado") end)
AddButton("Aimbot (Toggle)", function() Config.Aimbot = not Config.Aimbot end)
AddButton("Silent Aim (Toggle)", function() Config.SilentAim = not Config.SilentAim end)
AddButton("Mostrar FOV Circle", function() Config.ShowFOV = not Config.ShowFOV end)
AddButton("Speed Hack (Ativar)", function() Config.Speed = 60 end)

-- [[ LOOPS DE EXECUÇÃO ]]
RunService.RenderStepped:Connect(function()
    -- Update FOV Circle
    FOVCircle.Visible = Config.ShowFOV
    FOVCircle.Position = Vector2.new(Mouse.X, Mouse.Y + 36)
    FOVCircle.Radius = Config.AimFOV

    -- Aimbot Logic
    if Config.Aimbot then
        local target = GetClosestPlayer()
        if target then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Character.Head.Position)
        end
    end

    -- Speed Hack Logic
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = Config.Speed
    end
end)

-- [[ ARRASTE, MINIMIZAR E FECHAR ]]
local dragging, dragInput, dragStart, startPos
TitleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true dragStart = input.Position startPos = Main.Position
    end
end)
UIS.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        Main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)
UIS.InputEnded:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end end)

Close.MouseButton1Click:Connect(function() Screen:Destroy() FOVCircle:Remove() end)
Min.MouseButton1Click:Connect(function()
    Content.Visible = not Content.Visible
    Main:TweenSize(Content.Visible and UDim2.new(0, 450, 0, 380) or UDim2.new(0, 450, 0, 40), "Out", "Quart", 0.3, true)
end)

print("Yorgute Frost Hub carregado com sucesso!")
