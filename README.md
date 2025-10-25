--[[
🌱 PLANTINHA HUB
YouTube: pikachuzim_blox
]]

local player = game.Players.LocalPlayer
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")

-- GUI Principal
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0, 600, 0, 300)
Frame.Position = UDim2.new(0.5, -300, 0.5, -150)
Frame.BackgroundColor3 = Color3.fromRGB(0,0,0)
Frame.BackgroundTransparency = 0.25
Frame.Active = true
Frame.Draggable = true

local Title = Instance.new("TextLabel", Frame)
Title.Text = "🌱 Plantinha Hub"
Title.Size = UDim2.new(1,0,0,40)
Title.BackgroundTransparency = 1
Title.TextColor3 = Color3.fromRGB(0,255,0)
Title.Font = Enum.Font.FredokaOne
Title.TextSize = 26

-- Container de funções
local Funcoes = Instance.new("Frame", Frame)
Funcoes.Size = UDim2.new(1,0,1,-70)
Funcoes.Position = UDim2.new(0,0,0,60)
Funcoes.BackgroundTransparency = 1

local function addButton(nome, callback, posY)
	local btn = Instance.new("TextButton", Funcoes)
	btn.Size = UDim2.new(0,200,0,40)
	btn.Position = UDim2.new(0.5,-100,0,posY)
	btn.Text = nome
	btn.BackgroundColor3 = Color3.fromRGB(0,255,100)
	btn.TextColor3 = Color3.fromRGB(0,0,0)
	btn.Font = Enum.Font.FredokaOne
	btn.TextSize = 18
	btn.MouseButton1Click:Connect(callback)
	return btn
end

local function addSlider(nome, min, max, default, posY, callback)
	local label = Instance.new("TextLabel", Funcoes)
	label.Size = UDim2.new(0,200,0,20)
	label.Position = UDim2.new(0.5,-100,0,posY)
	label.Text = nome..": "..default
	label.TextColor3 = Color3.fromRGB(255,255,255)
	label.BackgroundTransparency = 1
	label.Font = Enum.Font.FredokaOne
	label.TextSize = 16
	
	local slider = Instance.new("TextBox", Funcoes)
	slider.Size = UDim2.new(0,200,0,20)
	slider.Position = UDim2.new(0.5,-100,0,posY+22)
	slider.Text = tostring(default)
	slider.TextColor3 = Color3.fromRGB(0,0,0)
	slider.BackgroundColor3 = Color3.fromRGB(200,200,200)
	slider.ClearTextOnFocus = false
	slider.FocusLost:Connect(function()
		local value = tonumber(slider.Text)
		if value then
			callback(value)
			label.Text = nome..": "..value
		end
	end)
end

-- FloatV2 toggle com sliders
local floatActive = false
local bodyPos, conn
local floatHeight = 3
local floatSpeed = 0.3
local floatD = 25
local floatP = 1000

addButton("FloatV2", function()
	local char = player.Character or player.CharacterAdded:Wait()
	local hrp = char:WaitForChild("HumanoidRootPart")
	
	if not floatActive then
		bodyPos = Instance.new("BodyPosition")
		bodyPos.MaxForce = Vector3.new(1e5,1e5,1e5)
		bodyPos.D = floatD
		bodyPos.P = floatP
		bodyPos.Position = hrp.Position
		bodyPos.Parent = hrp
		
		conn = RunService.Heartbeat:Connect(function()
			local moveDir = Vector3.new(0,0,0)
			if UIS:IsKeyDown(Enum.KeyCode.W) then moveDir = moveDir + workspace.CurrentCamera.CFrame.LookVector end
			if UIS:IsKeyDown(Enum.KeyCode.S) then moveDir = moveDir - workspace.CurrentCamera.CFrame.LookVector end
			if UIS:IsKeyDown(Enum.KeyCode.A) then moveDir = moveDir - workspace.CurrentCamera.CFrame.RightVector end
			if UIS:IsKeyDown(Enum.KeyCode.D) then moveDir = moveDir + workspace.CurrentCamera.CFrame.RightVector end
			
			local targetPos = hrp.Position + moveDir * floatSpeed
			bodyPos.Position = targetPos + Vector3.new(0,floatHeight,0)
		end)
		floatActive = true
	else
		if bodyPos then bodyPos:Destroy() end
		if conn then conn:Disconnect() end
		floatActive = false
	end
end, 0)

-- Sliders
addSlider("Altura", 1, 10, floatHeight, 50, function(v) floatHeight = v end)
addSlider("Velocidade", 0.1, 3, floatSpeed, 90, function(v) floatSpeed = v end)
addSlider("Suavidade(D)", 1, 50, floatD, 130, function(v) 
	floatD = v
	if bodyPos then bodyPos.D = floatD end
end)
addSlider("Força(P)", 100, 5000, floatP, 170, function(v)
	floatP = v
	if bodyPos then bodyPos.P = floatP end
end)

-- Créditos / Info
local creditos = Instance.new("TextLabel", Frame)
creditos.Text = "YouTube pikachuzim_blox"
creditos.Size = UDim2.new(1,0,0,30)
creditos.Position = UDim2.new(0,0,1,-30)
creditos.BackgroundTransparency = 1
creditos.TextColor3 = Color3.fromRGB(0,255,0)
creditos.Font = Enum.Font.FredokaOne
creditos.TextSize = 16

-- Botão minimizar para bolinha
local MiniButton = Instance.new("TextButton", ScreenGui)
MiniButton.Size = UDim2.new(0,40,0,40)
MiniButton.Position = UDim2.new(0.5,-20,0.5,-20)
MiniButton.Text = "🌱"
MiniButton.Visible = false
MiniButton.BackgroundColor3 = Color3.fromRGB(0,255,0)
MiniButton.TextColor3 = Color3.fromRGB(0,0,0)
MiniButton.Font = Enum.Font.FredokaOne
MiniButton.TextSize = 24
MiniButton.Active = true
MiniButton.Draggable = true

-- Minimizar
local Minimize = Instance.new("TextButton", Frame)
Minimize.Size = UDim2.new(0,30,0,30)
Minimize.Position = UDim2.new(1,-35,0,5)
Minimize.Text = "–"
Minimize.Font = Enum.Font.FredokaOne
Minimize.TextSize = 24
Minimize.TextColor3 = Color3.fromRGB(255,255,255)
Minimize.BackgroundColor3 = Color3.fromRGB(255,0,0)

Minimize.MouseButton1Click:Connect(function()
	Frame.Visible = false
	MiniButton.Visible = true
end)

MiniButton.MouseButton1Click:Connect(function()
	Frame.Visible = true
	MiniButton.Visible = false
end)
