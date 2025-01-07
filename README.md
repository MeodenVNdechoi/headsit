local player = game.Players.LocalPlayer
local replicatedStorage = game:GetService("ReplicatedStorage")
local screenGui = Instance.new("ScreenGui")
local frame = Instance.new("Frame")
local textBox = Instance.new("TextBox")
local toggleButton = Instance.new("TextButton")
local headsitEnabled = false

-- Tạo GUI
local function createGUI()
    screenGui.Name = "CustomGUI"
    screenGui.ResetOnSpawn = false -- Ngăn GUI bị reset khi nhân vật chết
    screenGui.Parent = player:WaitForChild("PlayerGui")

    frame.Parent = screenGui
    frame.Size = UDim2.new(0, 200, 0, 150)
    frame.Position = UDim2.new(0.5, -100, 0.5, -75)
    frame.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    frame.BorderSizePixel = 0
    frame.BackgroundTransparency = 0.3
    frame.Active = true
    frame.Draggable = true -- Cho phép di chuyển GUI

    textBox.Parent = frame
    textBox.Size = UDim2.new(0, 180, 0, 30)
    textBox.Position = UDim2.new(0, 10, 0, 10)
    textBox.PlaceholderText = "Nhập tên người bạn yêu"
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
            toggleButton.Text = "bạn đang yêu nhân vật ảo à"
            task.wait(1)
            toggleButton.Text = headsitEnabled and "Chia tay" or "Bật bùa yêu"
            return
        end

        local character = player.Character
        local targetCharacter = targetPlayer.Character

        if not character or not targetCharacter then
            toggleButton.Text = "bạn đang yêu nhân vật ảo à"
            task.wait(1)
            toggleButton.Text = headsitEnabled and "Chia tay" or "Bật bùa yêu"
            return
        end

        headsitEnabled = not headsitEnabled
        toggleButton.Text = headsitEnabled and "Chia tay" or "Bật bùa yêu"

        if headsitEnabled then
            -- Tạo các đối tượng để cố định nhân vật
            local alignPos = Instance.new("AlignPosition")
            local alignOrient = Instance.new("AlignOrientation")
            local attach0 = Instance.new("Attachment")
            local attach1 = Instance.new("Attachment")
            
            attach0.Parent = character.HumanoidRootPart
            attach1.Parent = targetCharacter.Head

            alignPos.Attachment0 = attach0
            alignPos.Attachment1 = attach1
            alignPos.RigidityEnabled = true
            alignPos.MaxForce = math.huge
            alignPos.Responsiveness = 100
            alignPos.Parent = character.HumanoidRootPart

            alignOrient.Attachment0 = attach0
            alignOrient.Attachment1 = attach1
            alignOrient.RigidityEnabled = true
            alignOrient.MaxTorque = math.huge
            alignOrient.Responsiveness = 100
            alignOrient.Parent = character.HumanoidRootPart

            -- Đặt nhân vật lên đầu mục tiêu
            character.HumanoidRootPart.CFrame = targetCharacter.Head.CFrame * CFrame.new(0, 2, 0)
            local humanoid = character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                humanoid.Sit = true -- Chuyển trạng thái nhân vật thành ngồi
            end
        else
            -- Xóa tất cả AlignPosition, AlignOrientation và Attachment
            for _, obj in pairs(character.HumanoidRootPart:GetChildren()) do
                if obj:IsA("AlignPosition") or obj:IsA("AlignOrientation") or obj:IsA("Attachment") then
                    obj:Destroy()
                end
            end

            -- Đảm bảo nhân vật trở lại trạng thái đứng
            local humanoid = character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                humanoid.Sit = false -- Trở lại trạng thái đứng
            end
        end
    end)
end

-- Tạo GUI lần đầu
createGUI()

-- Đảm bảo GUI không biến mất sau khi chết
player.CharacterAdded:Connect(function()
    if not player.PlayerGui:FindFirstChild("CustomGUI") then
        createGUI()
    end
    -- Đặt lại trạng thái khi nhân vật xuất hiện
    headsitEnabled = false
    toggleButton.Text = "Bật bùa yêu"
end)
