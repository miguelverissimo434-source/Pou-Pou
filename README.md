--[[ 
    FTF ELITE V9.0 - COMPETITIVE EDITION
    - Insta-Hack & Fast Action
    - Speed Boost
    - Anti-Camera Shake
    - ESP Pro
]]

local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
local Main = Instance.new("Frame", ScreenGui)
local ScrollingFrame = Instance.new("ScrollingFrame", Main)
local UIListLayout = Instance.new("UIListLayout", ScrollingFrame)
local Title = Instance.new("TextLabel", Main)

-- Design Moderno Carbono
Main.Size = UDim2.new(0, 230, 0, 350)
Main.Position = UDim2.new(0.05, 0, 0.3, 0)
Main.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
Main.BorderSizePixel = 0
Main.Active = true
Main.Draggable = true
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 10)

local Stroke = Instance.new("UIStroke", Main)
Stroke.Thickness = 2
Stroke.Color = Color3.fromRGB(255, 50, 50) -- Cor de alerta/besta

Title.Size = UDim2.new(1, 0, 0, 45)
Title.Text = "FTF ELITE V9.0"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16
Title.BackgroundTransparency = 1

ScrollingFrame.Size = UDim2.new(1, -20, 1, -65)
ScrollingFrame.Position = UDim2.new(0, 10, 0, 55)
ScrollingFrame.BackgroundTransparency = 1
ScrollingFrame.ScrollBarThickness = 0
UIListLayout.Padding = UDim.new(0, 8)

_G.Config = {
    Pessoas = false,
    Besta = false,
    PCs = false,
    Speed = false,
    InstaInteracao = false,
    NoShake = false
}

local function NewButton(txt, var)
    local b = Instance.new("TextButton", ScrollingFrame)
    b.Size = UDim2.new(1, 0, 0, 45)
    b.Text = txt
    b.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    b.TextColor3 = Color3.fromRGB(200, 200, 200)
    b.Font = Enum.Font.GothamSemibold
    b.TextSize = 13
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 8)

    b.MouseButton1Click:Connect(function()
        _G.Config[var] = not _G.Config[var]
        b.BackgroundColor3 = _G.Config[var] and Color3.fromRGB(255, 50, 50) or Color3.fromRGB(35, 35, 35)
        b.TextColor3 = Color3.new(1, 1, 1)
    end)
end

NewButton("👥 ESP: Jogadores", "Pessoas")
NewButton("👹 ESP: Besta", "Besta")
NewButton("💻 ESP: Computadores", "PCs")
NewButton("⚡ Velocidade (Speed)", "Speed")
NewButton("⚙️ Interação Instantânea", "InstaInteracao")
NewButton("🚫 Sem Tremor de Câmera", "NoShake")

-- LOOP DE VELOCIDADE
task.spawn(function()
    while task.wait() do
        if _G.Config.Speed then
            pcall(function()
                game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = 24 -- Velocidade otimizada para não ser kickado
            end)
        else
            pcall(function()
                game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = 16
            end)
        end
    end
end)

-- INTERAÇÃO INSTANTÂNEA (COMPUTADORES E PORTAS)
task.spawn(function()
    while task.wait(0.1) do
        if _G.Config.InstaInteracao then
            pcall(function()
                for _, v in pairs(workspace:GetDescendants()) do
                    if v:IsA("ProximityPrompt") then
                        v.HoldDuration = 0 -- Remove o tempo de segurar
                    end
                end
            end)
        end
    end
end)

-- ANTI CAMERA SHAKE (ESTABILIZADOR)
task.spawn(function()
    game:GetService("RunService").RenderStepped:Connect(function()
        if _G.Config.NoShake then
            pcall(function()
                local cam = workspace.CurrentCamera
                -- Tenta neutralizar o tremor forçado pelo jogo
                if cam:FindFirstChild("Shake") then cam.Shake:Destroy() end
            end)
        end
    end)
end)

-- ESP SISTEMA (SEM LAG)
task.spawn(function()
    while task.wait(1) do
        pcall(function()
            -- PCs
            for _, v in pairs(workspace:GetDescendants()) do
                if v.Name == "ComputerTable" then
                    local h = v:FindFirstChild("Highlight") or Instance.new("Highlight", v)
                    h.Enabled = _G.Config.PCs
                    h.FillColor = Color3.fromRGB(0, 255, 100)
                end
            end
            -- Players
            for _, p in pairs(game.Players:GetPlayers()) do
                if p ~= game.Players.LocalPlayer and p.Character then
                    local isBeast = p.Character:FindFirstChild("Hammer")
                    local h = p.Character:FindFirstChild("Highlight") or Instance.new("Highlight", p.Character)
                    h.Enabled = isBeast and _G.Config.Besta or _G.Config.Pessoas
                    h.FillColor = isBeast and Color3.new(1, 0, 0) or Color3.new(1, 1, 1)
                end
            end
        end)
    end
end)

-- Botão de Abrir/Fechar
local T = Instance.new("TextButton", ScreenGui)
T.Size = UDim2.new(0, 70, 0, 30)
T.Position = UDim2.new(0.4, 0, 0.02, 0)
T.Text = "ELITE"
T.BackgroundColor3 = Color3.new(0,0,0)
T.TextColor3 = Color3.new(1,1,1)
T.Font = Enum.Font.GothamBold
Instance.new("UICorner", T)
T.MouseButton1Click:Connect(function() Main.Visible = not Main.Visible end)
