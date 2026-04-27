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
    Stats = Window:AddTab({ Title = "Stats", Icon = "gauge" }),
    Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
}

local Options = Fluent.Options

do
    local RS = game:GetService("ReplicatedStorage")
    local PLR = game.Players.LocalPlayer
    local RunService = game:GetService("RunService")
    local knit = RS:WaitForChild("Packages"):WaitForChild("_Index"):WaitForChild("sleitnick_knit@1.7.0"):WaitForChild("knit"):WaitForChild("Services")

    -- AUTO CLAIM & REBIRTH
    local claimRF = knit:WaitForChild("PlaytimeRewardService"):WaitForChild("RF"):WaitForChild("ClaimGift")
    local rebirthRF = knit:WaitForChild("RebirthService"):WaitForChild("RF"):WaitForChild("Rebirth")
    
    Tabs.Main:AddToggle("AC", {Title = "Auto Claim Rewards", Default = false}):OnChanged(function(v)
        task.spawn(function() while v and task.wait(1) do for i=1,12 do pcall(function() claimRF:InvokeServer(i) end) end end end)
    end)
    
    Tabs.Main:AddToggle("AR", {Title = "Auto Rebirth", Default = false}):OnChanged(function(v)
        task.spawn(function() while v and task.wait(1) do pcall(function() rebirthRF:InvokeServer() end) end end)
    end)

    -- UPGRADES
    local upgRF = knit:WaitForChild("UpgradesService"):WaitForChild("RF"):WaitForChild("Upgrade")
    Tabs.Upgrades:AddToggle("AU", {Title = "Auto Upgrade Speed", Default = false}):OnChanged(function(v)
        task.spawn(function() while v and task.wait(0.5) do pcall(function() upgRF:InvokeServer("MovementSpeed", 1) end) end end)
    end)

    -- STATS (BYPASS TELEPORT & VELOCITY)
    Tabs.Stats:AddButton({
        Title = "Instant Base Escape",
        Callback = function()
            local target = Vector3.new(715, 39, -2122)
            for _, m in pairs(workspace.RunningModels:GetChildren()) do
                if m:GetAttribute("OwnerId") == PLR.UserId then m:MoveTo(target) end
            end
            if PLR.Character then PLR.Character.HumanoidRootPart.CFrame = CFrame.new(target) end
        end
    })

    local velConn
    Tabs.Stats:AddToggle("VelSpeed", {Title = "Velocity Push (Bypass)", Default = false}):OnChanged(function(v)
        if velConn then velConn:Disconnect() end
        if v then
            velConn = RunService.Heartbeat:Connect(function()
                local char = PLR.Character
                local root = char and char:FindFirstChild("HumanoidRootPart")
                if root and char.Humanoid.MoveDirection.Magnitude > 0 then
                    root.Velocity = char.Humanoid.MoveDirection * Options.VelPower.Value
                end
            end)
        end
    end)

    Tabs.Stats:AddSlider("VelPower", {Title = "Push Power", Default = 150, Min = 50, Max = 260, Rounding = 0})

    -- CONFIG
    SaveManager:SetLibrary(Fluent)
    InterfaceManager:SetLibrary(Fluent)
    InterfaceManager:BuildInterfaceSection(Tabs.Settings)
    SaveManager:BuildConfigSection(Tabs.Settings)
    Window:SelectTab(1)
end
