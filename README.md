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
        -- Tạo Attachment và AlignPosition để nhân vật ngồi đúng vị trí và di chuyển theo
        local alignPos = Instance.new("AlignPosition")
        local attach0 = Instance.new("Attachment")
        local attach1 = Instance.new("Attachment")
        
        attach0.Parent = character.HumanoidRootPart
        attach1.Parent = targetCharacter.Head
        
        alignPos.Attachment0 = attach0
        alignPos.Attachment1 = attach1
        alignPos.RigidityEnabled = true
        alignPos.MaxForce = 25000
        alignPos.Responsiveness = 50
        alignPos.Parent = character.HumanoidRootPart
        
        -- Đặt nhân vật lên trên đầu và chuyển sang trạng thái ngồi
        character.HumanoidRootPart.CFrame = targetCharacter.Head.CFrame * CFrame.new(0, 2, 0)
        local humanoid = character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.Sit = true -- Chuyển trạng thái nhân vật thành ngồi
        end
    else
        -- Xóa tất cả AlignPosition và Attachment
        for _, obj in pairs(character.HumanoidRootPart:GetChildren()) do
            if obj:IsA("AlignPosition") or obj:IsA("Attachment") then
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
