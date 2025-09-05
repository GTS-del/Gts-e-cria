-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")
local UserInputService = game:GetService("UserInputService")

-- Settings
local Settings = {
    SuperSpeed = false,
    NoClip = false,
    AntiTrap = false,
    AntiRit = false
}

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

-- Functions
RunService.Heartbeat:Connect(function()
    if Settings.SuperSpeed then
        HumanoidRootPart.Velocity = HumanoidRootPart.CFrame.LookVector * 100
    end
    
    if Settings.NoClip then
        for _, part in pairs(Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    else
        for _, part in pairs(Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = true
            end
        end
    end
    
    if Settings.AntiTrap then
        local ray = Ray.new(HumanoidRootPart.Position, HumanoidRootPart.CFrame.LookVector * 5)
        local part, pos = workspace:FindPartOnRay(ray, Character)
        if part and part.Name == "Trap" then
            HumanoidRootPart.CFrame = HumanoidRootPart.CFrame * CFrame.new(0,0,-5)
        end
    end
    
    if Settings.AntiRit then
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local distance = (HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
                if distance < 5 then
                    HumanoidRootPart.CFrame = HumanoidRootPart.CFrame * CFrame.new(0,0,-3)
                end
            end
        end
    end
end)
