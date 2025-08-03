-- LocalScript dentro de StarterGui
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer

-- Jogadores autorizados
local autorizados = {
    ["SeuNomeAqui"] = true -- substitua pelo seu nome de usuário
}

if not autorizados[player.Name] then return end

-- Criar GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ModMenu"
screenGui.Parent = player:WaitForChild("PlayerGui")

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 200, 0, 140)
frame.Position = UDim2.new(0, 20, 0, 100)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 0
frame.Parent = screenGui

-- Função utilitária para criar botões
local function criarBotao(texto, posY)
    local botao = Instance.new("TextButton")
    botao.Size = UDim2.new(1, -20, 0, 40)
    botao.Position = UDim2.new(0, 10, 0, posY)
    botao.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    botao.TextColor3 = Color3.new(1, 1, 1)
    botao.Font = Enum.Font.Gotham
    botao.TextSize = 18
    botao.Text = texto
    botao.Parent = frame
    return botao
end

-- SUPER PULO
local superPuloBotao = criarBotao("Super Pulo", 10)
superPuloBotao.MouseButton1Click:Connect(function()
    if player.Character and player.Character:FindFirstChild("Humanoid") then
        player.Character.Humanoid.JumpPower = 150
    end
end)

-- VOO
local voando = false
local velocidade = 60
local direcao = Vector3.new()

local function iniciarVoo()
    voando = true
    local hrp = player.Character:WaitForChild("HumanoidRootPart")
    local bv = Instance.new("BodyVelocity")
    bv.Name = "FlyVelocity"
    bv.MaxForce = Vector3.new(1, 1, 1) * math.huge
    bv.Velocity = Vector3.zero
    bv.Parent = hrp

    RunService:BindToRenderStep("ControleDeVoo", Enum.RenderPriority.Character.Value, function()
        if not voando then return end
        bv.Velocity = direcao * velocidade
    end)
end

local function pararVoo()
    voando = false
    RunService:UnbindFromRenderStep("ControleDeVoo")
    local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
    if hrp and hrp:FindFirstChild("FlyVelocity") then
        hrp.FlyVelocity:Destroy()
    end
end

-- Captura de teclas para controlar voo
UIS.InputBegan:Connect(function(input, gpe)
    if gpe then return end
    if not voando then return end

    if input.KeyCode == Enum.KeyCode.W then
        direcao = Vector3.new(0, 0, -1)
    elseif input.KeyCode == Enum.KeyCode.S then
        direcao = Vector3.new(0, 0, 1)
    elseif input.KeyCode == Enum.KeyCode.A then
        direcao = Vector3.new(-1, 0, 0)
    elseif input.KeyCode == Enum.KeyCode.D then
        direcao = Vector3.new(1, 0, 0)
    elseif input.KeyCode == Enum.KeyCode.Space then
        direcao = Vector3.new(0, 1, 0)
    end
end)

UIS.InputEnded:Connect(function(input)
    if not voando then return end
    direcao = Vector3.new()
end)

-- BOTÃO DE VOAR
local voarBotao = criarBotao("Ativar/Desativar Voo", 60)
voarBotao.MouseButton1Click:Connect(function()
    if voando then
        pararVoo()
    else
        iniciarVoo()
    end
end)
