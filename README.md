# Soran-hublocal Rayfield = loadstring(game:HttpGet("https://raw.githubusercontent.com/shlexware/Rayfield/main/source"))()

local Window = Rayfield:CreateWindow({
	Name = "Soran Hub - Grow A Garden",
	LoadingTitle = "Soran Hub",
	LoadingSubtitle = "Delta Edition",
	ConfigurationSaving = { Enabled = false },
	KeySystem = false,
})

local mt = getrawmetatable(game)
setreadonly(mt, false)
local oldNamecall = mt.__namecall
mt.__namecall = newcclosure(function(self, ...)
	local args = {...}
	local method = getnamecallmethod()
	if tostring(self):lower():find("kick") or tostring(self):lower():find("ban") then
		return nil
	end
	if method == "Kick" and self == game.Players.LocalPlayer then
		return
	end
	return oldNamecall(self, unpack(args))
end)

task.spawn(function()
	local vu = game:GetService("VirtualUser")
	game:GetService("Players").LocalPlayer.Idled:Connect(function()
		vu:Button2Down(Vector2.new(), workspace.CurrentCamera.CFrame)
		wait(1)
		vu:Button2Up(Vector2.new(), workspace.CurrentCamera.CFrame)
	end)
end)

local autoFarm = false
Window:CreateToggle({
	Name = "Auto Farm",
	CurrentValue = false,
	Callback = function(v)
		autoFarm = v
		while autoFarm do
			for _, plot in pairs(workspace.Plots:GetChildren()) do
				if plot:FindFirstChild("Soil") and plot.Soil:FindFirstChild("ClickDetector") then
					pcall(function()
						fireclickdetector(plot.Soil.ClickDetector)
					end)
				end
			end
			wait(1)
		end
	end
})

local autoSell = false
Window:CreateToggle({
	Name = "Auto Sell",
	CurrentValue = false,
	Callback = function(v)
		autoSell = v
		while autoSell do
			local sell = workspace:FindFirstChild("Sell")
			if sell then
				pcall(function()
					firetouchinterest(game.Players.LocalPlayer.Character.HumanoidRootPart, sell, 0)
					wait(0.3)
					firetouchinterest(game.Players.LocalPlayer.Character.HumanoidRootPart, sell, 1)
				end)
			end
			wait(3)
		end
	end
})

Window:CreateButton({
	Name = "Spawn Mimic Pet",
	Callback = function()
		pcall(function()
			game.ReplicatedStorage.Remotes.SpawnPet:FireServer("Mimic")
		end)
	end
})

Window:CreateButton({
	Name = "Spawn Golden Seed",
	Callback = function()
		pcall(function()
			game.ReplicatedStorage.Remotes.SpawnSeed:FireServer("GoldenSeed")
		end)
	end
})

Window:CreateButton({
	Name = "Server Hop",
	Callback = function()
		game:GetService("TeleportService"):Teleport(game.PlaceId)
	end
})

Window:CreateButton({
	Name = "Boost FPS + Enhance Visuals",
	Callback = function()
		pcall(function()
			local lighting = game:GetService("Lighting")
			lighting.Brightness = 2
			lighting.GlobalShadows = true
			lighting.FogEnd = 100000
			lighting.Ambient = Color3.fromRGB(140, 140, 140)
			lighting.ColorShift_Top = Color3.fromRGB(255, 255, 255)
			for _, obj in pairs(workspace:GetDescendants()) do
				if obj:IsA("Texture") or obj:IsA("Decal") then
					obj.Transparency = 0.1
				elseif obj:IsA("ParticleEmitter") or obj:IsA("Trail") then
					obj.Enabled = false
				end
			end
			settings().Rendering.QualityLevel = Enum.QualityLevel.Level04
		end)
	end
})
