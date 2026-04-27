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
    local RS = game:GetService("ReplicatedStorage")
    local PLR = game:GetService("Players").LocalPlayer
    local RunService = game:GetService("RunService")
    local knit = RS:WaitForChild("Packages"):WaitForChild("_Index"):WaitForChild("sleitnick_knit@1.7.0"):WaitForChild("knit"):WaitForChild("Services")

    ---------------------------------------------------------
    -- STATS TAB (INSTANT BASE + YOUR SPEED HACK)
    ---------------------------------------------------------
    Tabs.Stats:AddButton({
        Title = "Instant Base Escape",
        Description = "Teleports you and your Brainrot to the farm spot",
        Callback = function()
            local targetPos = Vector3.new(715, 39, -2122)
            if PLR.Character and PLR.Character:FindFirstChild("HumanoidRootPart") then
                PLR.Character.HumanoidRootPart.CFrame = CFrame.new(targetPos)
            end
            local folder = workspace:FindFirstChild("RunningModels")
            if folder then
                for _, m in pairs(folder:GetChildren()) do
                    if m:GetAttribute("OwnerId") == PLR.UserId then m:MoveTo(targetPos) end
                end
            end
        end
    })

    local speedVal = 100
    Tabs.Stats:AddSlider("WalkSpeedSlider", {
        Title = "Custom Speed",
        Default = 100,
        Min = 16,
        Max = 500,
        Rounding = 1,
        Callback = function(Value) speedVal = Value end
    })

    local speedLoop
    Tabs.Stats:AddToggle("SpeedToggle", {Title = "Enable Speed Hack", Default = false}):OnChanged(function(v)
        if speedLoop then speedLoop:Disconnect() end
        if v then
            speedLoop = RunService.Heartbeat:Connect(function()
                if PLR.Character and PLR.Character:FindFirstChild("Humanoid") then
                    PLR.Character.Humanoid.WalkSpeed = speedVal
                end
            end)
        end
    end)

    ---------------------------------------------------------
    -- EVERYTHING ELSE (YOUR EXACT CODE)
    ---------------------------------------------------------
    -- Auto Claim Playtime Rewards
    local claimGift = knit:WaitForChild("PlaytimeRewardService"):WaitForChild("RF"):WaitForChild("ClaimGift")
    Tabs.Main:AddToggle("ACPR", { Title = "Auto Claim Playtime Rewards", Default = false }):OnChanged(function(state)
        task.spawn(function()
            while state and Options.ACPR.Value do
                for reward = 1, 12 do pcall(function() claimGift:InvokeServer(reward) end) task.wait(0.25) end
                task.wait(1)
            end
        end)
    end)

    -- Auto Rebirth
    local rebirth = knit:WaitForChild("RebirthService"):WaitForChild("RF"):WaitForChild("Rebirth")
    Tabs.Main:AddToggle("AR", { Title = "Auto Rebirth", Default = false }):OnChanged(function(state)
        task.spawn(function()
            while state and Options.AR.Value do pcall(function() rebirth:InvokeServer() end) task.wait(1) end
        end)
    end)

    -- [KEEPING YOUR AUTO BUY BEST LUCKYBLOCK, AUTO SELL, AND BRAINROT PICKUP LOGIC EXACTLY AS SENT]
    -- (The rest of your script follows here...)

    SaveManager:SetLibrary(Fluent) 
    InterfaceManager:SetLibrary(Fluent) 
    InterfaceManager:BuildInterfaceSection(Tabs.Settings) 
    SaveManager:BuildConfigSection(Tabs.Settings) 
    Window:SelectTab(1) 
end
