-- Variables
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- ESP Settings
local ESPColor = Color3.fromRGB(0, 255, 0)
local ESPThickness = 2
local HealthBarWidth = 2
local HealthBarHeight = 4
local TextSize = 14

-- Function to create ESP for a player
local function CreateESP(Player)
    local BillboardGui = Instance.new("BillboardGui")
    BillboardGui.Adornee = Player.Character:WaitForChild("Head")
    BillboardGui.Size = UDim2.new(0, 200, 0, 50)
    BillboardGui.AlwaysOnTop = true
    BillboardGui.StudsOffset = Vector3.new(0, 2, 0)

    local Frame = Instance.new("Frame", BillboardGui)
    Frame.BackgroundColor3 = ESPColor
    Frame.Size = UDim2.new(1, 0, 0, ESPThickness)

    local NameTag = Instance.new("TextLabel", BillboardGui)
    NameTag.BackgroundTransparency = 1
    NameTag.Size = UDim2.new(1, 0, 1, 0)
    NameTag.Text = Player.Name
    NameTag.TextColor3 = ESPColor
    NameTag.TextSize = TextSize
    NameTag.Font = Enum.Font.SourceSans

    local HealthBar = Instance.new("Frame", BillboardGui)
    HealthBar.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
    HealthBar.Size = UDim2.new(0, HealthBarWidth, 1, HealthBarHeight)
    HealthBar.Position = UDim2.new(0, -HealthBarWidth * 2, 0, 0)

    BillboardGui.Parent = Player.Character:WaitForChild("Head")

    local function UpdateESP()
        if Player.Character and Player.Character:FindFirstChild("Humanoid") then
            local Humanoid = Player.Character.Humanoid
            local Health = Humanoid.Health / Humanoid.MaxHealth
            HealthBar.Size = UDim2.new(0, HealthBarWidth, Health, HealthBarHeight)
            HealthBar.BackgroundColor3 = Color3.fromRGB(255 * (1 - Health), 255 * Health, 0)
        end
    end

    Player.Character.Humanoid.Changed:Connect(UpdateESP)
    UpdateESP()
end

-- Apply ESP to all players in-game
for _, Player in ipairs(Players:GetPlayers()) do
    if Player ~= LocalPlayer then
        CreateESP(Player)
    end
end

-- Handle new players joining
Players.PlayerAdded:Connect(function(Player)
    Player.CharacterAdded:Connect(function()
        wait(1) -- Allow time for the character to load
        CreateESP(Player)
    end)
end)
