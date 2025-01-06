local player = game.Players.LocalPlayer
local screenGui = Instance.new("ScreenGui")
local frame = Instance.new("Frame")
local textBox = Instance.new("TextBox")
local toggleButton = Instance.new("TextButton")
local headsitEnabled = false

screenGui.Parent = player:WaitForChild("PlayerGui")
frame.Parent = screenGui
frame.Size = UDim2.new(0, 200, 0, 150)
frame.Position = UDim2.new(0.5, -100, 0.5, -75)
frame.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
frame.BorderSizePixel = 0
frame.BackgroundTransparency = 0.3

textBox.Parent = frame
textBox.Size = UDim2.new(0, 180, 0, 30)
textBox.Position = UDim2.new(0, 10, 0, 10)
textBox.PlaceholderText = "Nhập tên người chơi"
textBox.Text = ""
textBox.ClearTextOnFocus = false
textBox.TextColor3 = Color3.new(1, 1, 1)
textBox.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
textBox.BorderSizePixel = 0

toggleButton.Parent = frame
toggleButton.Size = UDim2.new(0, 180, 0, 30)
toggleButton.Position = UDim2.new(0, 10, 0, 50)
toggleButton.Text = "Bật Headsit"
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.BackgroundColor3 = Color3.fromRGB(60, 120, 200)
toggleButton.BorderSizePixel = 0

toggleButton.MouseButton1Click:Connect(function()
    local targetName = textBox.Text
    local targetPlayer = game.Players:FindFirstChild(targetName)
    
    if not targetPlayer then
        toggleButton.Text = "Người chơi không tồn tại"
        task.wait(1)
        toggleButton.Text = headsitEnabled and "Tắt Headsit" or "Bật Headsit"
        return
    end

    local character = player.Character
    local targetCharacter = targetPlayer.Character

    if not character or not targetCharacter then
        toggleButton.Text = "Không tìm thấy nhân vật"
        task.wait(1)
        toggleButton.Text = headsitEnabled and "Tắt Headsit" or "Bật Headsit"
        return
    end

    headsitEnabled = not headsitEnabled
    toggleButton.Text = headsitEnabled and "Tắt Headsit" or "Bật Headsit"

    if headsitEnabled then
        local weld = Instance.new("WeldConstraint")
        weld.Part0 = character:WaitForChild("HumanoidRootPart")
        weld.Part1 = targetCharacter:WaitForChild("Head")
        weld.Parent = weld.Part0
        character.HumanoidRootPart.CFrame = targetCharacter.Head.CFrame * CFrame.new(0, 1.5, 0)
    else
        for _, weld in pairs(character.HumanoidRootPart:GetChildren()) do
            if weld:IsA("WeldConstraint") then
                weld:Destroy()
            end
        end
    end
end)
