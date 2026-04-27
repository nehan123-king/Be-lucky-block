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
    Stats = Window:AddTab({ Title = "Stats", Icon = "gauge" }),
    Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
}

local Options = Fluent.Options

do
    local RunService = game:GetService("RunService")
    local player = game.Players.LocalPlayer
    local speedConn

    ---------------------------------------------------------
    -- BYPASS SPEED LOGIC
    ---------------------------------------------------------
    local SpeedToggle = Tabs.Stats:AddToggle("MovementToggle", {
        Title = "Enable Stealth Speed", 
        Description = "Bypasses the 275 speed limit kick",
        Default = false
    })
    
    SpeedToggle:OnChanged(function()
        if speedConn then speedConn:Disconnect() end
        if Options.MovementToggle.Value then
            speedConn = RunService.Heartbeat:Connect(function()
                -- 1. Fix Character Speed (Prevents Anti-Cheat Kick)
                if player.Character and player.Character:FindFirstChild("Humanoid") then
                    player.Character.Humanoid.WalkSpeed = 16 
                end

                -- 2. Boost the Lucky Block Model (Allows Base Escape)
                local folder = workspace:FindFirstChild("RunningModels")
                if folder then
                    for _, model in ipairs(folder:GetChildren()) do
                        if model:GetAttribute("OwnerId") == player.UserId then
                            -- We stay at 250 internally to avoid the '275 limit'
                            local safeVal = math.min(Options.MovementSlider.Value, 250)
                            model:SetAttribute("MovementSpeed", safeVal)
                        end
                    end
                end
            end)
        end
    end)

    Tabs.Stats:AddSlider("MovementSlider", {
        Title = "Safe Speed (Max 270)", 
        Default = 200, 
        Min = 16, 
        Max = 270, -- Hard capped to stay under the 275 limit
        Rounding = 0
    })

    ---------------------------------------------------------
    -- [PLACE YOUR OTHER FUNCTIONS HERE]
    ---------------------------------------------------------

    SaveManager:SetLibrary(Fluent)
    InterfaceManager:SetLibrary(Fluent)
    InterfaceManager:BuildInterfaceSection(Tabs.Settings)
    SaveManager:BuildConfigSection(Tabs.Settings)
    Window:SelectTab(1)
end
