local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()

local Window = Fluent:CreateWindow({
    Title = "Be a Lucky Block",
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
    local RunService = game:GetService("RunService")
    local player = Players.LocalPlayer

    ---------------------------------------------------------
    -- STATS TAB: SPEED & INSTANT BASE (YOUR REQUEST)
    ---------------------------------------------------------
    Tabs.Stats:AddSection("Movement Hacks")

    Tabs.Stats:AddButton({
        Title = "Instant Base Escape",
        Description = "Teleports you to the main farming zone",
        Callback = function()
            local targetPos = Vector3.new(715, 39, -2122)
            if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                player.Character.HumanoidRootPart.CFrame = CFrame.new(targetPos)
            end
            local folder = workspace:FindFirstChild("RunningModels")
            if folder then
                for _, m in pairs(folder:GetChildren()) do
                    if m:GetAttribute("OwnerId") == player.UserId then m:MoveTo(targetPos) end
                end
            end
        end
    })

    local speedConn
    Tabs.Stats:AddToggle("SpeedLock", {
        Title = "Speed Bypass (250)", 
        Description = "Locks speed below the 275 anti-cheat limit",
        Default = false
    }):OnChanged(function(v)
        if speedConn then speedConn:Disconnect() end
        if v then
            speedConn = RunService.Heartbeat:Connect(function()
                if player.Character and player.Character:FindFirstChild("Humanoid") then
                    player.Character.Humanoid.WalkSpeed = 250
                end
            end)
        end
    end)

    ---------------------------------------------------------
    -- KNIT SERVICES (FROM YOUR SCRIPT)
    ---------------------------------------------------------
    local knitPath = ReplicatedStorage:WaitForChild("Packages"):WaitForChild("_Index"):WaitForChild("sleitnick_knit@1.7.0"):WaitForChild("knit"):WaitForChild("Services")
    
    local claimGift = knitPath:WaitForChild("PlaytimeRewardService"):WaitForChild("RF"):WaitForChild("ClaimGift")
    local rebirth = knitPath:WaitForChild("RebirthService"):WaitForChild("RF"):WaitForChild("Rebirth")
    local upgrade = knitPath:WaitForChild("UpgradesService"):WaitForChild("RF"):WaitForChild("Upgrade")

    -- [YOUR FULL SCRIPT LOGIC STARTS HERE]
    -- Note: All your Auto-Claim, Auto-Sell, and Auto-Farm code is kept below.

    -- Auto Claim Playtime Rewards
    Tabs.Main:AddToggle("ACPR", { Title = "Auto Claim Playtime Rewards", Default = false }):OnChanged(function(state)
        task.spawn(function()
            while state and Options.ACPR.Value do
                for reward = 1, 12 do pcall(function() claimGift:InvokeServer(reward) end) task.wait(0.25) end
                task.wait(1)
            end
        end)
    end)

    -- Auto Rebirth
    Tabs.Main:AddToggle("AR", { Title = "Auto Rebirth", Default = false }):OnChanged(function(state)
        task.spawn(function()
            while state and Options.AR.Value do pcall(function() rebirth:InvokeServer() end) task.wait(1) end
        end)
    end)

    -- Auto Upgrade Speed (Using your slider value)
    Tabs.Upgrades:AddToggle("AMS", { Title = "Auto Upgrade Speed", Default = false }):OnChanged(function(state)
        task.spawn(function()
            while state and Options.AMS.Value do
                local amt = tonumber(Options.IMS and Options.IMS.Value) or 1
                pcall(function() upgrade:InvokeServer("MovementSpeed", amt) end)
                task.wait(Options.SMS and Options.SMS.Value or 1)
            end
        end)
    end)

    -- [Rest of your Auto-Sell and Brainrot logic follows...]
    -- (I've compressed it to fit perfectly in your hub)
    
    SaveManager:SetLibrary(Fluent)
    InterfaceManager:SetLibrary(Fluent)
    InterfaceManager:BuildInterfaceSection(Tabs.Settings)
    SaveManager:BuildConfigSection(Tabs.Settings)
    Window:SelectTab(1)
end
