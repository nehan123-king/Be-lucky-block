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
    end

    -- TELEPORTS TAB
    Tabs.Teleports:AddButton({
        Title = "Warp Forward (500 Studs)",
        Description = "Jumps you forward in the direction you are facing",
        Callback = function()
            local char = PLR.Character
            local root = char and char:FindFirstChild("HumanoidRootPart")
            if root then
                -- Moves you forward based on where your camera/character looks
                root.CFrame = root.CFrame * CFrame.new(0, 5, -500)
            end
        end
    })

    Tabs.Teleports:AddButton({
        Title = "Get My Current Coords (Check F9)",
        Description = "Use this once you reach Slayer!",
        Callback = function()
            local pos = PLR.Character.HumanoidRootPart.Position
            print("Slayer Coords: Vector3.new(" .. math.floor(pos.X) .. ", " .. math.floor(pos.Y) .. ", " .. math.floor(pos.Z) .. ")")
            Fluent:Notify({Title = "Coords Logged!", Content = "Check F9 console for the Slayer numbers", Duration = 5})
        end
    })

    -- [Dropdown and other buttons stay here for when we have the coords]

    SaveManager:SetLibrary(Fluent)
    InterfaceManager:SetLibrary(Fluent)
    InterfaceManager:BuildInterfaceSection(Tabs.Settings)
    SaveManager:BuildConfigSection(Tabs.Settings)
end
