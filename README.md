function FarmAutomation:CollectNearbyItems()
    local character = game.Players.LocalPlayer.Character
    if not character then return end
    
    local rootPart = character:FindFirstChild("HumanoidRootPart")
    if not rootPart then return end
    
    -- Find all collectible items (adjust folder/name as needed)
    local items = workspace:FindPartsInRegion3WithWhitelist(
        Region3.new(
            rootPart.Position - Vector3.new(self.Settings.CollectRange, self.Settings.CollectRange, self.Settings.CollectRange),
            rootPart.Position + Vector3.new(self.Settings.CollectRange, self.Settings.CollectRange, self.Settings.CollectRange)
        ),
        workspace:GetDescendants(),
        1000
    )
    
    for _, item in pairs(items) do
        if item:IsA("BasePart") and item:FindFirstChild("CollectPrompt") then
            -- Trigger collection (fireclickdetector or remote event)
            local clickDetector = item:FindFirstChildWhichIsA("ClickDetector")
            if clickDetector then
                clickDetector:Click()
            end
        end
    end
end

function FarmAutomation:CheckEvents()
    local players = game:GetService("Players")
    local localPlayer = players.LocalPlayer
    
    -- Find event zones/NPCs within range
    for _, eventZone in pairs(workspace:GetDescendants()) do
        if eventZone:IsA("Part") and eventZone.Name:find("Event") then
            local distance = (eventZone.Position - localPlayer.Character.HumanoidRootPart.Position).Magnitude
            if distance <= self.Settings.EventRange then
                -- Auto-interact with event
                eventZone:FindFirstChildWhichIsA("ClickDetector")?.Click()
            end
        end
    end
end
