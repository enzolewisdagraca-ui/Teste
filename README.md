-- Script Hub: Aimlock & ESP Test Component
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local aimbotEnabled = false
local espEnabled = false

-- Função para criar Highlight (ESP)
local function createHighlight(target)
    local highlight = Instance.new("Highlight")
    highlight.FillColor = Color3.fromRGB(0, 255, 0) -- Verde
    highlight.OutlineTransparency = 1
    highlight.Parent = target
    return highlight
end

-- Lógica de busca de inimigo mais próximo
local function getClosestPlayer()
    local closest, minDistance = nil, math.huge
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
            local pos = Camera:WorldToViewportPoint(player.Character.Head.Position)
            local dist = (Vector2.new(pos.X, pos.Y) - Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)).Magnitude
            if dist < minDistance then
                closest = player.Character.Head
                minDistance = dist
            end
        end
    end
    return closest
end

-- Loop de execução (Aimlock)
RunService.RenderStepped:Connect(function()
    if aimbotEnabled then
        local target = getClosestPlayer()
        if target then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Position)
        end
    end
end)

-- GUI Hub (Interface de controle)
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
local ToggleButton = Instance.new("TextButton", ScreenGui)
ToggleButton.Size = UDim2.new(0, 100, 0, 50)
ToggleButton.Position = UDim2.new(0.1, 0, 0.1, 0)
ToggleButton.Text = "Toggle Aimlock"

ToggleButton.MouseButton1Click:Connect(function()
    aimbotEnabled = not aimbotEnabled
    ToggleButton.Text = aimbotEnabled and "Aimlock: ON" or "Aimlock: OFF"
end)
