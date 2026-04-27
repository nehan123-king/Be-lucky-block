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
    local dashConn

    ---------------------------------------------------------
    -- WORKING TELEPORT
    ---------------------------------------------------------
    Tabs.Stats:AddButton({
        Title = "Instant Base Escape",
        Description = "Teleports you out instantly",
        Callback = function()
            local folder = workspace:FindFirstChild("RunningModels")
            local targetPos = Vector3.new(715, 39, -2122) 
            if folder then
                for _, model in ipairs(folder:GetChildren()) do
                    if model:GetAttribute("OwnerId") == player.UserId then
                        model:MoveTo(targetPos)
                    end
                end
            end
            if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                player.Character.HumanoidRootPart.CFrame = CFrame.new(targetPos)
            end
        end
    })

    ---------------------------------------------------------
    -- BYPASS DASH (REPLACES BROKEN SPEED)
    ---------------------------------------------------------
    local DashToggle = Tabs.Stats:AddToggle("DashToggle", {
        Title = "Enable Bypass Dash", 
        Description = "Move fast without getting kicked",
        Default = false
    })
    
    DashToggle:OnChanged(function()
        if dashConn then dashConn:Disconnect() end
        if Options.DashToggle.Value then
            dashConn = RunService.Heartbeat:Connect(function()
                local char = player.Character
                local hum = char and char:FindFirstChild("Humanoid")
                local root = char and char:FindFirstChild("HumanoidRootPart")
                
                if hum and root and hum.MoveDirection.Magnitude > 0 then
                    -- This teleports you forward 2 studs every frame
                    -- It confuses the server's speed check
                    root.CFrame = root.CFrame + (hum.MoveDirection * (Options.DashPower.Value / 10))
                end
            end)
        end
    end)

    Tabs.Stats:AddSlider("DashPower", {
        Title = "Dash Power", 
        Default = 5, 
        Min = 1, 
        Max = 15, 
        Rounding = 1
    })

    ---------------------------------------------------------
    -- FINAL CONFIG
    ---------------------------------------------------------
    SaveManager:SetLibrary(Fluent)
    InterfaceManager:SetLibrary(Fluent)
    InterfaceManager:BuildInterfaceSection(Tabs.Settings)
    SaveManager:BuildConfigSection(Tabs.Settings)
    Window:SelectTab(1)
end
