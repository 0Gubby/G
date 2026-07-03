local Library = {}

function Library:CreateWindow(HubTitle, HubSubtitle)
    local Players = game:GetService("Players")
    local UIS = game:GetService("UserInputService")
    local CoreGui = game:GetService("CoreGui")
    local TweenService = game:GetService("TweenService")

    local Player = Players.LocalPlayer
    local Minimized = false

    local ParentFolder
    if gethui then
        ParentFolder = gethui()
    elseif cloneref then
        local success, core = pcall(cloneref, CoreGui)
        ParentFolder = success and core or Player:WaitForChild("PlayerGui")
    else
        local success, core = pcall(function() return CoreGui:ClassName() == "CoreGui" and CoreGui end)
        ParentFolder = success and core or Player:WaitForChild("PlayerGui")
    end

    pcall(function()
        if ParentFolder:FindFirstChild("UniversalGui") then
            ParentFolder.UniversalGui:Destroy()
        end
    end)

    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "UniversalGui"
    ScreenGui.ResetOnSpawn = false
    ScreenGui.Parent = ParentFolder

    -- =========================================================================
    -- SLIM MAIN WINDOW (OPTIMIZED FOR MOBILE)
    -- =========================================================================
    local Main = Instance.new("Frame")
    Main.Name = "Main"
    Main.Size = UDim2.new(0.85, 0, 0, 85) -- Shrunk initial base height from 120 to 85
    Main.Position = UDim2.new(0.5, 0, 0.5, 0)
    Main.AnchorPoint = Vector2.new(0.5, 0.5)
    Main.BackgroundColor3 = Color3.fromRGB(10, 8, 16)
    Main.BorderSizePixel = 0
    Main.Parent = ScreenGui

    local MainSizeConstraint = Instance.new("UISizeConstraint")
    MainSizeConstraint.MaxSize = Vector2.new(340, 2000) -- Capped max width at 340 (down from 460)
    MainSizeConstraint.Parent = Main

    local MainCorner = Instance.new("UICorner")
    MainCorner.CornerRadius = UDim.new(0, 10)
    MainCorner.Parent = Main

    local MainBgGradient = Instance.new("UIGradient")
    MainBgGradient.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(22, 16, 35)),
        ColorSequenceKeypoint.new(0.5, Color3.fromRGB(12, 10, 18)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(6, 5, 10))
    })
    MainBgGradient.Rotation = 45
    MainBgGradient.Parent = Main

    local MainStroke = Instance.new("UIStroke")
    MainStroke.Thickness = 1
    MainStroke.Color = Color3.fromRGB(55, 45, 75)
    MainStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    MainStroke.Parent = Main

    -- Header Drag Area
    local HeaderHitbox = Instance.new("TextButton")
    HeaderHitbox.Name = "HeaderHitbox"
    HeaderHitbox.Size = UDim2.new(1, 0, 0, 45) -- Lowered header profile
    HeaderHitbox.BackgroundTransparency = 1
    HeaderHitbox.Text = ""
    HeaderHitbox.Parent = Main

    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1, -95, 0, 18)
    Title.Position = UDim2.fromOffset(14, 10)
    Title.BackgroundTransparency = 1
    Title.Text = HubTitle or "UNIVERSAL GUI"
    Title.Font = Enum.Font.GothamBold
    Title.TextSize = 11 -- Shrunk text size
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = Main

    local Subtitle = Instance.new("TextLabel")
    Subtitle.Size = UDim2.new(1, -95, 0, 12)
    Subtitle.Position = UDim2.fromOffset(14, 26)
    Subtitle.BackgroundTransparency = 1
    Subtitle.Text = HubSubtitle or "Made by Unknown"
    Subtitle.Font = Enum.Font.GothamMedium
    Subtitle.TextSize = 8.5
    Subtitle.TextColor3 = Color3.fromRGB(120, 110, 145)
    Subtitle.TextXAlignment = Enum.TextXAlignment.Left
    Subtitle.Parent = Main

    -- Top Bar Window Controls Container
    local Controls = Instance.new("Frame")
    Controls.Name = "Controls"
    Controls.Size = UDim2.fromOffset(50, 22)
    Controls.Position = UDim2.new(1, -64, 0, 11)
    Controls.BackgroundTransparency = 1
    Controls.Parent = Main

    local ControlsLayout = Instance.new("UIListLayout")
    ControlsLayout.FillDirection = Enum.FillDirection.Horizontal
    ControlsLayout.HorizontalAlignment = Enum.HorizontalAlignment.Right
    ControlsLayout.SortOrder = Enum.SortOrder.LayoutOrder
    ControlsLayout.Padding = UDim.new(0, 6)
    ControlsLayout.Parent = Controls

    -- Minimize Button
    local Minimize = Instance.new("TextButton")
    Minimize.Name = "Minimize"
    Minimize.Size = UDim2.fromOffset(22, 22)
    Minimize.BackgroundColor3 = Color3.fromRGB(25, 20, 35)
    Minimize.Text = "•"
    Minimize.TextColor3 = Color3.fromRGB(200, 190, 220)
    Minimize.Font = Enum.Font.GothamBold
    Minimize.TextSize = 14
    Minimize.BorderSizePixel = 0
    Minimize.LayoutOrder = 1
    Minimize.Parent = Controls

    local MinCorner = Instance.new("UICorner")
    MinCorner.CornerRadius = UDim.new(0, 6)
    MinCorner.Parent = Minimize

    -- Close Button
    local Close = Instance.new("TextButton")
    Close.Name = "Close"
    Close.Size = UDim2.fromOffset(22, 22)
    Close.BackgroundColor3 = Color3.fromRGB(45, 20, 30)
    Close.Text = "✕"
    Close.TextColor3 = Color3.fromRGB(255, 100, 120)
    Close.Font = Enum.Font.GothamBold
    Close.TextSize = 10
    Close.BorderSizePixel = 0
    Close.LayoutOrder = 2
    Close.Parent = Controls

    local CloseCorner = Instance.new("UICorner")
    CloseCorner.CornerRadius = UDim.new(0, 6)
    CloseCorner.Parent = Close

    local Content = Instance.new("Frame")
    Content.Size = UDim2.new(1, -24, 1, -85)
    Content.Position = UDim2.fromOffset(12, 45)
    Content.BackgroundTransparency = 1
    Content.ClipsDescendants = true
    Content.Parent = Main

    -- =========================================================================
    -- COMPACT PROFILE FOOTER WITH ANONYMOUS TOGGLE
    -- =========================================================================
    local ProfileFrame = Instance.new("Frame")
    ProfileFrame.Name = "ProfileFrame"
    ProfileFrame.Size = UDim2.new(1, -24, 0, 32) -- Trimmed profile frame height
    ProfileFrame.Position = UDim2.new(0, 12, 1, -40)
    ProfileFrame.BackgroundColor3 = Color3.fromRGB(14, 11, 22)
    ProfileFrame.BorderSizePixel = 0
    ProfileFrame.Parent = Main

    local ProfileCorner = Instance.new("UICorner")
    ProfileCorner.CornerRadius = UDim.new(0, 5)
    ProfileCorner.Parent = ProfileFrame

    local ProfileStroke = Instance.new("UIStroke")
    ProfileStroke.Thickness = 1
    ProfileStroke.Color = Color3.fromRGB(35, 28, 48)
    ProfileStroke.Parent = ProfileFrame

    local AvatarImage = Instance.new("ImageLabel")
    AvatarImage.Name = "AvatarImage"
    AvatarImage.Size = UDim2.fromOffset(20, 20)
    AvatarImage.Position = UDim2.new(0, 6, 0.5, -10)
    AvatarImage.BackgroundColor3 = Color3.fromRGB(24, 18, 36)
    AvatarImage.BorderSizePixel = 0
    AvatarImage.Parent = ProfileFrame

    local AvatarCorner = Instance.new("UICorner")
    AvatarCorner.CornerRadius = UDim.new(1, 0)
    AvatarCorner.Parent = AvatarImage

    task.spawn(function()
        local success, content = pcall(function()
            return Players:GetUserThumbnailAsync(Player.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size420x420)
        end)
        if success then AvatarImage.Image = content end
    end)

    local DisplayNameLabel = Instance.new("TextButton")
    DisplayNameLabel.Size = UDim2.new(1, -50, 0, 12)
    DisplayNameLabel.Position = UDim2.fromOffset(32, 3)
    DisplayNameLabel.BackgroundTransparency = 1
    DisplayNameLabel.Text = Player.DisplayName
    DisplayNameLabel.Font = Enum.Font.GothamBold
    DisplayNameLabel.TextSize = 9.5
    DisplayNameLabel.TextColor3 = Color3.fromRGB(230, 225, 245)
    DisplayNameLabel.TextXAlignment = Enum.TextXAlignment.Left
    DisplayNameLabel.Parent = ProfileFrame

    local UsernameLabel = Instance.new("TextButton")
    UsernameLabel.Size = UDim2.new(1, -50, 0, 10)
    UsernameLabel.Position = UDim2.fromOffset(32, 15)
    UsernameLabel.BackgroundTransparency = 1
    UsernameLabel.Text = "@" .. Player.Name
    UsernameLabel.Font = Enum.Font.GothamMedium
    UsernameLabel.TextSize = 8
    UsernameLabel.TextColor3 = Color3.fromRGB(115, 105, 135)
    UsernameLabel.TextXAlignment = Enum.TextXAlignment.Left
    UsernameLabel.Parent = ProfileFrame

    local IdentityHidden = false
    local function ToggleIdentityProtection()
        IdentityHidden = not IdentityHidden
        if IdentityHidden then
            DisplayNameLabel.Text = "********"
            UsernameLabel.Text = "@********"
        else
            DisplayNameLabel.Text = Player.DisplayName
            UsernameLabel.Text = "@" .. Player.Name
        end
    end

    DisplayNameLabel.MouseButton1Click:Connect(ToggleIdentityProtection)
    UsernameLabel.MouseButton1Click:Connect(ToggleIdentityProtection)

    -- Close Engine functionality
    Close.MouseButton1Click:Connect(function()
        ScreenGui:Destroy()
    end)

    -- =========================================================================
    -- DRAGGING MECHANIC (MOUSE + TOUCH RESPONSIVE)
    -- =========================================================================
    local dragging, dragInput, dragStart, startPos

    local function update(input)
        local delta = input.Position - dragStart
        Main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end

    HeaderHitbox.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = Main.Position

            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)

    HeaderHitbox.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)

    UIS.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            update(input)
        end
    end)
        -- =========================================================================
    -- WINDOW METRICS & MINIMIZE SYSTEM
    -- =========================================================================
    local Window = {
        ElementCount = 0,
        MaxHeight = 85
    }

    local function UpdateSize()
        Window.MaxHeight = 85 + (Window.ElementCount * 36) -- Spaced beautifully for mobile touch targets
        if not Minimized then
            TweenService:Create(Main, TweenInfo.new(0.18, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Size = UDim2.new(Main.Size.X.Scale, 0, 0, Window.MaxHeight + 45)}):Play()
        end
    end

    TableInsert = table.insert -- Micro-optimization

    Minimize.MouseButton1Click:Connect(function()
        Minimized = not Minimized
        if Minimized then
            TweenService:Create(Main, TweenInfo.new(0.2, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Size = UDim2.new(Main.Size.X.Scale, 0, 0, 45)}):Play()
            Content.Visible = false
            ProfileFrame.Visible = false
            Minimize.Text = "◦"
        else
            Content.Visible = true
            ProfileFrame.Visible = true
            TweenService:Create(Main, TweenInfo.new(0.2, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Size = UDim2.new(Main.Size.X.Scale, 0, 0, Window.MaxHeight + 45)}):Play()
            Minimize.Text = "•"
        end
    end)

    function Window:CreateToggle(Name, Callback)
        local YPos = Window.ElementCount * 36
        Window.ElementCount = Window.ElementCount + 1
        UpdateSize()

        local ButtonFrame = Instance.new("TextButton")
        ButtonFrame.Name = "ToggleButton"
        ButtonFrame.Size = UDim2.new(1, 0, 0, 30) -- Compact height
        ButtonFrame.Position = UDim2.fromOffset(0, YPos)
        ButtonFrame.BackgroundColor3 = Color3.fromRGB(16, 14, 24)
        ButtonFrame.BorderSizePixel = 0
        ButtonFrame.Text = ""
        ButtonFrame.Parent = Content

        local btnCorner = Instance.new("UICorner")
        btnCorner.CornerRadius = UDim.new(0, 5)
        btnCorner.Parent = ButtonFrame

        local BaseGradient = Instance.new("UIGradient")  
        BaseGradient.Color = ColorSequence.new({  
            ColorSequenceKeypoint.new(0, Color3.fromRGB(28, 20, 42)),  
            ColorSequenceKeypoint.new(1, Color3.fromRGB(14, 10, 20))  
        })  
        BaseGradient.Rotation = 90  
        BaseGradient.Parent = ButtonFrame  

        local Stroke = Instance.new("UIStroke")  
        Stroke.Color = Color3.fromRGB(48, 38, 68)
        Stroke.Thickness = 1
        Stroke.Parent = ButtonFrame  

        local Label = Instance.new("TextLabel")  
        Label.Size = UDim2.new(1, -55, 1, 0)  
        Label.Position = UDim2.fromOffset(10, 0)  
        Label.BackgroundTransparency = 1  
        Label.Text = Name
        Label.Font = Enum.Font.GothamBold  
        Label.TextSize = 10.5
        Label.TextColor3 = Color3.fromRGB(225, 220, 240) 
        Label.TextXAlignment = Enum.TextXAlignment.Left
        Label.Parent = ButtonFrame  

        local SwitchTrack = Instance.new("Frame")
        SwitchTrack.Name = "SwitchTrack"
        SwitchTrack.Size = UDim2.fromOffset(32, 16)
        SwitchTrack.Position = UDim2.new(1, -42, 0.5, -8)
        SwitchTrack.BackgroundColor3 = Color3.fromRGB(32, 25, 45)
        SwitchTrack.BorderSizePixel = 0
        SwitchTrack.Parent = ButtonFrame

        local trackCorner = Instance.new("UICorner")
        trackCorner.CornerRadius = UDim.new(1, 0)
        trackCorner.Parent = SwitchTrack

        local SwitchKnob = Instance.new("Frame")
        SwitchKnob.Name = "SwitchKnob"
        SwitchKnob.Size = UDim2.fromOffset(12, 12)
        SwitchKnob.Position = UDim2.fromOffset(2, 2)
        SwitchKnob.BackgroundColor3 = Color3.fromRGB(160, 150, 175)
        SwitchKnob.BorderSizePixel = 0
        SwitchKnob.Parent = SwitchTrack

        local knobCorner = Instance.new("UICorner")
        knobCorner.CornerRadius = UDim.new(1, 0)
        knobCorner.Parent = SwitchKnob

        local ToggledState = false
        
        ButtonFrame.MouseButton1Click:Connect(function()  
            ToggledState = not ToggledState
            
            if ToggledState then
                TweenService:Create(SwitchTrack, TweenInfo.new(0.12, Enum.EasingStyle.Quad), {BackgroundColor3 = Color3.fromRGB(120, 40, 240)}):Play()
                TweenService:Create(SwitchKnob, TweenInfo.new(0.15, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {Position = UDim2.fromOffset(18, 2), BackgroundColor3 = Color3.fromRGB(255, 255, 255)}):Play()
            else
                TweenService:Create(SwitchTrack, TweenInfo.new(0.12, Enum.EasingStyle.Quad), {BackgroundColor3 = Color3.fromRGB(32, 25, 45)}):Play()
                TweenService:Create(SwitchKnob, TweenInfo.new(0.15, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {Position = UDim2.fromOffset(2, 2), BackgroundColor3 = Color3.fromRGB(160, 150, 175)}):Play()
            end

            if Callback then
                pcall(Callback, ToggledState)
            end
        end)  
    end

    function Window:CreateButton(Name, Callback)
        local YPos = Window.ElementCount * 36
        Window.ElementCount = Window.ElementCount + 1
        UpdateSize()

        local ButtonFrame = Instance.new("TextButton")
        ButtonFrame.Name = "SimpleButton"
        ButtonFrame.Size = UDim2.new(1, 0, 0, 30)
        ButtonFrame.Position = UDim2.fromOffset(0, YPos)
        ButtonFrame.BackgroundColor3 = Color3.fromRGB(20, 16, 30)
        ButtonFrame.BorderSizePixel = 0
        ButtonFrame.ClipsDescendants = true
        ButtonFrame.Text = ""
        ButtonFrame.Parent = Content

        local bCorner = Instance.new("UICorner")
        bCorner.CornerRadius = UDim.new(0, 5)
        bCorner.Parent = ButtonFrame

        local BaseGradient = Instance.new("UIGradient")  
        BaseGradient.Color = ColorSequence.new({  
            ColorSequenceKeypoint.new(0, Color3.fromRGB(38, 26, 56)),  
            ColorSequenceKeypoint.new(1, Color3.fromRGB(18, 12, 28))  
        })  
        BaseGradient.Rotation = 90  
        BaseGradient.Parent = ButtonFrame  

        local Stroke = Instance.new("UIStroke")  
        Stroke.Color = Color3.fromRGB(60, 45, 85)  
        Stroke.Thickness = 1  
        Stroke.Parent = ButtonFrame  

        local Label = Instance.new("TextLabel")  
        Label.Size = UDim2.new(1, 0, 1, 0)  
        Label.BackgroundTransparency = 1  
        Label.Text = Name  
        Label.Font = Enum.Font.GothamBlack  
        Label.TextSize = 11  
        Label.TextColor3 = Color3.fromRGB(255, 255, 255) 
        Label.Parent = ButtonFrame
        
        ButtonFrame.MouseButton1Click:Connect(function()
            TweenService:Create(ButtonFrame, TweenInfo.new(0.05, Enum.EasingStyle.Quad), {ImageTransparency = 0.3}):Play()
            task.wait(0.05)
            TweenService:Create(ButtonFrame, TweenInfo.new(0.05, Enum.EasingStyle.Quad), {ImageTransparency = 0}):Play()
            
            if Callback then
                pcall(Callback)
            end
        end)
    end

    return Window
end

return Library
