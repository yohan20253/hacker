--// Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera

--// Referências
local LocalPlayer = Players.LocalPlayer
local ParteParaMirar = "Head" -- Ou "HumanoidRootPart"
local ChecarTime = false -- true = não mira em aliados
local AimbotAtivo = false

--// Função: encontrar inimigo mais próximo
local function JogadorMaisProximo()
	local menorDistancia = math.huge
	local alvoMaisProximo = nil

	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild(ParteParaMirar) then
			if not ChecarTime or player.Team ~= LocalPlayer.Team then
				local parte = player.Character[ParteParaMirar]
				local distancia = (parte.Position - Camera.CFrame.Position).Magnitude
				if distancia < menorDistancia then
					menorDistancia = distancia
					alvoMaisProximo = parte
				end
			end
		end
	end

	return alvoMaisProximo
end

--// Aimbot com RenderStepped
RunService.RenderStepped:Connect(function()
	if AimbotAtivo then
		local alvo = JogadorMaisProximo()
		if alvo then
			local direcao = (alvo.Position - Camera.CFrame.Position).Unit
			Camera.CFrame = CFrame.new(Camera.CFrame.Position, Camera.CFrame.Position + direcao)
		end
	end
end)

--// Ativação com tecla E
UserInputService.InputBegan:Connect(function(input, processado)
	if processado then return end
	if input.KeyCode == Enum.KeyCode.E then
		AimbotAtivo = true
	end
end)

UserInputService.InputEnded:Connect(function(input, processado)
	if input.KeyCode == Enum.KeyCode.E then
		AimbotAtivo = false
	end
end)
