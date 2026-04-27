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
    Upgrades = Window:AddTab({ Title = "Upgrades", Icon = "info" }),
    Brainrots = Window:AddTab({ Title = "Brainrots", Icon = "bot" }),
    Sell = Window:AddTab({ Title = "Sell", Icon = "dollar-sign" }),
    Stats = Window:AddTab({ Title = "Stats", Icon = "gauge" }),
    Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
}

local Options = Fluent.Options

do
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local Players = game:GetService("Players")
    local player = Players.LocalPlayer
    local RunService = game:GetService("RunService")
    local knit = ReplicatedStorage:WaitForChild("Packages"):WaitForChild("_Index"):WaitForChild("sleitnick_knit@1.7.0"):WaitForChild("knit"):WaitForChild("Services")

    ---------------------------------------------------------
    -- MAIN FUNCTIONS
    ---------------------------------------------------------
    local claimGift = knit:WaitForChild("PlaytimeRewardService"):WaitForChild("RF"):WaitForChild("ClaimGift")
    local autoClaiming = false
    Tabs.Main:AddToggle("ACPR", {Title = "Auto Claim Playtime Rewards", Default = false}):OnChanged(function(s)
        autoClaiming = s
        task.spawn(function()
            while autoClaiming do
                for i = 1, 12 do if not autoClaiming then break end pcall(function() claimGift:InvokeServer(i) end) task.wait(0.25) end
                task.wait(1)
            end
        end)
    end)

    local rebirth = knit:WaitForChild("RebirthService"):WaitForChild("RF"):WaitForChild("Rebirth")
    local autoRebirth = false
    Tabs.Main:AddToggle("AR", {Title = "Auto Rebirth", Default = false}):OnChanged(function(s)
        autoRebirth = s
        task.spawn(function() while autoRebirth do pcall(function() rebirth:InvokeServer() end) task.wait(1) end end)
    end)

    ---------------------------------------------------------
    -- UPGRADES
    ---------------------------------------------------------
    local upgrade = knit:WaitForChild("UpgradesService"):WaitForChild("RF"):WaitForChild("Upgrade")
    local upgRunning = false
    Tabs.Upgrades:AddToggle("AMS", {Title = "Auto Upgrade Speed", Default = false}):OnChanged(function(s)
        upgRunning = s
        task.spawn(function() while upgRunning do pcall(function() upgrade:InvokeServer("MovementSpeed", 1) end) task.wait(0.5) end end)
    end)

    ---------------------------------------------------------
    -- STATS (SAFE BYPASS SPEED)
    ---------------------------------------------------------
    local speedConn
    local SpeedToggle = Tabs.Stats:AddToggle("MovementToggle", {
        Title = "Enable God Speed (Base Escape)", 
        Description = "Stay under 2500 to avoid kicks!",
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
                            -- Keeps you fast but under the anti-cheat kick threshold
                            local val = math.min(Options.MovementSlider.Value, 2500)
                            model:SetAttribute("MovementSpeed", val)
                        end
                    end
                end
            end)
        end
    end)

    Tabs.Stats:AddSlider("MovementSlider", {
        Title = "Brainrot Speed", 
        Default = 1000, 
        Min = 50, 
        Max = 3000, 
        Rounding = 0
    })

    ---------------------------------------------------------
    -- FARMING
    ---------------------------------------------------------
    local farmRunning = false
    Tabs.Brainrots:AddToggle("AutoFarmToggle", {Title = "Auto Farm Best Brainrots", Default = false}):OnChanged(function(s)
        farmRunning = s
        if s then
            task.spawn(function()
                while farmRunning do
                    local char = player.Character or player.CharacterAdded:Wait()
                    local root = char:WaitForChild("HumanoidRootPart")
                    root.CFrame = CFrame.new(715, 39, -2122)
                    task.wait(2)
                end
            end)
        end
    end)

    ---------------------------------------------------------
    -- SETTINGS
    ---------------------------------------------------------
    SaveManager:SetLibrary(Fluent)
    InterfaceManager:SetLibrary(Fluent)
    InterfaceManager:BuildInterfaceSection(Tabs.Settings)
    SaveManager:BuildConfigSection(Tabs.Settings)
    Window:SelectTab(1)
end
