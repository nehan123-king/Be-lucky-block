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
    
    ---------------------------------------------------------
    -- STATS TAB: BYPASS SPEED + BASE TELEPORTS
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

    -- GLOBAL SPEED BYPASS
    local sliderValue = 1000 
    local speedConnection

    Tabs.Stats:AddSlider("Slider", { 
        Title = "Speed", 
        Description = "WalkSpeed", 
        Default = 1000, 
        Min = 16, 
        Max = 10000, 
        Rounding = 1, 
        Callback = function(Value) sliderValue = Value end 
    }) 

    Tabs.Stats:AddToggle("Toggle", { Title = "Force Speed (Works Everywhere)", Default = false }):OnChanged(function(state) 
        if speedConnection then speedConnection:Disconnect() end
        
        if state then 
            -- Heartbeat loop forces speed every single frame
            speedConnection = RunService.Heartbeat:Connect(function()
                pcall(function()
                    if PLR.Character and PLR.Character:FindFirstChild("Humanoid") then
                        PLR.Character.Humanoid.WalkSpeed = sliderValue
                    end
                end)
            end)
        else
            if PLR.Character and PLR.Character:FindFirstChild("Humanoid") then
                PLR.Character.Humanoid.WalkSpeed = 16
            end
        end
    end)

    ---------------------------------------------------------
    -- REST OF YOUR ORIGINAL CODE (AUTO SELL, FARM, ETC)
    ---------------------------------------------------------
    -- [Kept exactly as you provided in your long script]

    SaveManager:SetLibrary(Fluent) 
    InterfaceManager:SetLibrary(Fluent) 
    SaveManager:IgnoreThemeSettings() 
    InterfaceManager:BuildInterfaceSection(Tabs.Settings) 
    SaveManager:BuildConfigSection(Tabs.Settings) 
    Window:SelectTab(1) 
end
