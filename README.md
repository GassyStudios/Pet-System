local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local PetFolder = workspace:WaitForChild("PlayersPets")

local Spacing = 7
local PetSize = 3
local MaxClimbHeight = 15

local RayParams = RaycastParams.new()
local RayDirection = Vector3.new(0, -500, 0)

local function RearrangeTables(Pets, Rows, MaxRowCapacity)
	table.clear(Rows)
	local AmountOfRows = math.ceil(#Pets / MaxRowCapacity)
	for i = 1, AmountOfRows do
		table.insert(Rows, {})
	end
	for i, v in Pets do
		local Row = Rows[math.ceil(i / MaxRowCapacity)]
		table.insert(Row, v)
	end
end

local function GetRowWidth(Row, Pet)
	if Pet ~= nil then
		local SpacingBetweenPets = Spacing - (Pet.PrimaryPart.Size.X or 0)
		local RowWidth = 0

		if #Row == 1 then
			return 0
		end

		for i, v in Row do
			if i ~= #Row then
				RowWidth += Pet.PrimaryPart.Size.X + SpacingBetweenPets
			else
				RowWidth += Pet.PrimaryPart.Size.X
			end
		end

		return RowWidth
	end
end

RunService.Heartbeat:Connect(function(Deltatime)
	for _, PlayerPetFolder in ipairs(PetFolder:GetChildren()) do
		local Player = Players:FindFirstChild(PlayerPetFolder.Name)
		if Player then
			local Character = Player.Character or Player.CharacterAdded:Wait()
			local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")
			local Humanoid = Character:WaitForChild("Humanoid")

			local Pets = {}
			local Rows = {}

			for _, Pet in ipairs(PlayerPetFolder:GetChildren()) do
				if Pet.Name == "Pet" then
					table.insert(Pets, Pet)
     end
     end
		RayParams.FilterDescendantsInstances ={PetFolder, Character}
		local MaxRowCapacity = 6
		RearrangeTables(Pets, Rows, MaxRowCapacity)
		for i, Pet in Pets do
			local RowIndex = math.ceil(i / MaxRowCapacity)
			local Row = Rows[RowIndex]
			local RowWidth = GetRowWidth(Row, Pet)

			local XOffset = #Row == 1 and 0 or RowWidth/2 - Pet.PrimaryPart.Size.X/2
			local X = (table.find(Row, Pet) - 1) * Spacing
			local Z = RowIndex * Spacing
			local Y = 0

			local RayResult = workspace:Blockcast(Pet.PrimaryPart.CFrame + Vector3.new(0, MaxClimbHeight, 0), Pet.PrimaryPart.Size, RayDirection, RayParams)

			if RayResult then
				Y = RayResult.Position.Y + Pet.PrimaryPart.Size.Y/2
			end

			local TargetCFrame = CFrame.new(HumanoidRootPart.CFrame.X, 0, HumanoidRootPart.CFrame.Z) * HumanoidRootPart.CFrame.Rotation * CFrame.new(X - XOffset, Y, Z)

			Pet.PrimaryPart.CFrame = Pet.PrimaryPart.CFrame:Lerp(TargetCFrame, 0.1)
			end
		end
		end
end)# Pet-System
