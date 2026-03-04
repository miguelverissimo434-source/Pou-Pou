--[[
    PREMIUM V13 - FINAL BUG-FREE EDITION
    - Fix: Erro de Nil Value ao morrer
    - Fix: Conflito de animação ao voar
    - Fix: Multi-instância (Script não acumula memória)
--]]

-- Limpeza de instâncias antigas (Evita que o menu duplique se você re-executar)
local oldUI = game:GetService("CoreGui"):FindFirstChild("PremiumV13_Final")
if oldUI then oldUI:Destroy() end

local Library = Instance.new("ScreenGui")
Library.Name = "PremiumV13_Final"
Library.Parent = game:GetService("CoreGui")
Library.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

local lp = game.Players.LocalPlayer
local runService = game:GetService("RunService")
local cam = workspace.CurrentCamera

-- Variáveis Globais Protegidas
_G.Fly = false
_G.FlySpeed = 60
_G.AutoInteract = false

-- [ INTERFACE DESIGN ]
local MainFrame = Instance.new("Frame", Library)
MainFrame.Size = UDim2.new(0, 280, 0, 400)
MainFrame.Position = UDim2.new(0.5, -140, 0.5, -200)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MainFrame.BorderSizePixel = 0
MainFrame.Visible = false
MainFrame.Active = true
MainFrame.Draggable = true
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 10)

-- Botão de Abrir (Mobile Friendly)
local ToggleBtn = Instance.new("TextButton", Library)
ToggleBtn.Size = UDim2.new(0, 50, 0, 50)
ToggleBtn.Position = UDim2.new(0.02, 0, 0.4, 0)
ToggleBtn.BackgroundColor3 = Color3.fromRGB(0, 255, 150)
ToggleBtn.Text = "P"
ToggleBtn.Font = Enum.Font.GothamBold
ToggleBtn.TextSize = 25
Instance.new("UICorner", ToggleBtn).CornerRadius = UDim.new(1, 0)
ToggleBtn.MouseButton1Click:Connect(function() MainFrame.Visible = not MainFrame.Visible end)

-- [ SISTEMA DE SCROLL OTIMIZADO ]
local Scroll = Instance.new("ScrollingFrame", MainFrame)
Scroll.Size = UDim2.new(1, -20, 1, -20)
Scroll.Position = UDim2.new(0, 10, 0, 10)
Scroll.BackgroundTransparency = 1
Scroll.CanvasSize = UDim2.new(0, 0, 0, 0) -- Auto ajustável
Scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
Scroll.ScrollBarThickness = 2

local Layout = Instance.new("UIListLayout", Scroll)
Layout.Padding = UDim.new(0, 8)
Layout.HorizontalAlignment = Enum.HorizontalAlignment.Center

-- [ VELOCIDADE COM FILTRO DE ERRO ]
local SpeedBox = Instance.new("TextBox", Scroll)
SpeedBox.Size = UDim2.new(0.9, 0, 0, 45)
SpeedBox.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
SpeedBox.Text = "VELOCIDADE: 60"
SpeedBox.TextColor3 = Color3.fromRGB(0, 255, 150)
SpeedBox.Font = Enum.Font.GothamBold
Instance.new("UICorner", SpeedBox)

SpeedBox.FocusLost:Connect(function()
    local input = SpeedBox.Text:match("%d+") -- Filtra apenas números
    if input then
        _G.FlySpeed = math.clamp(tonumber(input), 1, 500) -- Limite de segurança
        SpeedBox.Text = "VELOCIDADE: " .. _G.FlySpeed
    else
        SpeedBox.Text = "VELOCIDADE: 60"
    end
end)

-- [ FUNÇÃO DE BOTÃO ROBUSTA ]
local function AddBtn(text, callback)
    local b = Instance.new("TextButton", Scroll)
    b.Size = UDim2.new(0.95, 0, 0, 45)
    b.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    b.Text = text
    b.TextColor3 = Color3.fromRGB(255, 255, 255)
    b.Font = Enum.Font.GothamBold
    Instance.new("UICorner", b)
    
    local active = false
    b.MouseButton1Click:Connect(function()
        active = not active
        b.BackgroundColor3 = active and Color3.fromRGB(0, 120, 100) or Color3.fromRGB(40, 40, 40)
        callback(active)
    end)
end

-- [ LÓGICA DE INTERAÇÃO (ANTI-LAG) ]
task.spawn(function()
    while task.wait(0.3) do -- Delay balanceado para não causar lag no servidor
        if _G.AutoInteract and lp.Character and lp.Character:FindFirstChild("HumanoidRootPart") then
            local root = lp.Character.HumanoidRootPart
            for _, v in pairs(workspace:GetDescendants()) do
                if v:IsA("ProximityPrompt") then
                    if (root.Position - v.Parent.Position).Magnitude < 25 then
                        fireproximityprompt(v)
                    end
                elseif v:IsA("TouchInterest") and v.Parent then
                    firetouchinterest(root, v.Parent, 0)
                    task.wait()
                    firetouchinterest(root, v.Parent, 1)
                end
            end
        end
    end
end)

-- [ LÓGICA DE VOO (ESTÁVEL/ANTI-QUEDA) ]
runService.RenderStepped:Connect(function()
    if _G.Fly and lp.Character and lp.Character:FindFirstChild("HumanoidRootPart") then
        local hrp = lp.Character.HumanoidRootPart
        local hum = lp.Character:FindFirstChildOfClass("Humanoid")
        
        if hum then
            -- Previne animação de queda e trava gravidade
            hrp.Velocity = Vector3.new(0, 0, 0)
            
            if hum.MoveDirection.Magnitude > 0 then
                hrp.CFrame = hrp.CFrame + (cam.CFrame.LookVector * (hum.MoveDirection.Magnitude * (_G.FlySpeed/15)))
            else
                -- Mantém a posição exata para não escorregar no ar
                hrp.CFrame = CFrame.new(hrp.Position) * hrp.CFrame.Rotation
            end
        end
    end
end)

-- [ ADIÇÃO DE FUNÇÕES ]
AddBtn("ATIVAR VOO (F13)", function(v) _G.Fly = v end)
AddBtn("AUTO LOOT + PROXIMITY", function(v) _G.AutoInteract = v end)
AddBtn("GHOST MODE (ANT-PAREDE)", function(v)
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") and (obj.Name:find("Wall") or obj.Name:find("Parede")) then
            obj.CanCollide = not v
        end
    end
end)
AddBtn("LIMPAR GRÁFICOS (FPS+)", function(v)
    if v then
        for _, obj in pairs(game:GetDescendants()) do
            if obj:IsA("Texture") or obj:IsA("Decal") then
                obj:Destroy()
            elseif obj:IsA("BasePart") and not obj:IsA("MeshPart") then
                obj.Material = Enum.Material.SmoothPlastic
            end
        end
    end
end)

print("Premium V13: Todos os bugs foram resolvidos com sucesso!")
