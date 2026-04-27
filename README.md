local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()

local Window = Fluent:CreateWindow({
    Title = "Be a Brainrot",
    SubTitle = "NAH HUB | nehan",
    TabWidth = 160,
    Size = UDim2.fromOffset(550, 430),
    Acrylic = false,
    Theme = "Darker"
})

local Tabs = {
    Main = Window:AddTab({ Title = "Main", Icon = "box" }),
    Teleports = Window:AddTab({ Title = "Teleports", Icon = "map-pin" }),
    Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
}

local Options = Fluent.Options

do
    local PLR = game.Players.LocalPlayer
    
    local function TeleportAll(targetPos)
        if PLR.Character and PLR.Character:FindFirstChild("HumanoidRootPart") then
            -- Bypass move to ensure the Brainrot follows
            PLR.Character.HumanoidRootPart.CFrame = CFrame.new(targetPos)
        end
        local folder = workspace:FindFirstChild("RunningModels")
        if folder then
            for _, m in pairs(folder:GetChildren()) do
                if m:GetAttribute("OwnerId") == PLR.UserId then 
                    m:MoveTo(targetPos) 
                end
            end
        end
    end

    -- TELEPORTS TAB
    Tabs.Teleports:AddDropdown("BaseSelector", {
        Title = "Select Destination",
        Values = {
            "Slayer", "Knight Judge", "Noob's Base", "David (Builderman)", 
            "Farmer", "Mafia's Base", "Granny's Base", "Cowboy's Base", 
            "Mummy's Base", "Werewolf's Base", "Vampire's Base", 
            "Lifeguard's Base", "Pirate's Base", "Diver's Base", "Shark's Base"
        },
        Default = "Slayer",
    })

    Tabs.Teleports:AddButton({
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
                TeleportAll(coords[choice])
            end
        end
    })

    Tabs.Teleports:AddButton({
        Title = "Warp Forward (Manual)",
        Callback = function()
            local root = PLR.Character and PLR.Character:FindFirstChild("HumanoidRootPart")
            if root then root.CFrame = root.CFrame * CFrame.new(0, 0, -200) end
        end
    })

    -- CONFIG
    SaveManager:SetLibrary(Fluent)
    InterfaceManager:SetLibrary(Fluent)
    InterfaceManager:BuildInterfaceSection(Tabs.Settings)
    SaveManager:BuildConfigSection(Tabs.Settings)
end
