local Rayfield = loadstring(game:HttpGet("https://sirius.menu/rayfield"))()
 
local Window = Rayfield:CreateWindow({
   Name = "smiles fun box - dw",
   Icon = 97823152917197, -- Icon in Topbar. Can use Lucide Icons (string) or Roblox Image (number). 0 to use no icon (default).
   LoadingTitle = "loading the fun up",
   LoadingSubtitle = "by mr smiles",
   Theme = "Ocean", -- Check https://docs.sirius.menu/rayfield/configuration/themes
   ConfigurationSaving = { -- Fixed capitalization
	   Enabled = true,
	   FolderName = nil,
	   FileName = "smiles fun box - dw"
   },
   KeySystem = false
})
 
local playertab = Window:CreateTab("local player", 117259180607823)
local testingtab = Window:CreateTab("untested", 15928895003) 
local esptab = Window:CreateTab("ESPS", 120129574453255)
local funtab = Window:CreateTab("fun", 101478743599847)
local skintab = Window:CreateTab("skins", 15726694719)
 
local walkSpeedSlider = playertab:CreateSlider({
   Name = "Walk Speed",
   Range = {16, 100},
   Increment = 1,
   Suffix = "Speed",
   CurrentValue = 16,
   Callback = function(Value)
       local player = game.Players.LocalPlayer
       if player.Character and player.Character:FindFirstChildOfClass("Humanoid") then
           player.Character:FindFirstChildOfClass("Humanoid").WalkSpeed = Value
       end
   end
})
 
local enforcingSpeed = false
 
local enforceWalkSpeed = playertab:CreateToggle({
   Name = "STOP MAKEING MY SPEED NORMAL!",
   CurrentValue = false,
   Flag = "EnforceSpeedToggle",
   Callback = function(Value)
       enforcingSpeed = Value
       local player = game.Players.LocalPlayer
       local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
 
       if humanoid then
           task.spawn(function()
               while enforcingSpeed and humanoid.Parent do
                   humanoid.WalkSpeed = walkSpeedSlider.CurrentValue
                   task.wait(0) -- Prevents lag and ensures it updates
               end
           end)
       end
   end
})
 
-- Monster ESP Button
local monsterESPEnabled = false
local tapeESPEnabled = false
local bandESPEnabled = false
 
-- Function to add or remove highlights
local function updateESP()
    while monsterESPEnabled or tapeESPEnabled or bandESPEnabled do
        for _, obj in ipairs(workspace:GetDescendants()) do
            if obj:IsA("Model") then
                local highlight = obj:FindFirstChild("Highlight")
 
                -- Monster ESP
                if obj.Name:lower():sub(-7) == "monster" then
                    if monsterESPEnabled then
                        if not highlight then
                            highlight = Instance.new("Highlight")
                            highlight.Parent = obj
                            highlight.FillColor = Color3.fromRGB(255, 0, 0) -- Red
                            highlight.OutlineColor = Color3.fromRGB(255, 255, 255) -- White
                        end
                    else
                        if highlight then highlight:Destroy() end
                    end
                end
 
                -- Tape ESP
                if obj.Name:lower():match("tape$") then
                    if tapeESPEnabled then
                        if not highlight then
                            highlight = Instance.new("Highlight")
                            highlight.Parent = obj
                            highlight.FillColor = Color3.fromRGB(65, 247, 47) -- Green
                            highlight.OutlineColor = Color3.fromRGB(255, 255, 255) -- White
                        end
                    else
                        if highlight then highlight:Destroy() end
                    end
                end
 
                -- Bandage ESP
                if obj.Name:lower():sub(-7) == "bandage" then
                    if bandESPEnabled then
                        if not highlight then
                            highlight = Instance.new("Highlight")
                            highlight.Parent = obj
                            highlight.FillColor = Color3.fromRGB(255, 0, 0) -- Red
                            highlight.OutlineColor = Color3.fromRGB(255, 128, 128) -- Light Red
                        end
                    else
                        if highlight then highlight:Destroy() end
                    end
                end
            end
        end
        task.wait(1) -- Updates ESP every second
    end
end
 
