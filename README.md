local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local ESP_ACTIVE = false
local highlights = {}

local function createHighlight(character)
    if not character then return end
    if character:FindFirstChild("Highlight") then return end

    local highlight = Instance.new("Highlight")
    highlight.Name = "Highlight"
    highlight.FillColor = Color3.fromRGB(255,0,0)
    highlight.OutlineColor = Color3.fromRGB(255,0,0)
    highlight.FillTransparency = 0.5
    highlight.OutlineTransparency = 0
    highlight.Parent = character
    highlights[character] = highlight
end

local function removeHighlight(character)
    if not character then return end
    local highlight = character:FindFirstChild("Highlight")
    if highlight then
        highlight:Destroy()
        highlights[character] = nil
    end
end

local function toggleESP()
    ESP_ACTIVE = not ESP_ACTIVE
    if ESP_ACTIVE then
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character then
                createHighlight(player.Character)
            end
        end
        Players.PlayerAdded:Connect(function(player)
            player.CharacterAdded:Connect(function(character)
                if ESP_ACTIVE then createHighlight(character) end
            end)
        end)
        for _, player in ipairs(Players:GetPlayers()) do
            player.CharacterAdded:Connect(function(character)
                if ESP_ACTIVE then createHighlight(character) end
            end)
        end
    else
        for character, _ in pairs(highlights) do
            removeHighlight(character)
        end
        highlights = {}
    end
end

local UIS = game:GetService("UserInputService")
UIS.InputBegan:Connect(function(input, processed)
    if processed then return end
    if input.KeyCode == Enum.KeyCode.V then
        toggleESP()
    end
end)
