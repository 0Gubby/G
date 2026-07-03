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
    -- THEME COLORS (SHINY CYAN)
    -- =========================================================================
    local CyanShiny = Color3.fromRGB(0, 235, 255)
    local CyanDark = Color3.fromRGB(0, 90, 110)
    local BgDark = Color3.fromRGB(10, 14, 16)
    local ComponentBg = Color3.fromRGB(18, 24, 28)

    -- =========================================================================
    -- SIDE MINIMIZED FLOATING BUTTON
    -- =========================================================================
    local FloatingBtn = Instance.new("TextButton")
    FloatingBtn.Name = "FloatingMinimize"
    FloatingBtn.Size = UDim2.fromOffset(45, 45)
    FloatingBtn.Position = UDim2.new(0, 15, 0.5, -22) -- Left-side anchored middle
    FloatingBtn.BackgroundColor3 = BgDark
    FloatingBtn.Text = "⚡"
    FloatingBtn.TextColor3 = CyanShiny
    FloatingBtn.Font = Enum.Font.GothamBold
    FloatingBtn.TextSize = 20
    FloatingBtn.Visible = false
    FloatingBtn.Parent = ScreenGui

    local FloatCorner = Instance.new("UICorner")
    FloatCorner.CornerRadius = UDim.new(0, 12)
    FloatCorner.Parent = FloatingBtn

    local FloatStroke = Instance.new("UIStroke")
    FloatStroke.Thickness = 1.5
    FloatStroke.Color = CyanShiny
    FloatStroke.Parent = FloatingBtn

    -- =========================================================================
    -- MAIN WINDOW (COMPACT MOBILE DESIGN)
    -- =========================================================================
    local Main = Instance.new("Frame")
    Main.Name = "Main"
    Main.Size = UDim2.fromOffset(330, 130) -- Compact footprint ideal for mobile
    Main.Position = UDim2.new(0.5, -165, 0.5, -65)
    Main.BackgroundColor3 = BgDark
    Main.BorderSizePixel = 0
    Main.ClipsDescendants = true
    Main.Parent = ScreenGui

    local MainCorner = Instance.new("UICorner")
    MainCorner.CornerRadius = UDim.new(0, 12)
    MainCorner.Parent = Main

    local MainBgGradient = Instance.new("UIGradient")
    MainBgGradient.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(14, 24, 28)),
        ColorSequenceKeypoint.new(0.5, Color3.fromRGB(10, 14, 16)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(6, 8, 10))
    })
    MainBgGradient.Rotation = 45
    MainBgGradient.Parent = Main

    local MainStroke = Instance.new("UIStroke")
    MainStroke.Thickness = 1.4
    MainStroke.Color = Color3.fromRGB(255, 255, 255)
    MainStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    MainStroke.Parent = Main

    local MainStrokeGradient = Instance.new("UIGradient")
    MainStrokeGradient.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, CyanShiny),
        ColorSequenceKeypoint.new(0.5, CyanDark),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(10, 20, 25))
    })
    MainStrokeGradient.Rotation = 45
    MainStrokeGradient.Parent = MainStroke

    local AmbientGlow = Instance.new("Frame")
    AmbientGlow.Name = "AmbientGlow"
    AmbientGlow.Size = UDim2.new(1, 10, 1, 10)
    AmbientGlow.Position = UDim2.fromOffset(-5, -5)
    AmbientGlow.BackgroundColor3 = CyanShiny
    AmbientGlow.BackgroundTransparency = 0.92
    AmbientGlow.ZIndex = Main.ZIndex - 1
    AmbientGlow.Parent = Main

    local GlowCorner = Instance.new("UICorner")
    GlowCorner.CornerRadius = UDim.new(0, 14)
    GlowCorner.Parent = AmbientGlow

    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1, -80, 0, 22)
    Title.Position = UDim2.fromOffset(16, 14)
    Title.BackgroundTransparency = 1
    Title.Text = HubTitle or "CYAN HUB"
    Title.Font = Enum.Font.GothamBlack
    Title.TextSize = 13
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = Main

    local Subtitle = Instance.new("TextLabel")
    Subtitle.Size = UDim2.new(1, -80, 0, 14)
    Subtitle.Position = UDim2.fromOffset(16, 31)
    Subtitle.BackgroundTransparency = 1
    Subtitle.Text = HubSubtitle or "Mobile Edition"
    Subtitle.Font = Enum.Font.GothamMedium
    Subtitle.TextSize = 10
    Subtitle.TextColor3 = Color3.fromRGB(120, 140, 145)
    Subtitle.TextXAlignment = Enum.TextXAlignment.Left
    Subtitle.Parent = Main

    local Minimize = Instance.new("TextButton")
    Minimize.Size = UDim2.fromOffset(26, 26)
    Minimize.Position = UDim2.new(1, -40, 0, 14)
    Minimize.BackgroundColor3 = Color3.fromRGB(20, 30, 35)
    Minimize.Text = "−"
    Minimize.TextColor3 = CyanShiny
    Minimize.Font = Enum.Font.GothamBold
    Minimize.TextSize = 14
    Minimize.BorderSizePixel = 0
    Minimize.Parent = Main

    local MinCorner = Instance.new("UICorner")
    MinCorner.CornerRadius = UDim.new(0, 8)
    MinCorner.Parent = Minimize

    local MinimizeStroke = Instance.new("UIStroke")
    MinimizeStroke.Color = CyanDark
    MinimizeStroke.Thickness = 1
    MinimizeStroke.Parent = Minimize

    -- Scrolling Frame for Content Items (Keeps interface extremely compact)
    local ContentContainer = Instance.new("ScrollingFrame")
    ContentContainer.Name = "ContentContainer"
    ContentContainer.Size = UDim2.new(1, -32, 1, -115)
    ContentContainer.Position = UDim2.fromOffset(16, 54)
    ContentContainer.BackgroundTransparency = 1
    ContentContainer.ClipsDescendants = true
    ContentContainer.CanvasSize = UDim2.new(0, 0, 0, 0)
    ContentContainer.ScrollBarThickness = 3
    ContentContainer.ScrollBarImageColor3 = CyanShiny
    ContentContainer.Parent = Main

    local ContentLayout = Instance.new("UIListLayout")
    ContentLayout.Padding = UDim.new(0, 6)
    ContentLayout.SortOrder = Enum.SortOrder.LayoutOrder
    ContentLayout.Parent = ContentContainer

    -- Auto adjustment of lists
    ContentLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
        ContentContainer.CanvasSize = UDim2.new(0, 0, 0, ContentLayout.AbsoluteContentSize.Y + 5)
    end)

    -- =========================================================================
    -- PROFILE FOOTER WITH INCORPORATED REDACTION SYSTEM
    -- =========================================================================
    local ProfileFrame = Instance.new("TextButton")
    ProfileFrame.Name = "ProfileFrame"
    ProfileFrame.Size = UDim2.new(1, -32, 0, 38)
    ProfileFrame.Position = UDim2.new(0, 16, 1, -48)
    ProfileFrame.BackgroundColor3 = Color3.fromRGB(12, 18, 20)
    ProfileFrame.BorderSizePixel = 0
    ProfileFrame.Text = ""
    ProfileFrame.AutoButtonColor = false
    ProfileFrame.Parent = Main

    local ProfileCorner = Instance.new("UICorner")
    ProfileCorner.CornerRadius = UDim.new(0, 6)
    ProfileCorner.Parent = ProfileFrame

    local ProfileStroke = Instance.new("UIStroke")
    ProfileStroke.Thickness = 1
    ProfileStroke.Color = Color3.fromRGB(25, 40, 45)
    ProfileStroke.Parent = ProfileFrame

    local AvatarImage = Instance.new("ImageLabel")
    AvatarImage.Name = "AvatarImage"
    AvatarImage.Size = UDim2.fromOffset(24, 24)
    AvatarImage.Position = UDim2.new(0, 8, 0.5, -12)
    AvatarImage.BackgroundColor3 = Color3.fromRGB(18, 26, 30)
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

    local DisplayNameLabel = Instance.new("TextLabel")
    DisplayNameLabel.Size = UDim2.new(1, -60, 0, 14)
    DisplayNameLabel.Position = UDim2.fromOffset(40, 4)
    DisplayNameLabel.BackgroundTransparency = 1
    DisplayNameLabel.Text = Player.DisplayName
    DisplayNameLabel.Font = Enum.Font.GothamBold
    DisplayNameLabel.TextSize = 11
    DisplayNameLabel.TextColor3 = Color3.fromRGB(225, 245, 250)
    DisplayNameLabel.TextXAlignment = Enum.TextXAlignment.Left
    DisplayNameLabel.Parent = ProfileFrame

    local UsernameLabel = Instance.new("TextLabel")
    UsernameLabel.Size = UDim2.new(1, -60, 0, 12)
    UsernameLabel.Position = UDim2.fromOffset(40, 18)
    UsernameLabel.BackgroundTransparency = 1
    UsernameLabel.Text = "@" .. Player.Name
    UsernameLabel.Font = Enum.Font.GothamMedium
    UsernameLabel.TextSize = 9
    UsernameLabel.TextColor3 = Color3.fromRGB(105, 130, 135)
    UsernameLabel.TextXAlignment = Enum.TextXAlignment.Left
    UsernameLabel.Parent = ProfileFrame

    -- Hide/Unhide Censor Logic on Click
    local AccountsHidden = false
    ProfileFrame.MouseButton1Click:Connect(function()
        AccountsHidden = not AccountsHidden
        if AccountsHidden then
            DisplayNameLabel.Text = "********"
            UsernameLabel.Text = "@********"
            AvatarImage.ImageTransparency = 1
        else
            DisplayNameLabel.Text = Player.DisplayName
            UsernameLabel.Text = "@" .. Player.Name
            AvatarImage.ImageTransparency = 0
        end
    end)

    local Window = {
        ElementCount = 0,
        MaxHeight = 220 -- Static clean default height limits for Mobile layout comfort
    }

    local function UpdateSize()
        Main.Size = UDim2.fromOffset(330, Window.MaxHeight)
    end

    -- =========================================================================
    -- FEATURES MODULES
    -- =========================================================================
    
    -- 1. TOGGLE ELEMENT
    function Window:CreateToggle(Name, Callback)
        local ToggleFrame = Instance.new("TextButton")
        ToggleFrame.Name = "Toggle"
        ToggleFrame.Size = UDim2.new(1, 0, 0, 36)
        ToggleFrame.BackgroundColor3 = ComponentBg
        ToggleFrame.BorderSizePixel = 0
        ToggleFrame.Text = ""
        ToggleFrame.Parent = ContentContainer

        local btnCorner = Instance.new("UICorner")
        btnCorner.CornerRadius = UDim.new(0, 6)
        btnCorner.Parent = ToggleFrame

        local Stroke = Instance.new("UIStroke")  
        Stroke.Color = Color3.fromRGB(28, 38, 44)
        Stroke.Thickness = 1  
        Stroke.Parent = ToggleFrame  

        local Label = Instance.new("TextLabel")  
        Label.Size = UDim2.new(1, -70, 1, 0)  
        Label.Position = UDim2.fromOffset(10, 0)  
        Label.BackgroundTransparency = 1  
        Label.Text = Name
        Label.Font = Enum.Font.GothamBold  
        Label.TextSize = 12  
        Label.TextColor3 = Color3.fromRGB(225, 245, 250) 
        Label.TextXAlignment = Enum.TextXAlignment.Left
        Label.Parent = ToggleFrame  

        local SwitchTrack = Instance.new("Frame")
        SwitchTrack.Size = UDim2.fromOffset(36, 18)
        SwitchTrack.Position = UDim2.new(1, -46, 0.5, -9)
        SwitchTrack.BackgroundColor3 = Color3.fromRGB(12, 18, 20)
        SwitchTrack.Parent = ToggleFrame

        local trackCorner = Instance.new("UICorner")
        trackCorner.CornerRadius = UDim.new(1, 0)
        trackCorner.Parent = SwitchTrack

        local SwitchKnob = Instance.new("Frame")
        SwitchKnob.Size = UDim2.fromOffset(12, 12)
        SwitchKnob.Position = UDim2.fromOffset(3, 3)
        SwitchKnob.BackgroundColor3 = Color3.fromRGB(120, 140, 145)
        SwitchKnob.Parent = SwitchTrack

        local knobCorner = Instance.new("UICorner")
        knobCorner.CornerRadius = UDim.new(1, 0)
        knobCorner.Parent = SwitchKnob

        local ToggledState = false
        ToggleFrame.MouseButton1Click:Connect(function()  
            ToggledState = not ToggledState
            if ToggledState then
                TweenService:Create(SwitchTrack, TweenInfo.new(0.12), {BackgroundColor3 = CyanDark}):Play()
                TweenService:Create(SwitchKnob, TweenInfo.new(0.12), {Position = UDim2.fromOffset(21, 3), BackgroundColor3 = CyanShiny}):Play()
                Stroke.Color = CyanDark
            else
                TweenService:Create(SwitchTrack, TweenInfo.new(0.12), {BackgroundColor3 = Color3.fromRGB(12, 18, 20)}):Play()
                TweenService:Create(SwitchKnob, TweenInfo.new(0.12), {Position = UDim2.fromOffset(3, 3), BackgroundColor3 = Color3.fromRGB(120, 140, 145)}):Play()
                Stroke.Color = Color3.fromRGB(28, 38, 44)
            end
            if Callback then pcall(Callback, ToggledState) end
        end)  
    end

    -- 2. BUTTON ELEMENT
    function Window:CreateButton(Name, Callback)
        local ButtonFrame = Instance.new("TextButton")
        ButtonFrame.Name = "Button"
        ButtonFrame.Size = UDim2.new(1, 0, 0, 36)
        ButtonFrame.BackgroundColor3 = ComponentBg
        ButtonFrame.BorderSizePixel = 0
        ButtonFrame.Text = ""
        ButtonFrame.Parent = ContentContainer

        local bCorner = Instance.new("UICorner")
        bCorner.CornerRadius = UDim.new(0, 6)
        bCorner.Parent = ButtonFrame

        local Stroke = Instance.new("UIStroke")  
        Stroke.Color = Color3.fromRGB(28, 38, 44)
        Stroke.Thickness = 1  
        Stroke.Parent = ButtonFrame  

        local Label = Instance.new("TextLabel")  
        Label.Size = UDim2.new(1, 0, 1, 0)  
        Label.BackgroundTransparency = 1  
        Label.Text = Name  
        Label.Font = Enum.Font.GothamBlack  
        Label.TextSize = 12  
        Label.TextColor3 = Color3.fromRGB(255, 255, 255) 
        Label.Parent = ButtonFrame
        
        ButtonFrame.MouseButton1Click:Connect(function()
            Stroke.Color = CyanShiny
            Label.TextColor3 = CyanShiny
            task.wait(0.1)
            TweenService:Create(Stroke, TweenInfo.new(0.2), {Color = Color3.fromRGB(28, 38, 44)}):Play()
            TweenService:Create(Label, TweenInfo.new(0.2), {TextColor3 = Color3.fromRGB(255, 255, 255)}):Play()
            if Callback then pcall(Callback) end
        end)
    end

    -- 3. SLIDER ELEMENT
    function Window:CreateSlider(Name, Min, Max, Default, Callback)
        local SliderFrame = Instance.new("Frame")
        SliderFrame.Name = "Slider"
        SliderFrame.Size = UDim2.new(1, 0, 0, 42)
        SliderFrame.BackgroundColor3 = ComponentBg
        SliderFrame.Parent = ContentContainer

        local Corner = Instance.new("UICorner")
        Corner.CornerRadius = UDim.new(0, 6)
        Corner.Parent = SliderFrame

        local Label = Instance.new("TextLabel")
        Label.Size = UDim2.new(1, -60, 0, 20)
        Label.Position = UDim2.fromOffset(10, 2)
        Label.BackgroundTransparency = 1
        Label.Text = Name
        Label.Font = Enum.Font.GothamBold
        Label.TextSize = 11
        Label.TextColor3 = Color3.fromRGB(225, 245, 250)
        Label.TextXAlignment = Enum.TextXAlignment.Left
        Label.Parent = SliderFrame

        local ValueLabel = Instance.new("TextLabel")
        ValueLabel.Size = UDim2.new(0, 50, 0, 20)
        ValueLabel.Position = UDim2.new(1, -60, 0, 2)
        ValueLabel.BackgroundTransparency = 1
        ValueLabel.Text = tostring(Default)
        ValueLabel.Font = Enum.Font.GothamBlack
        ValueLabel.TextSize = 11
        ValueLabel.TextColor3 = CyanShiny
        ValueLabel.TextXAlignment = Enum.TextXAlignment.Right
        ValueLabel.Parent = SliderFrame

        local SliderBar = Instance.new("TextButton")
        SliderBar.Size = UDim2.new(1, -20, 0, 5)
        SliderBar.Position = UDim2.fromOffset(10, 26)
        SliderBar.BackgroundColor3 = Color3.fromRGB(12, 18, 20)
        SliderBar.Text = ""
        SliderBar.Parent = SliderFrame

        local BarCorner = Instance.new("UICorner")
        BarCorner.CornerRadius = UDim.new(1, 0)
        BarCorner.Parent = SliderBar

        local Fill = Instance.new("Frame")
        Fill.Size = UDim2.new((Default - Min) / (Max - Min), 0, 1, 0)
        Fill.BackgroundColor3 = CyanShiny
        Fill.BorderSizePixel = 0
        Fill.Parent = SliderBar

        local function update(input)
            local unit = math.clamp((input.Position.X - SliderBar.AbsolutePosition.X) / SliderBar.AbsoluteSize.X, 0, 1)
            local value = math.floor(Min + (unit * (Max - Min)))
            ValueLabel.Text = tostring(value)
            Fill.Size = UDim2.new(unit, 0, 1, 0)
            if Callback then pcall(Callback, value) end
        end

        local sliding = false
        SliderBar.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                sliding = true
                update(input)
            end
        end)
        UIS.InputChanged:Connect(function(input)
            if sliding and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
                update(input)
            end
        end)
        UIS.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                sliding = false
            end
        end)
    end

    -- 4. TEXTBOX ELEMENT
    function Window:CreateTextBox(Name, Placeholder, Callback)
        local BoxFrame = Instance.new("Frame")
        BoxFrame.Name = "TextBoxFrame"
        BoxFrame.Size = UDim2.new(1, 0, 0, 38)
        BoxFrame.BackgroundColor3 = ComponentBg
        BoxFrame.Parent = ContentContainer

        local Corner = Instance.new("UICorner")
        Corner.CornerRadius = UDim.new(0, 6)
        Corner.Parent = BoxFrame

        local Label = Instance.new("TextLabel")
        Label.Size = UDim2.new(0.4, 0, 1, 0)
        Label.Position = UDim2.fromOffset(10, 0)
        Label.BackgroundTransparency = 1
        Label.Text = Name
        Label.Font = Enum.Font.GothamBold
        Label.TextSize = 11
        Label.TextColor3 = Color3.fromRGB(225, 245, 250)
        Label.TextXAlignment = Enum.TextXAlignment.Left
        Label.Parent = BoxFrame

        local TextBox = Instance.new("TextBox")
        TextBox.Size = UDim2.new(0.55, -10, 0, 24)
        TextBox.Position = UDim2.new(0.45, 0, 0.5, -12)
        TextBox.BackgroundColor3 = Color3.fromRGB(12, 18, 20)
        TextBox.Text = ""
        TextBox.PlaceholderText = Placeholder or "Input..."
        TextBox.PlaceholderColor3 = Color3.fromRGB(70, 90, 95)
        TextBox.TextColor3 = CyanShiny
        TextBox.Font = Enum.Font.Gotham
        TextBox.TextSize = 11
        TextBox.ClearTextOnFocus = false
        TextBox.Parent = BoxFrame

        local BoxCorner = Instance.new("UICorner")
        BoxCorner.CornerRadius = UDim.new(0, 4)
        BoxCorner.Parent = TextBox

        local BoxStroke = Instance.new("UIStroke")
        BoxStroke.Color = Color3.fromRGB(35, 45, 50)
        BoxStroke.Thickness = 1
        BoxStroke.Parent = TextBox

        TextBox.Focused:Connect(function() BoxStroke.Color = CyanS
