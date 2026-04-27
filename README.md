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
---------------------------------------------------------
-- MAIN TAB (Auto Claim, Rebirth, Event Pass, Buy)
---------------------------------------------------------
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local knit = ReplicatedStorage:WaitForChild("Packages"):WaitForChild("_Index"):WaitForChild("sleitnick_knit@1.7.0"):WaitForChild("knit"):WaitForChild("Services")

-- Auto Claim Playtime
local claimGift = knit:WaitForChild("PlaytimeRewardService"):WaitForChild("RF"):WaitForChild("ClaimGift")
local autoClaiming = false
Tabs.Main:AddToggle("ACPR", { Title = "Auto Claim Playtime Rewards", Default = false }):OnChanged(function(s)
    autoClaiming = s
    task.spawn(function()
        while autoClaiming do
            for i = 1, 12 do if not autoClaiming then break end pcall(function() claimGift:InvokeServer(i) end) task.wait(0.25) end
            task.wait(1)
        end
    end)
end)

-- Auto Rebirth
local rebirthRF = knit:WaitForChild("RebirthService"):WaitForChild("RF"):WaitForChild("Rebirth")
local autoRebirth = false
Tabs.Main:AddToggle("AR", { Title = "Auto Rebirth", Default = false }):OnChanged(function(s)
    autoRebirth = s
    task.spawn(function() while autoRebirth do pcall(function() rebirthRF:InvokeServer() end) task.wait(1) end end)
end)

-- Auto Buy Best
local buySkin = knit:WaitForChild("SkinService"):WaitForChild("RF"):WaitForChild("BuySkin")
local autoBuy = false
Tabs.Main:AddToggle("ABL", { Title = "Auto Buy Best Brainrot", Default = false }):OnChanged(function(s) autoBuy = s end)
-- (Your existing Shop detection logic would go here in a loop)

---------------------------------------------------------
-- BRAINROTS TAB (Pickup, Teleport, Auto Farm)
---------------------------------------------------------
Tabs.Brainrots:AddButton({
    Title = "Pickup All Your Brainrots",
    Callback = function()
        -- Your existing plot detection and PickupBrainrot logic
    end
})

local farmRunning = false
Tabs.Brainrots:AddToggle("AutoFarmToggle", { Title = "Auto Farm Best Brainrots", Default = false }):OnChanged(function(s)
    farmRunning = s
    if s then
        task.spawn(function()
            while farmRunning do
                -- Your existing Auto Farm CFrame logic
                task.wait(2)
            end
        end)
    end
end)

---------------------------------------------------------
-- STATS TAB (THE 50K SPEED FIX)
---------------------------------------------------------
local RunService = game:GetService("RunService")
local speedConn

local SpeedToggle = Tabs.Stats:AddToggle("MovementToggle", {
    Title = "Enable God Speed (Base Escape)",
    Default = false
})

SpeedToggle:OnChanged(function()
    if speedConn then speedConn:Disconnect() end
    if Options.MovementToggle.Value then
        speedConn = RunService.Stepped:Connect(function()
            local folder = workspace:FindFirstChild("RunningModels")
            if folder then
                for _, model in ipairs(folder:GetChildren()) do
                    if model:GetAttribute("OwnerId") == game.Players.LocalPlayer.UserId then
                        -- Forces speed to override carrying weight
                        model:SetAttribute("MovementSpeed", Options.MovementSlider.Value)
                    end
                end
            end
        end)
    end
end)

Tabs.Stats:AddSlider("MovementSlider", {
    Title = "Brainrot Speed",
    Default = 10000,
    Min = 50,
    Max = 50000,
    Rounding = 0
})

---------------------------------------------------------
-- SELL TAB
---------------------------------------------------------
-- Your existing Sell logic using SellBrainrot RF

---------------------------------------------------------
-- SETTINGS
---------------------------------------------------------
SaveManager:SetLibrary(Fluent)
InterfaceManager:SetLibrary(Fluent)
InterfaceManager:BuildInterfaceSection(Tabs.Settings)
SaveManager:BuildConfigSection(Tabs.Settings)
Window:SelectTab(1)
end
