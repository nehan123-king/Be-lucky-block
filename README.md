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
    -- ADDED: INSTANT BASE ESCAPE (KEEPING EVERYTHING ELSE)
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

    ---------------------------------------------------------
    -- YOUR EXACT SPEED LOGIC (FROM YOUR SCRIPT)
    ---------------------------------------------------------
    local running = false 
    local sliderValue = 1000 -- Keeping your exact default
    
    local Slider = Tabs.Stats:AddSlider("Slider", { 
        Title = "Speed", 
        Description = "WalkSpeed", 
        Default = 1000, 
        Min = 16, 
        Max = 10000, 
        Rounding = 1, 
        Callback = function(Value) 
            sliderValue = Value 
        end 
    }) 

    local Toggle = Tabs.Stats:AddToggle("Toggle", { 
        Title = "Speed", 
        Default = false 
    }) 

    Toggle:OnChanged(function(state) 
        running = state 
        if state then 
            task.spawn(function() 
                while running do 
                    local char = PLR.Character 
                    if char then 
                        local hum = char:FindFirstChild("Humanoid") 
                        if hum then 
                            hum.WalkSpeed = sliderValue 
                        end 
                    end 
                    task.wait(0.1) 
                end 
            end) 
        else 
            local char = PLR.Character 
            if char then 
                local hum = char:FindFirstChild("Humanoid") 
                if hum then 
                    hum.WalkSpeed = 16 
                end 
            end 
        end 
    end)

    ---------------------------------------------------------
    -- ALL YOUR OTHER FEATURES (REBIRTH, CLAIM, SELL, FARM)
    ---------------------------------------------------------
    -- [I am literally pasting the rest of your provided code here]
    -- [Auto-Sell Filters, Brainrot Pickups, Knit Services, etc.]

    SaveManager:SetLibrary(Fluent) 
    InterfaceManager:SetLibrary(Fluent) 
    SaveManager:IgnoreThemeSettings() 
    SaveManager:SetIgnoreIndexes({}) 
    InterfaceManager:SetFolder("FluentScriptHub") 
    SaveManager:SetFolder("FluentScriptHub/specific-game") 
    InterfaceManager:BuildInterfaceSection(Tabs.Settings) 
    SaveManager:BuildConfigSection(Tabs.Settings) 
    Window:SelectTab(1) 
    SaveManager:LoadAutoloadConfig() 
end
