# hack
enter my text

-- CONFIGURAÇÕES VISUAIS E ÁUDIO
local ID_IMG = "rbxassetid://87267513827945"
local ID_SONG = "rbxassetid://1839247124"
local TEXTO_HINT = "THIS GAME GOT HACKED BY NICKNAME4190 NOOBS"

local players = game:GetService("Players")
local lighting = game:GetService("Lighting")
local me = players.LocalPlayer or players:GetPlayers()[1]

-- 1. PROTOCOLO CAOS (SOM E VISUAL)
task.spawn(function()
    -- Música Vol 5
    local s = Instance.new("Sound", workspace)
    s.SoundId = ID_SONG
    s.Volume = 5
    s.Looped = true
    s:Play()
    
    -- Skybox
    lighting:ClearAllChildren()
    local sky = Instance.new("Sky", lighting)
    for _, f in pairs({"SkyboxBk","SkyboxDn","SkyboxFt","SkyboxLf","SkyboxRt","SkyboxUp"}) do sky[f] = ID_IMG end
    
    -- Hint
    local h = Instance.new("Hint", workspace)
    h.Text = TEXTO_HINT
end)

-- 2. ANIMAÇÃO FE (HEADSHAKE + CHICKEN ARMS)
task.spawn(function()
    local char = me.Character or me.CharacterAdded:Wait()
    local hum = char:WaitForChild("Humanoid")
    local torso = char:FindFirstChild("Torso")
    local neck = char:FindFirstChild("Neck", true)

    if hum.RigType == Enum.HumanoidRigType.R6 and torso then
        local rs = torso:FindFirstChild("Right Shoulder")
        local ls = torso:FindFirstChild("Left Shoulder")
        if rs and ls then
            rs.C0 = CFrame.new(1.4, 0.5, -0.5) * CFrame.Angles(0, 0, math.rad(90))
            ls.C0 = CFrame.new(-1.4, 0.5, -0.5) * CFrame.Angles(0, 0, math.rad(-90))
        end
    end

    local x, y, z = neck.C0.X, neck.C0.Y, neck.C0.Z
    while char and char.Parent do
        local sway = math.sin(os.clock() * 6) * 0.8
        if neck then
            neck.C0 = CFrame.new(x + sway, y, z) * (hum.RigType == Enum.HumanoidRigType.R6 and CFrame.Angles(3 * math.pi/2, 0, math.pi) or CFrame.new())
        end
        task.wait()
    end
end)

-- 3. INTERFACE: BOTÃO EMERGENCY SERVER HOP
local ScreenGui = Instance.new("ScreenGui", me:WaitForChild("PlayerGui"))
local HopButton = Instance.new("TextButton", ScreenGui)
local UICorner = Instance.new("UICorner", HopButton)

HopButton.Name = "HopButton"
HopButton.Size = UDim2.new(0, 120, 0, 40)
HopButton.Position = UDim2.new(0.5, -60, 0.05, 0)
HopButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
HopButton.Text = "SERVER HOP"
HopButton.TextColor3 = Color3.new(1, 1, 1)
HopButton.Font = Enum.Font.SourceSansBold
HopButton.Draggable = true

HopButton.MouseButton1Click:Connect(function()
    HopButton.Text = "Saindo..."
    local TeleportService = game:GetService("TeleportService")
    local HttpService = game:GetService("HttpService")
    local success, result = pcall(function()
        return HttpService:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/" .. game.PlaceId .. "/servers/Public?sortOrder=Desc&limit=100"))
    end)
    if success and result.data then
        for _, s in pairs(result.data) do
            if s.id ~= game.JobId and s.playing < s.maxPlayers then
                TeleportService:TeleportToPlaceInstance(game.PlaceId, s.id, me)
                break
            end
        end
    else
        TeleportService:Teleport(game.PlaceId, me)
    end
end)

-- 4. DECAL SPAM LEVE (COBRIR MAPA)
task.spawn(function()
    for i, v in pairs(workspace:GetDescendants()) do
        if v:IsA("BasePart") then
            local d = Instance.new("Decal", v)
            d.Texture = ID_IMG
            if i % 100 == 0 then task.wait() end
        end
    end
end)
