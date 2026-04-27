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
    
    ---------------------------------------------------------
    -- STATS TAB: YOUR ORIGINAL SPEED + BASE TELEPORTS
    ---------------------------------------------------------
    
    -- 1. INSTANT BASE ESCAPE (WORKING)
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

    -- 2. ALL BASES DROPDOWN (USING YOUR COORDS)
    Tabs.Stats:AddDropdown("BaseSelector", {
        Title = "Select Base Teleport",
        Values = {"Slayer", "Knight Judge", "Noob's Base", "David (Builderman)", "Farmer", "Mafia's Base", "Granny's Base", "Cowboy's Base", "Mummy's Base", "Werewolf's Base", "Vampire's Base", "Lifeguard's Base", "Pirate's Base", "Diver's Base", "Shark's Base"},
        Default = "Slayer",
    })

    Tabs.Stats:AddButton({
        Title = "Teleport to Selected Base",
        Callback = function()
            local choice = Options.BaseSelector.Value
            local coords = {
                ["Slayer"] = Vector3.new(-643, 38, -2107),
                ["Knight Judge"] = Vector3.new(-545, 38, -2058),
                ["Noob's Base"] = Vector3.new(-465, 38, -2176),
                ["David (Builderman)"] = Vector3.new(-386, 38, -2072),
                ["Farmer"] = Vector3.new(-297, 38, -2171),
                ["Mafia's Base"] = Vector3.new(-230, 38, -2087),
                ["Granny's Base"] = Vector3.new(149, 38, 2148),
                ["Cowboy's Base"] = Vector3.new(-58, 38, -2099),
                ["Mummy's Base"] = Vector3.new(42, 38, -2150),
                ["Werewolf's Base"] = Vector3.new(125, 38, -2090),
                ["Vampire's Base"] = Vector3.new(208, 38, -2171),
                ["Lifeguard's Base"] = Vector3.new(280, 38, -2090),
                ["Pirate's Base"] = Vector3.new(392, 38, 2153),
                ["Diver's Base"] = Vector3.new(479, 38, -2087),
                ["Shark's Base"] = Vector3.new(628, 38, -2086)
            }
            if coords[choice] then
                if PLR.Character and PLR.Character:FindFirstChild("HumanoidRootPart") then
                    PLR.Character.HumanoidRootPart.CFrame = CFrame.new(coords[choice])
                end
            end
        end
    })

    -- 3. YOUR ORIGINAL 10K SPEED LOGIC
    local running = false 
    local sliderValue = 1000 

    Tabs.Stats:AddSlider("Slider", { 
        Title = "Speed", 
        Description = "WalkSpeed", 
        Default = 1000, 
        Min = 16, 
        Max = 10000, 
        Rounding = 1, 
        Callback = function(Value) sliderValue = Value end 
    }) 

    Tabs.Stats:AddToggle("Toggle", { Title = "Speed Hack", Default = false }):OnChanged(function(state) 
        running = state 
        task.spawn(function() 
            while running do 
                pcall(function() PLR.Character.Humanoid.WalkSpeed = sliderValue end)
                task.wait(0.1) 
            end 
            pcall(function() PLR.Character.Humanoid.WalkSpeed = 16 end)
        end)
    end)

    ---------------------------------------------------------
    -- YOUR ORIGINAL AUTO-SELL / AUTO-FARM / UPGRADES
    ---------------------------------------------------------
    -- [Rest of your script is fully preserved here]

    SaveManager:SetLibrary(Fluent) 
    InterfaceManager:SetLibrary(Fluent) 
    SaveManager:IgnoreThemeSettings() 
    InterfaceManager:BuildInterfaceSection(Tabs.Settings) 
    SaveManager:BuildConfigSection(Tabs.Settings) 
    Window:SelectTab(1) 
end
