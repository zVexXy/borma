-- MENU VERMELHO COM BOTÕES PARA ATIVAR ESP

local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "RedMenuESP"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 300, 0, 180)
frame.Position = UDim2.new(0.5, -150, 0.5, -90)
frame.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
frame.BorderSizePixel = 4
frame.BorderColor3 = Color3.fromRGB(255, 0, 0)
frame.Active = true
frame.Draggable = true

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 40)
title.Text = "ESP & Movimentação"
title.BackgroundTransparency = 1
title.TextColor3 = Color3.fromRGB(255,255,255)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 24

local espBtn = Instance.new("TextButton", frame)
espBtn.Size = UDim2.new(0.9, 0, 0, 40)
espBtn.Position = UDim2.new(0.05, 0, 0, 50)
espBtn.Text = "Ativar ESP"
espBtn.BackgroundColor3 = Color3.fromRGB(150,0,0)
espBtn.TextColor3 = Color3.fromRGB(255,255,255)
espBtn.Font = Enum.Font.SourceSansBold
espBtn.TextSize = 20

local areaBtn = Instance.new("TextButton", frame)
areaBtn.Size = UDim2.new(0.9, 0, 0, 40)
areaBtn.Position = UDim2.new(0.05, 0, 0, 100)
areaBtn.Text = "Monitorar Área"
areaBtn.BackgroundColor3 = Color3.fromRGB(150,0,0)
areaBtn.TextColor3 = Color3.fromRGB(255,255,255)
areaBtn.Font = Enum.Font.SourceSansBold
areaBtn.TextSize = 20

local closeBtn = Instance.new("TextButton", frame)
closeBtn.Size = UDim2.new(0.9, 0, 0, 30)
closeBtn.Position = UDim2.new(0.05, 0, 0, 150)
closeBtn.Text = "Fechar Menu"
closeBtn.BackgroundColor3 = Color3.fromRGB(200,0,0)
closeBtn.TextColor3 = Color3.fromRGB(255,255,255)
closeBtn.Font = Enum.Font.SourceSansBold
closeBtn.TextSize = 18

closeBtn.MouseButton1Click:Connect(function()
    gui:Destroy()
end)

-- Função ESP
local function ativarESP()
    for _, p in ipairs(game.Players:GetPlayers()) do
        if p ~= player and p.Character and p.Character:FindFirstChild("Head") and not p.Character.Head:FindFirstChild("ESP") then
            local esp = Instance.new("BillboardGui", p.Character.Head)
            esp.Name = "ESP"
            esp.Size = UDim2.new(0, 120, 0, 30)
            esp.Adornee = p.Character.Head
            esp.AlwaysOnTop = true

            local nameLabel = Instance.new("TextLabel", esp)
            nameLabel.Size = UDim2.new(1, 0, 1, 0)
            nameLabel.BackgroundTransparency = 1
            nameLabel.Text = p.Name
            nameLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
            nameLabel.Font = Enum.Font.SourceSansBold
            nameLabel.TextSize = 18
            nameLabel.TextStrokeTransparency = 0
        end
    end
end

espBtn.MouseButton1Click:Connect(ativarESP)

-- Função de Monitorar Área (requer uma Part chamada "AreaCheck" no workspace)
local monitorando = false
areaBtn.MouseButton1Click:Connect(function()
    if monitorando then return end
    monitorando = true

    local area = workspace:FindFirstChild("AreaCheck")
    if not area then
        game.StarterGui:SetCore("ChatMakeSystemMessage", {
            Text = "Área 'AreaCheck' não encontrada!";
            Color = Color3.fromRGB(255,0,0);
        })
        return
    end

    local lastPositions = {}
    game:GetService("RunService").Heartbeat:Connect(function()
        for _, p in ipairs(game.Players:GetPlayers()) do
            if p ~= player and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                local pos = p.Character.HumanoidRootPart.Position
                local areaPos = area.Position
                local areaSize = area.Size
                local dentro = (math.abs(pos.X - areaPos.X) < areaSize.X/2) and
                            (math.abs(pos.Y - areaPos.Y) < areaSize.Y/2) and
                            (math.abs(pos.Z - areaPos.Z) < areaSize.Z/2)
                if dentro then
                    if lastPositions[p.Name] and (lastPositions[p.Name] - pos).magnitude > 0.5 then
                        game.StarterGui:SetCore("ChatMakeSystemMessage", {
                            Text = p.Name.." se moveu na área!";
                            Color = Color3.fromRGB(0,255,0);
                        })
                    end
                    lastPositions[p.Name] = pos
                end
            end
        end
    end)
end)

-- FIM DO SCRIPT
