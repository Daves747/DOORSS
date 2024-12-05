local player = game.Players.LocalPlayer
local mouse = player:GetMouse()

-- Criação do GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player:WaitForChild("PlayerGui")

local menuFrame = Instance.new("Frame")
menuFrame.Size = UDim2.new(0, 300, 0, 400)
menuFrame.Position = UDim2.new(0.5, -150, 0.5, -200)
menuFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
menuFrame.BackgroundTransparency = 0.5
menuFrame.Visible = false  -- Inicialmente invisível
menuFrame.Parent = screenGui

-- Botão para fechar o menu
local closeButton = Instance.new("TextButton")
closeButton.Text = "Fechar Menu"
closeButton.Size = UDim2.new(0, 200, 0, 50)
closeButton.Position = UDim2.new(0.5, -100, 0.9, -50)
closeButton.Parent = menuFrame

closeButton.MouseButton1Click:Connect(function()
    menuFrame.Visible = false
end)

-- Função para abrir o menu
function ToggleMenu()
    menuFrame.Visible = not menuFrame.Visible
end

-- Comando para abrir o menu com a tecla "M"
mouse.KeyDown:Connect(function(key)
    if key == "m" then
        ToggleMenu()
    end
end)
local function CreateESP(part)
    local box = Instance.new("BillboardGui")
    box.Size = UDim2.new(0, 200, 0, 50)
    box.Adornee = part
    box.Parent = screenGui

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, 0, 1, 0)
    label.Text = part.Name
    label.BackgroundTransparency = 1
    label.TextColor3 = Color3.fromRGB(255, 0, 0)
    label.Parent = box
end

-- Detectando entidades e objetos no mapa
game:GetService("Workspace").DescendantAdded:Connect(function(descendant)
    if descendant:IsA("Model") and descendant:FindFirstChild("Humanoid") then
        CreateESP(descendant)
    elseif descendant:IsA("Part") then
        CreateESP(descendant)
    end
end)
local function NotifyEntity(entityName)
    local notification = Instance.new("TextLabel")
    notification.Size = UDim2.new(0, 300, 0, 50)
    notification.Position = UDim2.new(0.5, -150, 0, 0)
    notification.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    notification.BackgroundTransparency = 0.5
    notification.Text = "Entidade detectada: " .. entityName
    notification.TextColor3 = Color3.fromRGB(255, 0, 0)
    notification.TextSize = 24
    notification.Parent = screenGui

    wait(3)  -- Exibe por 3 segundos
    notification:Destroy()
end

-- Detecção de entidades próximas
game:GetService("RunService").Heartbeat:Connect(function()
    local closestEntity = nil
    local closestDistance = math.huge

    for _, entity in pairs(game.Workspace:GetChildren()) do
        if entity:IsA("Model") and entity:FindFirstChild("Humanoid") then
            local distance = (entity.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).magnitude
            if distance < 50 then  -- Se estiver dentro de 50 metros
                if distance < closestDistance then
                    closestEntity = entity
                    closestDistance = distance
                end
            end
        end
    end

    if closestEntity then
        NotifyEntity(closestEntity.Name)
    end
end)
local function CollectItems()
    for _, item in pairs(workspace:GetChildren()) do
        if item:IsA("Part") and (item.Position - player.Character.HumanoidRootPart.Position).magnitude < 100 then
            -- Coletar o item, isso pode ser feito movendo o item para o inventário ou pegando o objeto
            print("Coletando item:", item.Name)
            item:Destroy()  -- Destruir o item como exemplo
        end
    end
end

-- Adicionar botão no menu para ativar a coleta
local collectButton = Instance.new("TextButton")
collectButton.Text = "Coletar Itens"
collectButton.Size = UDim2.new(0, 200, 0, 50)
collectButton.Position = UDim2.new(0.5, -100, 0.6, 0)
collectButton.Parent = menuFrame

collectButton.MouseButton1Click:Connect(function()
    CollectItems()
end)