-- Toggle Buttons
local MonsterToggle = esptab:CreateToggle({
    Name = "twisted ESP",
    CurrentValue = false,
    Callback = function(state)
        monsterESPEnabled = state
        if state then
            task.spawn(updateESP)
        else
            -- Remove all monster highlights
            for _, obj in ipairs(workspace:GetDescendants()) do
                if obj:IsA("Model") and obj.Name:lower():sub(-7) == "monster" then
                    local highlight = obj:FindFirstChild("Highlight")
                    if highlight then highlight:Destroy() end
                end
            end
        end
    end
})
 
local TapeToggle = esptab:CreateToggle({
    Name = "Tapes ESP",
    CurrentValue = false,
    Callback = function(state)
        tapeESPEnabled = state
        if state then
            task.spawn(updateESP)
        else
            -- Remove all tape highlights
            for _, obj in ipairs(workspace:GetDescendants()) do
                if obj:IsA("Model") and obj.Name:lower():match("tape$") then
                    local highlight = obj:FindFirstChild("Highlight")
                    if highlight then highlight:Destroy() end
                end
            end
        end
    end
})
 
local BandToggle = esptab:CreateToggle({
    Name = "Band ESP",
    CurrentValue = false,
    Callback = function(state)
        bandESPEnabled = state
        if state then
            task.spawn(updateESP)
        else
            -- Remove all bandage highlights
            for _, obj in ipairs(workspace:GetDescendants()) do
                if obj:IsA("Model") and obj.Name:lower():sub(-7) == "Bandage" then
                    local highlight = obj:FindFirstChild("Highlight")
                    if highlight then highlight:Destroy() end
                end
            end
        end
    end
})
 
local capESPEnabled = false
 
local function updateESP()
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("Model") and obj.Name:lower():sub(-16) == "researchcapsule" then
            if capESPEnabled then
                if not obj:FindFirstChild("Highlight") then
                    local highlight = Instance.new("Highlight")
                    highlight.Parent = obj
                    highlight.FillColor = Color3.fromRGB(0, 255, 255) -- Cyan highlight
                    highlight.OutlineColor = Color3.fromRGB(255, 255, 255) -- White outline
                end
            else
                local highlight = obj:FindFirstChild("Highlight")
                if highlight then
                    highlight:Destroy()
                end
            end
        end
    end
end
 
local capToggle = esptab:CreateToggle({
    Name = "Capsule ESP",
    CurrentValue = false,
    Callback = function(state)
        capESPEnabled = state
        updateESP()
    end
})
 
-- Update ESP dynamically when new objects are added
workspace.DescendantAdded:Connect(function(obj)
    if capESPEnabled and obj:IsA("Model") and obj.Name:lower():sub(-16) == "researchcapsule" then
        task.wait(0.1) -- Small delay to ensure proper parenting
        if not obj:FindFirstChild("Highlight") then
            local highlight = Instance.new("Highlight")
            highlight.Parent = obj
            highlight.FillColor = Color3.fromRGB(0, 255, 255) -- Cyan highlight
            highlight.OutlineColor = Color3.fromRGB(255, 255, 255) -- White outline
        end
    end
end)
 
 
local spinning = false
 
local SpinToggle = funtab:CreateToggle({
   Name = "have rabies ",
   CurrentValue = false,
   Flag = "rabiesToggle",
   Callback = function(Value)
       spinning = Value
       local player = game.Players.LocalPlayer
       if not player.Character or not player.Character:FindFirstChild("HumanoidRootPart") then return end
 
       local hrp = player.Character.HumanoidRootPart
       local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
 
       if humanoid then
           task.spawn(function()
               while spinning and humanoid.Parent do
                   humanoid.WalkSpeed = 100 -- Continuously enforce speed to bypass resets
                   task.wait(0.1) -- Adjust timing as needed
               end
               humanoid.WalkSpeed = 16 -- Reset speed when toggle is off
           end)
       end
 
       -- Spinning Loop (Random 3D Rotation)
       task.spawn(function()
           while spinning and hrp and hrp.Parent do
               hrp.CFrame = hrp.CFrame * CFrame.Angles(
                   math.rad(math.random(-10000, 10000)),
                   math.rad(math.random(-10000, 10000)),
                   math.rad(math.random(-10000, 10000))
               )
               task.wait(0.05)
           end
       end)
   end
})
 
