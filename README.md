--[[ 
    Custom Menu Script for Executor (KRNL, Fluxus, etc.)
    Author: YOUR_NAME_HERE 
]]

local player = game.Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")

-- Xoá UI cũ nếu có
pcall(function() game.CoreGui.CustomMenu:Destroy() end)

-- Tạo GUI mới
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "CustomMenu"
screenGui.Parent = game.CoreGui
screenGui.ResetOnSpawn = false

-- Frame chính
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 400, 0, 320)
mainFrame.Position = UDim2.new(0.5, -200, 0.5, -160)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Parent = screenGui

-- Tiêu đề
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 50)
title.BackgroundTransparency = 1
title.Text = "Custom Menu (by TVC)"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 24
title.Parent = mainFrame

-- Button 1
local button1 = Instance.new("TextButton")
button1.Size = UDim2.new(0.8, 0, 0, 50)
button1.Position = UDim2.new(0.1, 0, 0, 70)
button1.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
button1.Text = "Spawn Pet"
button1.TextColor3 = Color3.fromRGB(255, 255, 255)
button1.Font = Enum.Font.SourceSans
button1.TextSize = 20
button1.Parent = mainFrame

button1.MouseButton1Click:Connect(function()
	print("Spawn Pet clicked")
	-- Thêm logic spawn pet tại đây
end)

-- Button 2
local button2 = Instance.new("TextButton")
button2.Size = UDim2.new(0.8, 0, 0, 50)
button2.Position = UDim2.new(0.1, 0, 0, 140)
button2.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
button2.Text = "Spawn Seed"
button2.TextColor3 = Color3.fromRGB(255, 255, 255)
button2.Font = Enum.Font.SourceSans
button2.TextSize = 20
button2.Parent = mainFrame

button2.MouseButton1Click:Connect(function()
	print("Spawn Seed clicked")
	-- Thêm logic spawn seed tại đây
end)

-- TextBox nhập tên seed/item
local inputBox = Instance.new("TextBox")
inputBox.PlaceholderText = "Nhập tên seed/item"
inputBox.Size = UDim2.new(0.8, 0, 0, 40)
inputBox.Position = UDim2.new(0.1, 0, 0, 210)
inputBox.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
inputBox.TextColor3 = Color3.fromRGB(255, 255, 255)
inputBox.Font = Enum.Font.SourceSans
inputBox.TextSize = 18
inputBox.ClearTextOnFocus = false
inputBox.Parent = mainFrame

-- Nút lấy seed từ workspace
local getButton = Instance.new("TextButton")
getButton.Size = UDim2.new(0.8, 0, 0, 40)
getButton.Position = UDim2.new(0.1, 0, 0, 260)
getButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
getButton.Text = "Lấy Seed từ Workspace"
getButton.TextColor3 = Color3.fromRGB(255, 255, 255)
getButton.Font = Enum.Font.SourceSans
getButton.TextSize = 18
getButton.Parent = mainFrame

getButton.MouseButton1Click:Connect(function()
	local name = inputBox.Text
	if name == "" then 
		warn("Vui lòng nhập tên seed/item")
		return 
	end

	local item = workspace:FindFirstChild(name)
	if item and (item:IsA("Model") or item:IsA("BasePart")) then
		local clone = item:Clone()
		
		local tool = Instance.new("Tool")
		tool.Name = name
		tool.RequiresHandle = false
		tool.CanBeDropped = true

		if clone:IsA("Model") then
			-- Gán PrimaryPart nếu chưa có
			if not clone.PrimaryPart then
				clone.PrimaryPart = clone:FindFirstChildWhichIsA("BasePart")
			end
			-- Đặt vị trí về gốc để tránh trục trặc khi cầm
			if clone.PrimaryPart then
				clone:SetPrimaryPartCFrame(CFrame.new(0,0,0))
			end
			clone.Parent = tool
		else
			clone.Name = "Handle"
			clone.Parent = tool
			tool.RequiresHandle = true
		end

		tool.Parent = player.Backpack
		print("Đã đưa item '"..name.."' vào Backpack.")
	else
		warn("Không tìm thấy item '"..name.."' trong workspace hoặc không đúng loại.")
	end
end)

-- Toggle bằng phím RightShift
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end
	if input.KeyCode == Enum.KeyCode.RightShift then
		screenGui.Enabled = not screenGui.Enabled
	end
end)

-- Kéo bảng
local dragging, dragInput, dragStart, startPos

mainFrame.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true
		dragStart = input.Position
		startPos = mainFrame.Position
		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

mainFrame.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement then
		dragInput = input
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if input == dragInput and dragging then
		local delta = input.Position - dragStart
		mainFrame.Position = UDim2.new(
			startPos.X.Scale,
			startPos.X.Offset + delta.X,
			startPos.Y.Scale,
			startPos.Y.Offset + delta.Y
		)
	end
end)
