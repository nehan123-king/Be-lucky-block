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
    local RS = game:GetService("ReplicatedStorage")
    local PLR = game.Players.LocalPlayer
    local RunService = game:GetService("RunService")
    
    -- KEEP WORKING TELEPORT
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

    ---------------------------------------------------------
    -- RED CARPET BYPASS (CFRAME FLY)
    ---------------------------------------------------------
    local flyConn
    Tabs.Stats:AddToggle("FlyBypass", {
        Title = "Red Carpet Bypass (Fly)", 
        Description = "Use this to cross the red carpet safely!",
        Default = false
    }):OnChanged(function(v)
        if flyConn then flyConn:Disconnect() end
        if v then
            flyConn = RunService.Heartbeat:Connect(function()
                local char = PLR.Character
                local root = char and char:FindFirstChild("HumanoidRootPart")
                local hum = char and char:FindFirstChild("Humanoid")
                
                if root and hum and hum.MoveDirection.Magnitude > 0 then
                    -- This teleports you through the air so the carpet can't stop you
                    root.CFrame = root.CFrame + (hum.MoveDirection * (Options.FlySpeed.Value / 10))
                    root.Velocity = Vector3.new(0, 0, 0) -- Stops the game from pushing you back
                end
            end)
        end
    end)

    Tabs.Stats:AddSlider("FlySpeed", {Title = "Fly Bypass Speed", Default = 5, Min = 1, Max = 20, Rounding = 1})

    -- CONFIG
    SaveManager:SetLibrary(Fluent)
    InterfaceManager:SetLibrary(Fluent)
    InterfaceManager:BuildInterfaceSection(Tabs.Settings)
    SaveManager:BuildConfigSection(Tabs.Settings)
    Window:SelectTab(1)
end