local TextureButton = skintab:CreateButton({
   Name = "become smile box (CS AND USE BOXTEN)",
   Callback = function()
       local player = game.Players.LocalPlayer
       if not player.Character then return end
 
       for _, part in ipairs(player.Character:GetDescendants()) do
           if part:IsA("MeshPart") then
               part.TextureID = "http://www.roblox.com/asset/?id=121418433953089"
           end
       end
   end
})
 
local TextureButton2 = skintab:CreateButton({
   Name = "become smile poppy (CS AND USE POPPY)",
   Callback = function()
       local player = game.Players.LocalPlayer
       if not player.Character then return end
 
       for _, part in ipairs(player.Character:GetDescendants()) do
           if part:IsA("MeshPart") then
               part.TextureID = "http://www.roblox.com/asset/?id=96926061582473"
           end
       end
   end
})
 
local TextureButton2 = skintab:CreateButton({
   Name = "become melanie (CS AND USE VEE)",
   Callback = function()
       local player = game.Players.LocalPlayer
       if not player.Character then return end
 
       for _, part in ipairs(player.Character:GetDescendants()) do
           if part:IsA("MeshPart") then
               part.TextureID = "http://www.roblox.com/asset/?id=110247389817670"
           end
       end
   end
}) 

local TextureButton2 = skintab:CreateButton({
   Name = "become kind shrimpo (CS AND USE SHRIMPO)",
   Callback = function()
       local player = game.Players.LocalPlayer
       if not player.Character then return end
 
       for _, part in ipairs(player.Character:GetDescendants()) do
           if part:IsA("MeshPart") then
               part.TextureID = "http://www.roblox.com/asset/?id=99922815734943"
           end
       end
   end
}) 

local TextureButton2 = skintab:CreateButton({
   Name = "become lovers day bobette (CS AND USE BOBETTE)",
   Callback = function()
       local player = game.Players.LocalPlayer
       if not player.Character then return end
 
       for _, part in ipairs(player.Character:GetDescendants()) do
           if part:IsA("MeshPart") then
               part.TextureID = "http://www.roblox.com/asset/?id=73287767696264"
           end
       end
   end
}) 

local TextureButton2 = skintab:CreateButton({
   Name = "become star time bobette (CS AND USE BOBETTE)",
   Callback = function()
       local player = game.Players.LocalPlayer
       if not player.Character then return end
 
       for _, part in ipairs(player.Character:GetDescendants()) do
           if part:IsA("MeshPart") then
               part.TextureID = "http://www.roblox.com/asset/?id=80671054695508"
           end
       end
   end
}) 

local TextureButton2 = skintab:CreateButton({
   Name = "become infected finn (CS AND USE FINN)",
   Callback = function()
       local player = game.Players.LocalPlayer
       if not player.Character then return end
 
       for _, part in ipairs(player.Character:GetDescendants()) do
           if part:IsA("MeshPart") then
               part.TextureID = "http://www.roblox.com/asset/?id=108557556755848"
           end
       end
   end
}) 

local TextureButton2 = skintab:CreateButton({
   Name = "become pooly (CS AND USE TOODLES)",
   Callback = function()
       local player = game.Players.LocalPlayer
       if not player.Character then return end
 
       for _, part in ipairs(player.Character:GetDescendants()) do
           if part:IsA("MeshPart") then
               part.TextureID = "http://www.roblox.com/asset/?id=76169453489922"
           end
       end
   end
}) 
 

local TextureButton2 = skintab:CreateButton({
   Name = "become stupid toodles (CS AND USE TOODLES)",
   Callback = function()
       local player = game.Players.LocalPlayer
       if not player.Character then return end
 
       for _, part in ipairs(player.Character:GetDescendants()) do
           if part:IsA("MeshPart") then
               part.TextureID = "http://www.roblox.com/asset/?id=121278972466351"
           end
       end
   end
}) 


