-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")

-- Settings
local Settings = {
    SuperSpeed = false,
    NoClip = false,
    AntiTrap = false,
    AntiRit = false
}

local KeysPressed = {
    W = false,
    A = false,
    S = false,
    D = false
}

-- Track key presses
UserInputService.InputBegan:Connect(function(input, gpe)
    if gpe then return end
    if input.KeyCode == Enum.KeyCode.W then KeysPressed.W = true end
    if input.KeyCode == Enum.KeyCode.A then KeysPressed.A = true end
    if input.KeyCode == Enum.KeyCode.S then KeysPressed.S = true end
    if input.KeyCode == Enum.KeyCode.D then KeysPressed.D = true end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.W then KeysPressed.W = false end
    if input.KeyCode == Enum.KeyCode.A then KeysPressed.A = false end
    if input.KeyCode == Enum.KeyCode.S then KeysPressed.S = false end
    if input.KeyCode == Enum.KeyCode.D then KeysPressed.D = false end
end)

-- GUI
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)

local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0, 250, 0, 260)
Frame.Position = UDim2.new(0.5, -125, 0.5, -130)
Frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
Frame.BorderSizePixel = 0
Frame.Active = true
Frame.Draggable = true
Frame.Name = "GTSHubFrame"

local Title = Instance.new("TextLabel", Frame)
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Position = UDim2.new(0, 0, 0, 0)
Title.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
Title.BorderSizePixel = 0
Title.Text = "🌙 GTS Hub"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextScaled = true
Title.Font = Enum.Font.SourceSansBold

-- Function to create toggle buttons
local function createToggle(name, settingKey, yPos)
    local btn = Instance.new("TextButton", Frame)
    btn.Size = UDim2.new(0, 220, 0, 35)
    btn.Position = UDim2.new(0, 15, 0, yPos)
    btn.Text = name .. ": OFF"
    btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Font = Enum.Font.SourceSans
    btn.TextScaled = true
    
    btn.MouseButton1Click:Connect(function()
        Settings[settingKey] = not Settings[settingKey]
        btn.Text = name .. (Settings[settingKey] and ": ON" or ": OFF")
    end)
end

-- Creating toggles
createToggle("Super Speed", "SuperSpeed", 50)
createToggle("No Clip", "NoClip", 95)
createToggle("Anti Trap", "AntiTrap", 140)
createToggle("Anti Rit", "AntiRit", 185)

-- Movement function
local function getMovementVector()
    local direction = Vector3.new(0,0,0)
    local cam = workspace.CurrentCamera
    if KeysPressed.W then direction = direction + cam.CFrame.LookVector end
    if KeysPressed.S then direction = direction - cam.CFrame.LookVector end
    if KeysPressed.A then direction = direction - cam.CFrame.RightVector end
    if KeysPressed.D then direction = direction + cam.CFrame.RightVector end
    direction = Vector3.new(direction.X, 0, direction.Z)
    if direction.Magnitude > 0 then
        return direction.Unit
    else
        return Vector3.new(0,0,0)
    end
end

-- Heartbeat
RunService.Heartbeat:Connect(function(delta)
    -- Super Speed
    if Settings.SuperSpeed then
        local move = getMovementVector()
        HumanoidRootPart.Velocity = Vector3.new(move.X*100, HumanoidRootPart.Velocity.Y, move.Z*100)
    end
    
    -- NoClip
    for _, part in pairs(Character:GetDescendants()) do
        if part:IsA("BasePart") then
            part.CanCollide = not Settings.NoClip
        end
    end
    
    -- AntiTrap
    if Settings.AntiTrap then
        local ray = Ray.new(HumanoidRootPart.Position, HumanoidRootPart.CFrame.LookVector * 5)
        local part, pos = workspace:FindPartOnRay(ray, Character)
        if part and part.Name == "Trap" then
            HumanoidRootPart.CFrame = HumanoidRootPart.CFrame:Lerp(HumanoidRootPart.CFrame * CFrame.new(0,0,-3), 0.5)
        end
    end
    
    -- AntiRit
    if Settings.AntiRit then
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local distance = (HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
                if distance < 5 then
                    HumanoidRootPart.CFrame = HumanoidRootPart.CFrame:Lerp(HumanoidRootPart.CFrame * CFrame.new(0,0,-3), 0.5)
                end
            end
        end
    end
end)

