-- Configurações iniciais
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")

local AutoFarmEnabled = true
local AutoQuestEnabled = true
local AutoChestEnabled = true

-- Função para mover o personagem até o alvo
local function TeleportTo(position)
    player.Character.HumanoidRootPart.CFrame = CFrame.new(position.X, position.Y, position.Z)
end

-- Função basica de atacar inimigos próximos
local function AutoFarm()
    if not AutoFarmEnabled then return end
    for _, enemy in pairs(workspace.Enemies:GetChildren()) do
        if enemy.Humanoid and enemy.Humanoid.Health > 0 then
            TeleportTo(enemy.HumanoidRootPart.Position)
            -- Executar ataque - comando depende da forma do seu executor e game event (exemplo genérico)
            ReplicatedStorage.Remotes.Damage:FireServer(enemy, 50) -- Exemplo de ataque causando 50 de dano
            wait(0.5) -- Espera meio segundo
        end
    end
end

-- Função para completar quests automaticamente
local function AutoQuest()
    if not AutoQuestEnabled then return end
    local questNPC = workspace.NPCs.QuestGiver
    if questNPC then
        TeleportTo(questNPC.HumanoidRootPart.Position)
        -- Interagir para aceitar ou entregar quest, exemplo genérico
        ReplicatedStorage.Remotes.Quest:FireServer("Accept") 
        wait(1)
        ReplicatedStorage.Remotes.Quest:FireServer("Complete")
    end
end

-- Função para coletar baús automaticamente
local function AutoChest()
    if not AutoChestEnabled then return end
    for _, chest in pairs(workspace.Chests:GetChildren()) do
        if (chest.Position - player.Character.HumanoidRootPart.Position).Magnitude < 50 then
            TeleportTo(chest.Position)
            -- Interagir com o baú (exemplo)
            ReplicatedStorage.Remotes.Chest:FireServer(chest)
            wait(0.5)
        end
    end
end

-- Loop principal
RunService.Heartbeat:Connect(function()
    if AutoFarmEnabled then AutoFarm() end
    if AutoQuestEnabled then AutoQuest() end
    if AutoChestEnabled then AutoChest() end
end