local TextureButton2 = skintab:CreateButton({
   Name = "become glutter (CS AND USE FLUTTER)",
   Callback = function()
       local player = game.Players.LocalPlayer
       if not player.Character then return end
 
       for _, part in ipairs(player.Character:GetDescendants()) do
           if part:IsA("MeshPart") then
               part.TextureID = "http://www.roblox.com/asset/?id=17230708611"
           end
       end
   end
})

local TextureButton2 = skintab:CreateButton({
   Name = "become starfall goob (CS AND USE GOOB)",
   Callback = function()
       local player = game.Players.LocalPlayer
       if not player.Character then return end
 
       for _, part in ipairs(player.Character:GetDescendants()) do
           if part:IsA("MeshPart") then
               part.TextureID = "http://www.roblox.com/asset/?id=82002793880797"
           end
       end
   end
}) 


local TextureButton2 = skintab:CreateButton({
   Name = "become stinkstro (CS AND USE ASSTROKES)",
   Callback = function()
       local player = game.Players.LocalPlayer
       if not player.Character then return end
 
       for _, part in ipairs(player.Character:GetDescendants()) do
           if part:IsA("MeshPart") then
               part.TextureID = "http://www.roblox.com/asset/?id=83640346037630"
           end
       end
   end
}) 
 

local jumpToggle = playertab:CreateToggle({
   Name = "toons can jump",
   CurrentValue = false,
   Flag = "JumpToggle",
   Callback = function(Value)
       local player = game.Players.LocalPlayer
       if not player.Character or not player.Character:FindFirstChildOfClass("Humanoid") then return end
 
       local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
       humanoid.JumpPower = Value and 50 or 0
       humanoid.UseJumpPower = Value -- Enable jumping if toggled on
 
       if Value then
           humanoid.StateChanged:Connect(function(old, new)
               if new == Enum.HumanoidStateType.Jumping then
                   local anim = Instance.new("Animation")
                   anim.AnimationId = "rbxassetid://0"
                   local animator = humanoid:FindFirstChildOfClass("Animator")
                   if animator then
                       local playAnim = animator:LoadAnimation(anim)
                       playAnim:Play()
                   end
               end
           end)
       end
   end
})

 
local targetPlayer = nil 
local following = false
local animTrack = nil -- Store animation track
 
local followInput = funtab:CreateInput({
    Name = "Backshot People",
    CurrentValue = "",
    PlaceholderText = "Enter Username to backshot",
    RemoveTextAfterFocusLost = false,
    Flag = "FollowPlayerInput",
    Callback = function(username)
        local player = game.Players.LocalPlayer
        local target = nil
        
        -- Find a player whose name contains the input
        for _, p in ipairs(game.Players:GetPlayers()) do
            if p.Name:lower():find(username:lower()) then
                target = p
                break
            end
        end
 
        if target and target.Character and player.Character then
            local humanoidRootPart = player.Character:FindFirstChild("HumanoidRootPart")
            local targetRoot = target.Character:FindFirstChild("HumanoidRootPart")
            local targetBody = target.Character:FindFirstChild("HumanoidRootPart") -- Using HRP for rotation reference
 
            if humanoidRootPart and targetRoot then
                targetPlayer = target
                following = true
 
                -- Teleport to the target first
                humanoidRootPart.CFrame = targetRoot.CFrame + targetRoot.CFrame.LookVector * -2 + Vector3.new(0, 0.562, 0)
 
                -- Ensure the animation plays after teleporting
                local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
                if humanoid then
                    local animator = humanoid:FindFirstChildOfClass("Animator")
                    if not animator then
                        -- Create an animator if it doesn't exist
                        animator = Instance.new("Animator")
                        animator.Name = "Animator"
                        animator.Parent = humanoid
                    end
 
                    -- Stop all current animations before playing a new one
                    for _, track in ipairs(animator:GetPlayingAnimationTracks()) do
                        track:Stop()
                    end
 
                    -- Play the specific animation
                    local anim = Instance.new("Animation")
                    anim.AnimationId = "rbxassetid://17561277632"
                    animTrack = animator:LoadAnimation(anim)
                    animTrack.Priority = Enum.AnimationPriority.Action
                    animTrack.Looped = true
                    animTrack:Play()
                end
 
                -- Follow Loop
                task.spawn(function()
                    while following and targetPlayer and targetPlayer.Character and humanoidRootPart.Parent do
                        if targetRoot and humanoidRootPart then
                            -- Position the player behind the target
                            local offset = targetRoot.CFrame.LookVector * -2 -- Moves 2 studs behind the player
                            humanoidRootPart.CFrame = CFrame.new(targetRoot.Position + offset - Vector3.new(0, 0.562, 0))
 
                            -- Lock Rotation to match the target's front
                            if targetBody then
                                local targetRotation = targetBody.Orientation.Y
                                humanoidRootPart.CFrame = CFrame.new(humanoidRootPart.Position) * CFrame.Angles(0, math.rad(targetRotation), 0)
                            end
                        end
                        task.wait(0.05) -- Small delay for smooth following
                    end
                end)
            end
        else
            print("Player not found")
        end
    end
})
 
