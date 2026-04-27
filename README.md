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
            PLR.Character.HumanoidRootPart.CFrame = CFrame.new(targetPos)
        end
        local folder = workspace:FindFirstChild("RunningModels")
        if folder then
            for _, m in pairs(folder:GetChildren()) do
                if m:GetAttribute("OwnerId") == PLR.UserId then m:MoveTo(targetPos) end
            end
        end
    end

    -- TELEPORTS TAB
    Tabs.Teleports:AddButton({
        Title = "Instant Base Escape",
        Description = "Teleports to the Main Farm",
        Callback = function() TeleportAll(Vector3.new(715, 39, -2122)) end
    })

    Tabs.Teleports:AddDropdown("BaseSelector", {
        Title = "Select Base",
        Values = {
            "Noob's Base", "Builderman (David)", "Farmer", "Mafia's Base", 
            "Granny's Base", "Cowboy's Base", "Mummy's Base", "Werewolf's Base", 
            "Vampire's Base", "Lifeguard's Base", "Pirate's Base", "Diver's Base", 
            "Shark's Base", "Slayer", "Knight Judge"
        },
        Default = "Noob's Base",
    })

    Tabs.Teleports:AddButton({
        Title = "Teleport to Selection",
        Callback = function()
            local choice = Options.BaseSelector.Value
            if choice == "Noob's Base" then TeleportAll(Vector3.new(0, 5, 0))
            elseif choice == "Slayer" then TeleportAll(Vector3.new(715, 39, -2122))
            elseif choice == "Builderman (David)" then TeleportAll(Vector3.new(100, 5, 100))
            -- Add the other coordinates here as you find them!
            end
        end
    })

    Tabs.Teleports:AddButton({
        Title = "Get My Current Coords (Check F9)",
        Callback = function()
            local pos = PLR.Character.HumanoidRootPart.Position
            print("Current Coords: Vector3.new(" .. math.floor(pos.X) .. ", " .. math.floor(pos.Y) .. ", " .. math.floor(pos.Z) .. ")")
            Fluent:Notify({Title = "Coords Copied to Console", Content = "Press F9 to see them!", Duration = 5})
        end
    })

    -- CONFIG
    SaveManager:SetLibrary(Fluent)
    InterfaceManager:SetLibrary(Fluent)
    InterfaceManager:BuildInterfaceSection(Tabs.Settings)
    SaveManager:BuildConfigSection(Tabs.Settings)
end
