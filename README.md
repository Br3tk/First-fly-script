-- LocalScript to be placed inside StarterPlayerScripts or a GUI button

local userInputService = game:GetService("UserInputService")
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local camera = game.Workspace.CurrentCamera
local flying = false
local speed = 50  -- Flight speed
local flyHeight = 50  -- Height of flight from ground
local bodyGyro, bodyVelocity  -- These will be used to control flight

-- Function to start flying
local function startFlying()
    if flying then return end
    flying = true

    -- Creating BodyGyro to maintain orientation during flight
    bodyGyro = Instance.new("BodyGyro")
    bodyGyro.MaxTorque = Vector3.new(400000, 400000, 400000)  -- Control the rotational torque
    bodyGyro.CFrame = character.PrimaryPart.CFrame  -- Start with the current orientation
    bodyGyro.Parent = character.PrimaryPart

    -- Creating BodyVelocity to control flight speed and direction
    bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.MaxForce = Vector3.new(400000, 400000, 400000)  -- Strong enough to counter gravity
    bodyVelocity.Velocity = Vector3.new(0, 0, 0)  -- Initial velocity is zero
    bodyVelocity.Parent = character.PrimaryPart

    -- Allow player to move using WASD
    userInputService.InputBegan:Connect(function(input, gameProcessed)
        if gameProcessed then return end
        
        if input.KeyCode == Enum.KeyCode.W then
            bodyVelocity.Velocity = camera.CFrame.LookVector * speed
        elseif input.KeyCode == Enum.KeyCode.S then
            bodyVelocity.Velocity = -camera.CFrame.LookVector * speed
        elseif input.KeyCode == Enum.KeyCode.A then
            bodyVelocity.Velocity = -camera.CFrame.RightVector * speed
        elseif input.KeyCode == Enum.KeyCode.D then
            bodyVelocity.Velocity = camera.CFrame.RightVector * speed
        elseif input.KeyCode == Enum.KeyCode.Space then
            bodyVelocity.Velocity = Vector3.new(0, speed, 0)  -- Move upwards
        elseif input.KeyCode == Enum.KeyCode.LeftControl then
            bodyVelocity.Velocity = Vector3.new(0, -speed, 0)  -- Move downwards
        end
    end)
end

-- Function to stop flying
local function stopFlying()
    if not flying then return end
    flying = false

    -- Remove BodyGyro and BodyVelocity when flying stops
    if bodyGyro then bodyGyro:Destroy() end
    if bodyVelocity then bodyVelocity:Destroy() end
end

-- Toggle flying with the F key
userInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    
    -- Toggle flying with the F key
    if input.KeyCode == Enum.KeyCode.F then
        if flying then
            stopFlying()
        else
            startFlying()
        end
    end
end)

-- Make sure the player can fly immediately after spawning
player.CharacterAdded:Connect(function(newCharacter)
    character = newCharacter
    humanoid = character:WaitForChild("Humanoid")
    camera = game.Workspace.CurrentCamera
end)