local stopFollowButton = funtab:CreateButton({
    Name = "Stop Backshotting People",
    Callback = function()
        following = false
        if animTrack then
            animTrack:Stop()
            animTrack = nil -- Reset animation track
        end
    end
})
 
-- Stop following when the player resets or leaves
game.Players.LocalPlayer.CharacterRemoving:Connect(function()
    following = false
    if animTrack then
        animTrack:Stop()
        animTrack = nil
    end
end)
 
local normalGravity = 196.2 -- Default Roblox gravity
local lowGravity = 196.2 / 6 -- Approximate moon gravity
 
local lowGravEnabled = false
local noGravEnabled = false
 
-- Function to update gravity
local function updateGravity()
    if noGravEnabled then
        workspace.Gravity = 0
    elseif lowGravEnabled then
        workspace.Gravity = lowGravity
    else
        workspace.Gravity = normalGravity
    end
end
 
-- Low Gravity Toggle
local LowGravToggle = funtab:CreateToggle({
    Name = "Low Grav",
    CurrentValue = false,
    Callback = function(state)
        lowGravEnabled = state
        updateGravity()
    end
})
 
-- No Gravity Toggle
local NoGravToggle = funtab:CreateToggle({
    Name = "No Grav",
    CurrentValue = false,
    Callback = function(state)
        noGravEnabled = state
        updateGravity()
    end
})
 
local targetPlayer = nil
local following = false
local animTrack = nil -- Store animation track
 
local followInput = funtab:CreateInput({
    Name = "Ride Player's Head",
    CurrentValue = "",
    PlaceholderText = "Enter Username",
    RemoveTextAfterFocusLost = false,
    Flag = "FollowPlayerInput",
    Callback = function(username)
        local player = game.Players.LocalPlayer
        local target = game.Players:FindFirstChild(username)
 
        if target and target.Character and player.Character then
            local humanoidRootPart = player.Character:FindFirstChild("HumanoidRootPart")
            local targetHead = target.Character:FindFirstChild("Head")
            local targetBody = target.Character:FindFirstChild("HumanoidRootPart") -- Using HRP for rotation reference
 
            if humanoidRootPart and targetHead then
                targetPlayer = target
                following = true
 
                -- Stop any existing animations
                local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
                if humanoid then
                    local animator = humanoid:FindFirstChildOfClass("Animator") or Instance.new("Animator", humanoid)
                    if animator then
                        for _, track in ipairs(animator:GetPlayingAnimationTracks()) do
                            track:Stop()
                        end
                    end
 
                    -- Play Only the Specific Animation with High Priority
                    local anim = Instance.new("Animation")
                    anim.AnimationId = "rbxassetid://16873659196"
                    animTrack = animator:LoadAnimation(anim)
                    animTrack.Priority = Enum.AnimationPriority.Action
                    animTrack.Looped = true
                    animTrack:Play()
                end
 
                -- Follow Loop
                task.spawn(function()
                    while following and targetPlayer and targetPlayer.Character and humanoidRootPart.Parent do
                        if targetHead and humanoidRootPart then
                            -- Position the player on top of the target's head
                            humanoidRootPart.CFrame = CFrame.new(targetHead.Position + Vector3.new(0, 2.3, 0))
 
                            -- Match rotation to the target's HRP
                            if targetBody then
                                local targetRotation = targetBody.Orientation.Y
                                humanoidRootPart.CFrame = humanoidRootPart.CFrame * CFrame.Angles(0, math.rad(targetRotation), 0)
                            end
                        end
                        task.wait()
                    end
                end)
            end
        else
            print("Player not found")
        end
    end
})
 
