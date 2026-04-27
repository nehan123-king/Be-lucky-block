local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()

local Window = Fluent:CreateWindow({
    Title = "Be a Brainrot",
    SubTitle = "NAH HUB | nehan",
    TabWidth = 160,
    Size = UDim2.fromOffset(550, 430),
    Acrylic = false,
    Theme = "Darker",
    MinimizeKey = Enum.KeyCode.LeftControl
})

local Tabs = {
    Main = Window:AddTab({ Title = "Main", Icon = "box" }),
    Stats = Window:AddTab({ Title = "Stats", Icon = "gauge" }),
    Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
}

local Options = Fluent.Options

do
    local RunService = game:GetService("RunService")
    local player = game.Players.LocalPlayer

    ---------------------------------------------------------
    -- BYPASS TELEPORT (BETTER THAN SPEED)
    ---------------------------------------------------------
    Tabs.Stats:AddButton({
        Title = "Instant Base Escape",
        Description = "Teleports you and your Brainrot outside the kick zone",
        Callback = function()
            local folder = workspace:FindFirstChild("RunningModels")
            local targetPos = Vector3.new(715, 39, -2122) -- Your farm zone
            
            if folder then
                for _, model in ipairs(folder:GetChildren()) do
                    if model:GetAttribute("OwnerId") == player.UserId then
                        -- Instantly move the model to bypass the speed check
                        model:MoveTo(targetPos)
                    end
                end
            end
            
            if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                player.Character.HumanoidRootPart.CFrame = CFrame.new(targetPos)
            end
        end
    })

    ---------------------------------------------------------
    -- LEGIT SPEED (STAYING UNDER 275)
    ---------------------------------------------------------
    local speedConn
    local SpeedToggle = Tabs.Stats:AddToggle("MovementToggle", {
        Title = "Enable Legit Speed", 
        Description = "Locked at 250 to prevent kicks",
        Default = false
    })
    
    SpeedToggle:OnChanged(function()
        if speedConn then speedConn:Disconnect() end
        if Options.MovementToggle.Value then
            speedConn = RunService.Heartbeat:Connect(function()
                local folder = workspace:FindFirstChild("RunningModels")
                if folder then
                    for _, model in ipairs(folder:GetChildren()) do
                        if model:GetAttribute("OwnerId") == player.UserId then
                            -- We use 250 because 275 is the kick point
                            model:SetAttribute("MovementSpeed", 250)
                        end
                    end
                end
            end)
        end
    end)

    -- Finalize
    SaveManager:SetLibrary(Fluent)
    InterfaceManager:SetLibrary(Fluent)
    InterfaceManager:BuildInterfaceSection(Tabs.Settings)
    SaveManager:BuildConfigSection(Tabs.Settings)
    Window:SelectTab(1)
end
