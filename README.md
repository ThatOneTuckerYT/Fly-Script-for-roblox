-- Simple Fly Script.
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
local flying = false
local speed = 50
local bodyGyro, bodyVelocity

-- Function to toggle flight
local function toggleFlight()
    if flying then
        -- Turn off flight
        flying = false
        bodyGyro:Destroy()
        bodyVelocity:Destroy()
    else
        -- Enable flight
        flying = true
        bodyGyro = Instance.new("BodyGyro")
        bodyVelocity = Instance.new("BodyVelocity")

        bodyGyro.P = 9e4
        bodyGyro.CFrame = humanoidRootPart.CFrame
        bodyGyro.MaxTorque = Vector3.new(9e4, 9e4, 9e4)
        bodyGyro.Parent = humanoidRootPart

        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        bodyVelocity.MaxForce = Vector3.new(9e4, 9e4, 9e4)
        bodyVelocity.Parent = humanoidRootPart
    end
end

-- Listen for key press to toggle flight
local userInputService = game:GetService("UserInputService")
userInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.F then
        toggleFlight()
    end
end)

-- Update flying movement
game:GetService("RunService").RenderStepped:Connect(function()
    if flying then
        local moveDirection = Vector3.new(
            (userInputService:IsKeyDown(Enum.KeyCode.D) and 1 or 0) - (userInputService:IsKeyDown(Enum.KeyCode.A) and 1 or 0),
            (userInputService:IsKeyDown(Enum.KeyCode.Space) and 1 or 0) - (userInputService:IsKeyDown(Enum.KeyCode.LeftControl) and 1 or 0),
            (userInputService:IsKeyDown(Enum.KeyCode.S) and 1 or 0) - (userInputService:IsKeyDown(Enum.KeyCode.W) and 1 or 0)
        )
        bodyVelocity.Velocity = humanoidRootPart.CFrame:VectorToWorldSpace(moveDirection) * speed
        bodyGyro.CFrame = workspace.CurrentCamera.CFrame
    end
end)