local stopFollowButton = funtab:CreateButton({
    Name = "Stop Riding Player",
    Callback = function()
        following = false
        if animTrack then
            animTrack:Stop()
            animTrack = nil -- Reset animation track
        end
    end
})
 
-- Stop following when the player resets or leaves
game.Players.LocalPlayer.CharacterRemoving:Connect(function()
    following = false
    if animTrack then
        animTrack:Stop()
        animTrack = nil
    end
end)
 
local generatorESPEnabled = false
local function updateGeneratorESP()
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("Model") and obj.Name == "Generator" then
            if generatorESPEnabled then
                if not obj:FindFirstChild("Highlight") then
                    local highlight = Instance.new("Highlight")
                    highlight.Parent = obj
                    highlight.FillColor = Color3.fromRGB(255, 255, 0) -- Yellow highlight
                    highlight.OutlineColor = Color3.fromRGB(255, 255, 255) -- White outline
                end
            else
                local highlight = obj:FindFirstChild("Highlight")
                if highlight then
                    highlight:Destroy()
                end
            end
        end
    end
end

local generatorToggle = esptab:CreateToggle({
    Name = "Generator ESP",
    CurrentValue = false,
    Callback = function(state)
        generatorESPEnabled = state
        updateGeneratorESP()
    end
})

local autoGrabEnabled = false

local autoGrabToggle = testingtab:CreateToggle({
    Name = "Auto Grab Items",
    CurrentValue = false,
    Callback = function(state)
        autoGrabEnabled = state
        if state then
            task.spawn(function()
                while autoGrabEnabled do
                    local player = game.Players.LocalPlayer
                    local character = player.Character
                    if character then
                        local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
                        if humanoidRootPart then
                            for _, item in ipairs(workspace:GetDescendants()) do
                                if item:IsA("Model") and item:FindFirstChild("Handle") then
                                    local distance = (humanoidRootPart.Position - item.Handle.Position).Magnitude
                                    if distance >= 100 and distance <= 200 then
                                        fireproximityprompt(item.Handle:FindFirstChildOfClass("ProximityPrompt"))
                                    end
                                end
                            end
                        end
                    end
                    task.wait(0) 
                end
            end)
        end
    end
})

local toggleState = false
local storedParts = {}
local connections = {}

local function disableCollision(part)
	if (part:IsA("BasePart") or part:IsA("UnionOperation")) and not storedParts[part] then
		storedParts[part] = true
		part.CanCollide = false
		part.CanTouch = false
		pcall(function() part.CollisionGroupId = 0 end)
	end
end

local function monitorFreeArea(folder)
	for _, obj in ipairs(folder:GetDescendants()) do
		disableCollision(obj)
	end

	local conn = folder.DescendantAdded:Connect(function(newPart)
		if toggleState then
			disableCollision(newPart)
		end
	end)
	table.insert(connections, conn)
end

local function scanAndWatch()
	local currentRoom = workspace:FindFirstChild("CurrentRoom")
	if not currentRoom then return end

	for _, obj in ipairs(currentRoom:GetDescendants()) do
		if obj:IsA("Folder") and obj.Name == "FreeArea" then
			monitorFreeArea(obj)
		end
	end
end

local function cleanup()
	for part in pairs(storedParts) do
		if part and part.Parent then
			part.CanCollide = true
			part.CanTouch = true
		end
	end
	storedParts = {}

	for _, conn in ipairs(connections) do
		if conn then conn:Disconnect() end
	end
	connections = {}
end

funtab:CreateToggle({
	Name = "Noclip Objects",
	CurrentValue = false,
	Flag = "ToggleNoclipObjects",
	Callback = function(state)
		toggleState = state

		if state then
			task.spawn(function()
				while toggleState do
					scanAndWatch()
					task.wait(0.25)
				end
			end)
		else
			cleanup()
		end
	end
})

