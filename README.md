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
    
    -- INSTANT ESCAPE (STILL WORKS)
    Tabs.Stats:AddButton({
        Title = "Instant Base Escape",
        Callback = function()
            local target = Vector3.new(715, 39, -2122)
            for _, m in pairs(workspace.RunningModels:GetChildren()) do
                if m:GetAttribute("OwnerId") == PLR.UserId then m:MoveTo(target) end
            end
            if PLR.Character then PLR.Character:MoveTo(target) end
        end
    })

    ---------------------------------------------------------
    -- THE CARPET GHOST (PHYSICS BYPASS)
    ---------------------------------------------------------
    local ghostConn
    Tabs.Stats:AddToggle("CarpetGhost", {
        Title = "Carpet Ghost Mode", 
        Description = "Walk through the carpet and anti-cheats",
        Default = false
    }):OnChanged(function(v)
        if ghostConn then ghostConn:Disconnect() end
        if v then
            ghostConn = RunService.Stepped:Connect(function()
                local char = PLR.Character
                if char then
                    -- This disables collisions so the carpet sensors miss you
                    for _, part in pairs(char:GetDescendants()) do
                        if part:IsA("BasePart") then part.CanCollide = false end
                    end
                    
                    local root = char:FindFirstChild("HumanoidRootPart")
                    local hum = char:FindFirstChild("Humanoid")
                    if root and hum and hum.MoveDirection.Magnitude > 0 then
                        -- Smooth CFrame movement that bypasses velocity checks
                        root.CFrame = root.CFrame + (hum.MoveDirection * (Options.GhostSpeed.Value / 10))
                    end
                end
            end)
        end
    end)

    Tabs.Stats:AddSlider("GhostSpeed", {Title = "Ghost Speed", Default = 8, Min = 1, Max = 15, Rounding = 1})

    -- CONFIG
    SaveManager:SetLibrary(Fluent)
    InterfaceManager:SetLibrary(Fluent)
    InterfaceManager:BuildInterfaceSection(Tabs.Settings)
    SaveManager:BuildConfigSection(Tabs.Settings)
    Window:SelectTab(1)
end
