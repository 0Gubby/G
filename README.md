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
    -- MAIN WINDOW (MOBILE RESPONSIVE & DRAGGABLE)
    -- =========================================================================
    local Main = Instance.new("Frame")
    Main.Name = "Main"
    Main.Size = UDim2.new(0.9, 0, 0, 120) 
    Main.Position = UDim2.new(0.5, 0, 0.5, 0)
    Main.AnchorPoint = Vector2.new(0.5, 0.5)
    Main.BackgroundColor3 = Color3.fromRGB(10, 8, 16)
    Main.BorderSizePixel = 0
    Main.Parent = ScreenGui

    local MainSizeConstraint = Instance.new("UISizeConstraint")
    MainSizeConstraint.MaxSize = Vector2.new(460, 2000)
    MainSizeConstraint.Parent = Main

    local MainCorner = Instance.new("UICorner")
    MainCorner.CornerRadius = UDim.new(0, 12)
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
    MainStroke.Thickness = 1.2
    MainStroke.Color = Color3.fromRGB(255, 255, 255)
    MainStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    MainStroke.Parent = Main

    local MainStrokeGradient = Instance.new("UIGradient")
    MainStrokeGradient.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(200, 160, 255)),
        ColorSequenceKeypoint.new(0.4, Color3.fromRGB(80, 65, 110)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(25, 20, 35))
    })
    MainStrokeGradient.Rotation = 45
    MainStrokeGradient.Parent = MainStroke

    local AmbientGlow = Instance.new("Frame")
    AmbientGlow.Name = "AmbientGlow"
    AmbientGlow.Size = UDim2.new(1, 10, 1, 10)
    AmbientGlow.Position = UDim2.fromOffset(-5, -5)
    AmbientGlow.BackgroundColor3 = Color3.fromRGB(115, 60, 255)
    AmbientGlow.BackgroundTransparency = 0.9
    AmbientGlow.ZIndex = Main.ZIndex - 1
    AmbientGlow.Parent = Main

    local GlowCorner = Instance.new("UICorner")
    GlowCorner.CornerRadius = UDim.new(0, 14)
    GlowCorner.Parent = AmbientGlow

    local HeaderHitbox = Instance.new("TextButton")
    HeaderHitbox.Name = "HeaderHitbox"
    HeaderHitbox.Size = UDim2.new(1, 0, 0, 60)
    HeaderHitbox.BackgroundTransparency = 1
    HeaderHitbox.Text = ""
    HeaderHitbox.Parent = Main

    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1, -80, 0, 22)
    Title.Position = UDim2.fromOffset(18, 16)
    Title.BackgroundTransparency = 1
    Title.Text = HubTitle or "UNIVERSAL GUI"
    Title.Font = Enum.Font.GothamBlack
    Title.TextSize = 13
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = Main

    local Subtitle = Instance.new("TextLabel")
    Subtitle.Size = UDim2.new(1, -80, 0, 14)
    Subtitle.Position = UDim2.fromOffset(18, 35)
    Subtitle.BackgroundTransparency = 1
    Subtitle.Text = HubSubtitle or "Made by Unknown"
    Subtitle.Font = Enum.Font.GothamMedium
    Subtitle.TextSize = 10
    Subtitle.TextColor3 = Color3.fromRGB(120, 110, 145)
    Subtitle.TextXAlignment = Enum.TextXAlignment.Left
    Subtitle.Parent = Main

    local Minimize = Instance.new("TextButton")
    Minimize.Size = UDim2.fromOffset(26, 26)
    Minimize.Position = UDim2.new(1, -44, 0, 14)
    Minimize.BackgroundColor3 = Color3.fromRGB(25, 20, 35)
    Minimize.Text = "•"
    Minimize.TextColor3 = Color3.fromRGB(255, 255, 255)
    Minimize.Font = Enum.Font.GothamBold
    Minimize.TextSize = 16
    Minimize.BorderSizePixel = 0
    Minimize.ZIndex = 5
    Minimize.Parent = Main

    local MinCorner = Instance.new("UICorner")
    MinCorner.CornerRadius = UDim.new(0, 8)
    MinCorner.Parent = Minimize

    local MinimizeStroke = Instance.new("UIStroke")
    MinimizeStroke.Color = Color3.fromRGB(255, 255, 255)
    MinimizeStroke.Thickness = 1
    MinimizeStroke.Parent = Minimize

    local MinStrokeGradient = Instance.new("UIGradient")
    MinStrokeGradient.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(200, 160, 255)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(50, 40, 70))
    })
    MinStrokeGradient.Rotation = 45
    MinStrokeGradient.Parent = MinimizeStroke

    local Content = Instance.new("Frame")
    Content.Size = UDim2.new(1, -36, 1, -120)
    Content.Position = UDim2.fromOffset(18, 62)
    Content.BackgroundTransparency = 1
    Content.ClipsDescendants = true
    Content.Parent = Main

    -- =========================================================================
    -- MINIATURIZED PROFILE CORNER FOOTER WITH ANONYMOUS TOGGLE
    -- =========================================================================
    local ProfileFrame = Instance.new("Frame")
    ProfileFrame.Name = "ProfileFrame"
    ProfileFrame.Size = UDim2.new(1, -36, 0, 38)
    ProfileFrame.Position = UDim2.new(0, 18, 1, -50)
    ProfileFrame.BackgroundColor3 = Color3.fromRGB(14, 11, 22)
    ProfileFrame.BorderSizePixel = 0
    ProfileFrame.Parent = Main

    local ProfileCorner = Instance.new("UICorner")
    ProfileCorner.CornerRadius = UDim.new(0, 6)
    ProfileCorner.Parent = ProfileFrame

    local ProfileStroke = Instance.new("UIStroke")
    ProfileStroke.Thickness = 1
    ProfileStroke.Color = Color3.fromRGB(40, 32, 55)
    ProfileStroke.Parent = ProfileFrame

    local AvatarImage = Instance.new("ImageLabel")
    AvatarImage.Name = "AvatarImage"
    AvatarImage.Size = UDim2.fromOffset(24, 24)
    AvatarImage.Position = UDim2.new(0, 8, 0.5, -12)
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
    DisplayNameLabel.Size = UDim2.new(1, -60, 0, 14)
    DisplayNameLabel.Position = UDim2.fromOffset(40, 4)
    DisplayNameLabel.BackgroundTransparency = 1
    DisplayNameLabel.Text = Player.DisplayName
    DisplayNameLabel.Font = Enum.Font.GothamBold
    DisplayNameLabel.TextSize = 11
    DisplayNameLabel.TextColor3 = Color3.fromRGB(230, 225, 245)
    DisplayNameLabel.TextXAlignment = Enum.TextXAlignment.Left
    DisplayNameLabel.Parent = ProfileFrame

    local UsernameLabel = Instance.new("TextButton")
    UsernameLabel.Size = UDim2.new(1, -60, 0, 12)
    UsernameLabel.Position = UDim2.fromOffset(40, 18)
    UsernameLabel.BackgroundTransparency = 1
    UsernameLabel.Text = "@" .. Player.Name
    UsernameLabel.Font = Enum.Font.GothamMedium
    UsernameLabel.TextSize = 9
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
        MaxHeight = 120
    }

    local function UpdateSize()
        Window.MaxHeight = 120 + (Window.ElementCount * 54)
        if not Minimized then
            TweenService:Create(Main, TweenInfo.new(0.2, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Size = UDim2.new(Main.Size.X.Scale, 0, 0, Window.MaxHeight)}):Play()
        end
    end

    TableInsert = table.insert -- Micro-optimization

    Minimize.MouseButton1Click:Connect(function()
        Minimized = not Minimized
        if Minimized then
            -- Compress window down to show only header frame
            TweenService:Create(Main, TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Size = UDim2.new(Main.Size.X.Scale, 0, 0, 60)}):Play()
            Content.Visible = false
            ProfileFrame.Visible = false
            Minimize.Text = "◦"
        else
            -- Restore true programmatic height smoothly
            Content.Visible = true
            ProfileFrame.Visible = true
            TweenService:Create(Main, TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Size = UDim2.new(Main.Size.X.Scale, 0, 0, Window.MaxHeight)}):Play()
            Minimize.Text = "•"
        end
    end)

    function Window:CreateToggle(Name, Callback)
        local YPos = Window.ElementCount * 54
        Window.ElementCount = Window.ElementCount + 1
        UpdateSize()

        local ButtonFrame = Instance.new("TextButton")
        ButtonFrame.Name = "ToggleButton"
        ButtonFrame.Size = UDim2.new(1, 0, 0, 46)
        ButtonFrame.Position = UDim2.fromOffset(0, YPos)
        ButtonFrame.BackgroundColor3 = Color3.fromRGB(16, 14, 24)
        ButtonFrame.BorderSizePixel = 0
        ButtonFrame.Text = ""
        ButtonFrame.Parent = Content

        local btnCorner = Instance.new("UICorner")
        btnCorner.CornerRadius = UDim.new(0, 8)
        btnCorner.Parent = ButtonFrame

        local BaseGradient = Instance.new("UIGradient")  
        BaseGradient.Color = ColorSequence.new({  
            ColorSequenceKeypoint.new(0, Color3.fromRGB(32, 22, 48)),  
            ColorSequenceKeypoint.new(1, Color3.fromRGB(16, 12, 24))  
        })  
        BaseGradient.Rotation = 90  
        BaseGradient.Parent = ButtonFrame  

        local Stroke = Instance.new("UIStroke")  
        Stroke.Color = Color3.fromRGB(255, 255, 255)
        Stroke.Thickness = 1.2  
        Stroke.Parent = ButtonFrame  

        local StrokeGradient = Instance.new("UIGradient")  
        StrokeGradient.Color = ColorSequence.new({  
            ColorSequenceKeypoint.new(0, Color3.fromRGB(140, 100, 200)),  
            ColorSequenceKeypoint.new(1, Color3.fromRGB(45, 30, 65))  
        })  
        StrokeGradient.Rotation = 90  
        StrokeGradient.Parent = Stroke  

        local Label = Instance.new("TextLabel")  
        Label.Size = UDim2.new(1, -70, 1, 0)  
        Label.Position = UDim2.fromOffset(14, 0)  
        Label.BackgroundTransparency = 1  
        Label.Text = Name
        Label.Font = Enum.Font.GothamBold  
        Label.TextSize = 13  
        Label.TextColor3 = Color3.fromRGB(230, 225, 245) 
        Label.TextXAlignment = Enum.TextXAlignment.Left
        Label.Parent = ButtonFrame  

        local SwitchTrack = Instance.new("Frame")
        SwitchTrack.Name = "SwitchTrack"
        SwitchTrack.Size = UDim2.fromOffset(42, 22)
        SwitchTrack.Position = UDim2.new(1, -56, 0.5, -11)
        SwitchTrack.BackgroundColor3 = Color3.fromRGB(40, 32, 55)
        SwitchTrack.BorderSizePixel = 0
        SwitchTrack.Parent = ButtonFrame

        local trackCorner = Instance.new("UICorner")
        trackCorner.CornerRadius = UDim.new(1, 0)
        trackCorner.Parent = SwitchTrack

        local TrackStroke = Instance.new("UIStroke")
        TrackStroke.Thickness = 1
        TrackStroke.Color = Color3.fromRGB(75, 60, 100)
        TrackStroke.Parent = SwitchTrack

        local SwitchKnob = Instance.new("Frame")
        SwitchKnob.Name = "SwitchKnob"
        SwitchKnob.Size = UDim2.fromOffset(16, 16)
        SwitchKnob.Position = UDim2.fromOffset(3, 3)
        SwitchKnob.BackgroundColor3 = Color3.fromRGB(180, 170, 195)
        SwitchKnob.BorderSizePixel = 0
        SwitchKnob.Parent = SwitchTrack

        local knobCorner = Instance.new("UICorner")
        knobCorner.CornerRadius = UDim.new(1, 0)
        knobCorner.Parent = SwitchKnob

        local ToggledState = false
        
        ButtonFrame.MouseButton1Click:Connect(function()  
            TweenService:Create(ButtonFrame, TweenInfo.new(0.05, Enum.EasingStyle.Quad), {Size = UDim2.new(1, -6, 0, 42), Position = UDim2.fromOffset(3, YPos + 2)}):Play()  
            task.wait(0.05)  
            TweenService:Create(ButtonFrame, TweenInfo.new(0.05, Enum.EasingStyle.Quad), {Size = UDim2.new(1, 0, 0, 46), Position = UDim2.fromOffset(0, YPos)}):Play()  

            ToggledState = not ToggledState
            
            if ToggledState then
                TweenService:Create(SwitchTrack, TweenInfo.new(0.15, Enum.EasingStyle.Quad), {BackgroundColor3 = Color3.fromRGB(130, 50, 255)}):Play()
                TweenService:Create(TrackStroke, TweenInfo.new(0.15, Enum.EasingStyle.Quad), {Color = Color3.fromRGB(180, 120, 255)}):Play()
                TweenService:Create(SwitchKnob, TweenInfo.new(0.2, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {Position = UDim2.fromOffset(23, 3), BackgroundColor3 = Color3.fromRGB(255, 255, 255)}):Play()
            else
                TweenService:Create(SwitchTrack, TweenInfo.new(0.15, Enum.EasingStyle.Quad), {BackgroundColor3 = Color3.fromRGB(40, 32, 55)}):Play()
                TweenService:Create(TrackStroke, TweenInfo.new(0.15, Enum.EasingStyle.Quad), {Color = Color3.fromRGB(75, 60, 100)}):Play()
                TweenService:Create(SwitchKnob, TweenInfo.new(0.2, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {Position = UDim2.fromOffset(3, 3), BackgroundColor3 = Color3.fromRGB(180, 170, 195)}):Play()
            end

            if Callback then
                pcall(Callback, ToggledState)
            end
        end)  
    end

    function Window:CreateButton(Name, Callback)
        local YPos = Window.ElementCount * 54
        Window.ElementCount = Window.ElementCount + 1
        UpdateSize()

        local ButtonFrame = Instance.new("TextButton")
        ButtonFrame.Name = "SimpleButton"
        ButtonFrame.Size = UDim2.new(1, 0, 0, 46)
        ButtonFrame.Position = UDim2.fromOffset(0, YPos)
        ButtonFrame.BackgroundColor3 = Color3.fromRGB(22, 18, 34)
        ButtonFrame.BorderSizePixel = 0
        ButtonFrame.ClipsDescendants = true
        ButtonFrame.Text = ""
        ButtonFrame.Parent = Content

        local bCorner = Instance.new("UICorner")
        bCorner.CornerRadius = UDim.new(0, 8)
        bCorner.Parent = ButtonFrame

        local BaseGradient = Instance.new("UIGradient")  
        BaseGradient.Color = ColorSequence.new({  
            ColorSequenceKeypoint.new(0, Color3.fromRGB(44, 30, 66)),  
            ColorSequenceKeypoint.new(1, Color3.fromRGB(22, 14, 34))  
        })  
        BaseGradient.Rotation = 90  
        BaseGradient.Parent = ButtonFrame  

        local Stroke = Instance.new("UIStroke")  
        Stroke.Color = Color3.fromRGB(255, 255, 255)  
        Stroke.Thickness = 1.2  
        Stroke.Parent = ButtonFrame  

        local StrokeGradient = Instance.new("UIGradient")  
        StrokeGradient.Color = ColorSequence.new({  
            ColorSequenceKeypoint.new(0, Color3.fromRGB(190, 140, 255)),  
            ColorSequenceKeypoint.new(1, Color3.fromRGB(70, 45, 110))  
        })  
        StrokeGradient.Rotation = 90  
        StrokeGradient.Parent = Stroke  

        local SheenBar = Instance.new("Frame")  
        SheenBar.Name = "SheenBar"  
        SheenBar.Size = UDim2.new(2, 0, 1, 0)
        SheenBar.Position = UDim2.new(-2, 0, 0, 0)
        SheenBar.BackgroundTransparency = 1
        SheenBar.ZIndex = 2
        SheenBar.Parent = ButtonFrame  

        local ClickSheenGradient = Instance.new("UIGradient")  
        ClickSheenGradient.Color = ColorSequence.new({  
            ColorSequenceKeypoint.new(0, Color3.fromRGB(44, 30, 66)),
            ColorSequenceKeypoint.new(0.4, Color3.fromRGB(210, 170, 255)),
            ColorSequenceKeypoint.new(0.6, Color3.fromRGB(210, 170, 255)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(22, 14, 34))
        })  
        ClickSheenGradient.Transparency = NumberSequence.new({  
            NumberSequenceKeypoint.new(0, 1),  
            NumberSequenceKeypoint.new(0.4, 0.2),
            NumberSequenceKeypoint.new(0.6, 0.2),  
            NumberSequenceKeypoint.new(1, 1)  
        })  
        ClickSheenGradient.Rotation = 20  
        ClickSheenGradient.Parent = SheenBar  

        local Label = Instance.new("TextLabel")  
        Label.Size = UDim2.new(1, 0, 1, 0)  
        Label.BackgroundTransparency = 1  
        Label.Text = Name  
        Label.Font = Enum.Font.GothamBlack  
        Label.TextSize = 13  
        Label.TextColor3 = Color3.fromRGB(255, 255, 255) 
        Label.ZIndex = 3  
        Label.Parent = ButtonFrame
        
        ButtonFrame.MouseButton1Click:Connect(function()
            SheenBar.Position = UDim2.new(-2, 0, 0, 0)
            
            TweenService:Create(ButtonFrame, TweenInfo.new(0.05, Enum.EasingStyle.Quad), {Size = UDim2.new(1, -6, 0, 42), Position = UDim2.fromOffset(3, YPos + 2)}):Play()  
            TweenService:Create(SheenBar, TweenInfo.new(0.4, Enum.EasingStyle.Linear), {Position = UDim2.new(1, 0, 0, 0)}):Play()
            
            task.wait(0.05)  
            TweenService:Create(ButtonFrame, TweenInfo.new(0.05, Enum.EasingStyle.Quad), {Size = UDim2.new(1, 0, 0, 46), Position = UDim2.fromOffset(0, YPos)}):Play()  
            
            if Callback then
                pcall(Callback)
            end
        end)
    end

    return Window
end

return Library
