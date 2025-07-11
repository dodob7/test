--[[
Script Roblox Local Lua: Aimbot + Highlight Vermelho
Quando o botão aparecer e for ativado, todos os jogadores ficam vermelhos e a mira puxa para a cabeça do jogador mais próximo.

**Comandos:**
- O botão aparece no canto da tela.
- Ao clicar, ativa/desativa a função.

**IMPORTANTE:**  
Esse script é para fins educacionais. Não use em jogos sem permissão do criador. Pode violar os Termos de Serviço do Roblox.

]]

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

-- UI Button
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local Button = Instance.new("TextButton")
Button.Size = UDim2.new(0, 120, 0, 40)
Button.Position = UDim2.new(0.01, 0, 0.85, 0)
Button.Text = "Aimbot Vermelho [OFF]"
Button.BackgroundColor3 = Color3.new(1,0,0)
Button.TextColor3 = Color3.new(1,1,1)
Button.Parent = ScreenGui

local active = false
local highlights = {}

-- Função para destacar todos jogadores
local function highlightAll()
    for _,plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("Head") then
            if not highlights[plr] then
                local hl = Instance.new("Highlight")
                hl.FillColor = Color3.new(1,0,0)
                hl.OutlineColor = Color3.new(1,0,0)
                hl.Adornee = plr.Character
                hl.Parent = plr.Character
                highlights[plr] = hl
            end
        end
    end
end

-- Remover destaque
local function unhighlightAll()
    for plr,hl in pairs(highlights) do
        if hl and hl.Parent then
            hl:Destroy()
        end
        highlights[plr] = nil
    end
end

-- Função para encontrar jogador mais próximo
local function getNearestPlayer()
    local closestPlr = nil
    local closestDist = math.huge
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("Head") then return nil end
    local myHead = char.Head.Position
    for _,plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("Head") then
            local dist = (plr.Character.Head.Position - myHead).Magnitude
            if dist < closestDist then
                closestDist = dist
                closestPlr = plr
            end
        end
    end
    return closestPlr
end

-- Aimbot função
local function aimAtHead()
    local mouse = LocalPlayer:GetMouse()
    local plr = getNearestPlayer()
    if plr and plr.Character and plr.Character:FindFirstChild("Head") then
        local headPos = plr.Character.Head.Position
        local camera = workspace.CurrentCamera
        camera.CFrame = CFrame.new(camera.CFrame.Position, headPos)
    end
end

-- Toggle Button
Button.MouseButton1Click:Connect(function()
    active = not active
    if active then
        Button.Text = "Aimbot Vermelho [ON]"
        highlightAll()
    else
        Button.Text = "Aimbot Vermelho [OFF]"
        unhighlightAll()
    end
end)

-- Aimbot Loop
RunService.RenderStepped:Connect(function()
    if active then
        aimAtHead()
    end
end)

-- Atualizar highlights se jogadores mudarem
Players.PlayerAdded:Connect(function(plr)
    plr.CharacterAdded:Connect(function()
        if active then
            highlightAll()
        end
    end)
end)

Players.PlayerRemoving:Connect(function(plr)
    if highlights[plr] then
        highlights[plr]:Destroy()
        highlights[plr] = nil
    end
end)

-- Remove highlights ao resetar
LocalPlayer.CharacterAdded:Connect(function()
    if active then
        highlightAll()
    end
end)
