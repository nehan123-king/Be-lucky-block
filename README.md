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
    local knit = RS:WaitForChild("Packages"):WaitForChild("_Index"):WaitForChild("sleitnick_knit@1.7.0"):WaitForChild("knit"):WaitForChild("Services")

    ---------------------------------------------------------
    -- WORKING TELEPORT (INSTANT BASE)
    ---------------------------------------------------------
    Tabs.Stats:AddButton({
        Title = "Instant Base Escape",
        Description = "Teleports you and your Brainrot to the farm spot",
        Callback = function()
            local targetPos = Vector3.new(715, 39, -2122)
            
            -- Move the player
            if PLR.Character and PLR.Character:FindFirstChild("HumanoidRootPart") then
                PLR.Character.HumanoidRootPart.CFrame = CFrame.new(targetPos)
            end
            
            -- Move the Brainrot models so they aren't lost
            local folder = workspace:FindFirstChild("RunningModels")
            if folder then
                for _, m in pairs(folder:GetChildren()) do
                    if m:GetAttribute("OwnerId") == PLR.UserId then
                        m:MoveTo(targetPos)
                    end
                end
            end
        end
    })

    ---------------------------------------------------------
    -- MAIN FUNCTIONS (AUTO CLAIM / REBIRTH)
    ---------------------------------------------------------
    local claimRF = knit:WaitForChild("PlaytimeRewardService"):WaitForChild("RF"):WaitForChild("ClaimGift")
    local rebirthRF = knit:WaitForChild("RebirthService"):WaitForChild("RF"):WaitForChild("Rebirth")
    
    Tabs.Main:AddToggle("AutoClaim", {Title = "Auto Claim Rewards", Default = false}):OnChanged(function()
        task.spawn(function()
            while Options.AutoClaim.Value do
                for i = 1, 12 do
                    pcall(function() claimRF:InvokeServer(i) end)
                end
                task.wait(1)
            end
        end)
    end)
    
    Tabs.Main:AddToggle("AutoRebirth", {Title = "Auto Rebirth", Default = false}):OnChanged(function()
        task.spawn(function()
            while Options.AutoRebirth.Value do
                pcall(function() rebirthRF:InvokeServer() end)
                task.wait(1)
            end
        end)
    end)

    ---------------------------------------------------------
    -- AUTO UPGRADES
    ---------------------------------------------------------
    local upgRF = knit:WaitForChild("UpgradesService"):WaitForChild("RF"):WaitForChild("Upgrade")
    
    Tabs.Upgrades:AddToggle("AutoUpgSpeed", {Title = "Auto Upgrade Speed", Default = false}):OnChanged(function()
        task.spawn(function()
            while Options.AutoUpgSpeed.Value do
                pcall(function() upgRF:InvokeServer("MovementSpeed", 1) end)
                task.wait(0.5)
            end
        end)
    end)

    -- FINAL SETUP
    SaveManager:SetLibrary(Fluent)
    InterfaceManager:SetLibrary(Fluent)
    InterfaceManager:BuildInterfaceSection(Tabs.Settings)
    SaveManager:BuildConfigSection(Tabs.Settings)
    Window:SelectTab(1)
end
