--[[
    SHINY CYAN MOBILE-OPTIMIZED UI LIBRARY
    Features: Buttons, Toggles, Sliders, Textboxes, and a Minimized Toggle Button.
    Executor Support: Uses CoreGui with fallback to PlayerGui.
]]

local Library = {}
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")
local Players = game:GetService("Players")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- Target CoreGui if executing on standard exploits, fallback to PlayerGui
local TargetParent = (runcontext and CoreGui) or (syn and CoreGui) or CoreGui or PlayerGui

-- Color Palette
local CyanShiny = Color3.fromRGB(0, 235, 255)
local CyanGlow = Color3.fromRGB(0, 180, 220)
local DarkBg = Color3.fromRGB(15, 18, 22)
local ComponentBg = Color3.fromRGB(25, 30, 38)
local TextColor = Color3.fromRGB(240, 245, 255)

function Library:CreateWindow(titleText)
    titleText = titleText or "Cyan Mobile Hub"
    
    -- Main ScreenGui
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "CyanMobileLib_" .. math.random(1000, 9999)
    ScreenGui.ResetOnSpawn = false
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    ScreenGui.Parent = TargetParent

    -- Toggle Button (Minimizer fixed to Left/Right middle)
    local ToggleButton = Instance.new("TextButton")
    ToggleButton.Name = "MinimizeBtn"
    ToggleButton.Size = UDim2.new(0, 45, 0, 45)
    ToggleButton.Position = UDim2.new(0, 10, 0.5, -22) -- Left edge middle
    ToggleButton.BackgroundColor3 = DarkBg
    ToggleButton.Text = "★"
    ToggleButton.TextColor3 = CyanShiny
    ToggleButton.TextSize = 22
    ToggleButton.Font = Enum.Font.GothamBold
    ToggleButton.Parent = ScreenGui

    local ToggleCorner = Instance.new("UICorner")
    ToggleCorner.CornerRadius = UDim.new(0, 12)
    ToggleCorner.Parent = ToggleButton

    local ToggleStroke = Instance.new("UIStroke")
    ToggleStroke.Color = CyanShiny
    ToggleStroke.Thickness = 1.5
    ToggleStroke.Parent = ToggleButton

    -- Main Container Frame (Compact size perfect for Mobile)
    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "Main"
    MainFrame.Size = UDim2.new(0, 340, 0, 240) -- Small, optimized mobile footprint
    MainFrame.Position = UDim2.new(0.5, -170, 0.5, -120)
    MainFrame.BackgroundColor3 = DarkBg
    MainFrame.Visible = true
    MainFrame.ClipsDescendants = true
    MainFrame.Parent = ScreenGui

    local MainCorner = Instance.new("UICorner")
    MainCorner.CornerRadius = UDim.new(0, 14)
    MainCorner.Parent = MainFrame

    local MainStroke = Instance.new("UIStroke")
    MainStroke.Color = CyanShiny
    MainStroke.Thickness = 2
    MainStroke.Parent = MainFrame

    -- Dragging functionality (Mobile & PC safe)
    local dragging, dragInput, dragStart, startPos
    MainFrame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = MainFrame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    MainFrame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)

    -- Header bar
    local Header = Instance.new("TextLabel")
    Header.Size = UDim2.new(1, 0, 0, 35)
    Header.BackgroundColor3 = Color3.fromRGB(20, 25, 32)
    Header.Text = "  " .. titleText
    Header.TextColor3 = TextColor
    Header.Font = Enum.Font.GothamBold
    Header.TextSize = 14
    Header.TextXAlignment = Enum.TextXAlignment.Left
    Header.Parent = MainFrame

    -- Top Shiny Accent Line
    local Accent = Instance.new("Frame")
    Accent.Size = UDim2.new(1, 0, 0, 2)
    Accent.Position = UDim2.new(0, 0, 0, 33)
    Accent.BackgroundColor3 = CyanShiny
    Accent.BorderSizePixel = 0
    Accent.Parent = Header

    -- Scrolling Container for Features
    local Container = Instance.new("ScrollingFrame")
    Container.Size = UDim2.new(1, -16, 1, -45)
    Container.Position = UDim2.new(0, 8, 0, 40)
    Container.BackgroundTransparency = 1
    Container.CanvasSize = UDim2.new(0, 0, 0, 0)
    Container.ScrollBarThickness = 4
    Container.ScrollBarImageColor3 = CyanShiny
    Container.Parent = MainFrame

    local Layout = Instance.new("UIListLayout")
    Layout.Padding = UDim.new(0, 6)
    Layout.SortOrder = Enum.SortOrder.LayoutOrder
    Layout.Parent = Container

    Layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
        Container.CanvasSize = UDim2.new(0, 0, 0, Layout.AbsoluteContentSize.Y + 10)
    end)

    -- Toggle visibility button logic
    ToggleButton.MouseButton1Click:Connect(function()
        MainFrame.Visible = not MainFrame.Visible
        TweenService:Create(ToggleButton, TweenInfo.new(0.2), {TextColor3 = MainFrame.Visible and CyanShiny or Color3.fromRGB(150, 150, 150)}):Play()
    end)

    local Elements = {}

    -- 1. ADD BUTTON
    function Elements:AddButton(text, callback)
        callback = callback or function() end
        local Btn = Instance.new("TextButton")
        Btn.Size = UDim2.new(1, 0, 0, 32)
        Btn.BackgroundColor3 = ComponentBg
        Btn.Text = text
        Btn.TextColor3 = TextColor
        Btn.Font = Enum.Font.GothamSemibold
        Btn.TextSize = 13
        Btn.Parent = Container

        local Corner = Instance.new("UICorner")
        Corner.CornerRadius = UDim.new(0, 6)
        Corner.Parent = Btn

        Btn.MouseButton1Click:Connect(function()
            Btn.BackgroundColor3 = CyanShiny
            Btn.TextColor3 = DarkBg
            task.wait(0.1)
            Btn.BackgroundColor3 = ComponentBg
            Btn.TextColor3 = TextColor
            callback()
        end)
    end

    -- 2. ADD TOGGLE
    function Elements:AddToggle(text, callback)
        callback = callback or function() end
        local state = false

        local TglFrame = Instance.new("TextButton")
        TglFrame.Size = UDim2.new(1, 0, 0, 32)
        TglFrame.BackgroundColor3 = ComponentBg
        TglFrame.Text = "  " .. text
        TglFrame.TextColor3 = TextColor
        TglFrame.Font = Enum.Font.GothamSemibold
        TglFrame.TextSize = 13
        TglFrame.TextXAlignment = Enum.TextXAlignment.Left
        TglFrame.Parent = Container

        local Corner = Instance.new("UICorner")
        Corner.CornerRadius = UDim.new(0, 6)
        Corner.Parent = TglFrame

        local Indicator = Instance.new("Frame")
        Indicator.Size = UDim2.new(0, 16, 0, 16)
        Indicator.Position = UDim2.new(1, -24, 0.5, -8)
        Indicator.BackgroundColor3 = DarkBg
        Indicator.Parent = TglFrame

        local IndCorner = Instance.new("UICorner")
        IndCorner.CornerRadius = UDim.new(0, 4)
        IndCorner.Parent = Indicator

        local IndStroke = Instance.new("UIStroke")
        IndStroke.Color = Color3.fromRGB(60, 70, 80)
        IndStroke.Thickness = 1.5
        IndStroke.Parent = Indicator

        TglFrame.MouseButton1Click:Connect(function()
            state = not state
            if state then
                TweenService:Create(Indicator, TweenInfo.new(0.15), {BackgroundColor3 = CyanShiny}):Play()
                IndStroke.Color = CyanShiny
            else
                TweenService:Create(Indicator, TweenInfo.new(0.15), {BackgroundColor3 = DarkBg}):Play()
                IndStroke.Color = Color3.fromRGB(60, 70, 80)
            end
            callback(state)
        end)
    end

    -- 3. ADD SLIDER
    function Elements:AddSlider(text, min, max, default, callback)
        callback = callback or function() end
        min = min or 0
        max = max or 100
        default = math.clamp(default or min, min, max)

        local SliderFrame = Instance.new("Frame")
        SliderFrame.Size = UDim2.new(1, 0, 0, 40)
        SliderFrame.BackgroundColor3 = ComponentBg
        SliderFrame.Parent = Container

        local Corner = Instance.new("UICorner")
        Corner.CornerRadius = UDim.new(0, 6)
        Corner.Parent = SliderFrame

        local Label = Instance.new("TextLabel")
        Label.Size = UDim2.new(1, -60, 0, 20)
        Label.Position = UDim2.new(0, 8, 0, 2)
        Label.BackgroundTransparency = 1
        Label.Text = text
        Label.TextColor3 = TextColor
        Label.Font = Enum.Font.GothamSemibold
        Label.TextSize = 12
        Label.TextXAlignment = Enum.TextXAlignment.Left
        Label.Parent = SliderFrame

        local ValueLabel = Instance.new("TextLabel")
        ValueLabel.Size = UDim2.new(0, 50, 0, 20)
        ValueLabel.Position = UDim2.new(1, -58, 0, 2)
        ValueLabel.BackgroundTransparency = 1
        ValueLabel.Text = tostring(default)
        ValueLabel.TextColor3 = CyanShiny
        ValueLabel.Font = Enum.Font.GothamBold
        ValueLabel.TextSize = 12
        ValueLabel.TextXAlignment = Enum.TextXAlignment.Right
        ValueLabel.Parent = SliderFrame

        local SliderBar = Instance.new("TextButton")
        SliderBar.Size = UDim2.new(1, -16, 0, 6)
        SliderBar.Position = UDim2.new(0, 8, 0, 26)
        SliderBar.BackgroundColor3 = DarkBg
        SliderBar.Text = ""
        SliderBar.Parent = SliderFrame

        local BarCorner = Instance.new("UICorner")
        BarCorner.CornerRadius = UDim.new(0, 3)
        BarCorner.Parent = SliderBar

        local Fill = Instance.new("Frame")
        Fill.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
        Fill.BackgroundColor3 = CyanShiny
        Fill.BorderSizePixel = 0
        Fill.Parent = SliderBar

        local FillCorner = Instance.new("UICorner")
        FillCorner.CornerRadius = UDim.new(0, 3)
        FillCorner.Parent = Fill

        local function update(input)
            local unit = math.clamp((input.Position.X - SliderBar.AbsolutePosition.X) / SliderBar.AbsoluteSize.X, 0, 1)
            local value = math.floor(min + (unit * (max - min)))
            ValueLabel.Text = tostring(value)
            Fill.Size = UDim2.new(unit, 0, 1, 0)
            callback(value)
        end

        local sliding = false
        SliderBar.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                sliding = true
                update(input)
            end
        end)

        UserInputService.InputChanged:Connect(function(input)
            if sliding and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
                update(input)
            end
        end)

        UserInputService.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                sliding = false
            end
        end)
    end

    -- 4. ADD TEXTBOX
    function Elements:AddTextBox(text, placeholder, callback)
        callback = callback or function() end
        placeholder = placeholder or "Type here..."

        local BoxFrame = Instance.new("Frame")
        BoxFrame.Size = UDim2.new(1, 0, 0, 36)
        BoxFrame.BackgroundColor3 = ComponentBg
        BoxFrame.Parent = Container

        local Corner = Instance.new("UICorner")
        Corner.CornerRadius = UDim.new(0, 6)
        Corner.Parent = BoxFrame

        local Label = Instance.new("TextLabel")
        Label.Size = UDim2.new(0.4, 0, 1, 0)
        Label.Position = UDim2.new(0, 8, 0, 0)
        Label.BackgroundTransparency = 1
        Label.Text = text
        Label.TextColor3 = TextColor
        Label.Font = Enum.Font.GothamSemibold
        Label.TextSize = 12
        Label.TextXAlignment = Enum.TextXAlignment.Left
        Label.Parent = BoxFrame

        local TextBox = Instance.new("TextBox")
        TextBox.Size = UDim2.new(0.55, -4, 0, 24)
        TextBox.Position = UDim2.new(0.45, 0, 0.5, -12)
        TextBox.BackgroundColor3 = DarkBg
        TextBox.Text = ""
        TextBox.PlaceholderText = placeholder
        TextBox.PlaceholderColor3 = Color3.fromRGB(100, 115, 130)
        TextBox.TextColor3 = CyanShiny
        TextBox.Font = Enum.Font.Gotham
        TextBox.TextSize = 12
        TextBox.ClearTextOnFocus = false
        TextBox.Parent = BoxFrame

        local BoxCorner = Instance.new("UICorner")
        BoxCorner.CornerRadius = UDim.new(0, 4)
        BoxCorner.Parent = TextBox

        local BoxStroke = Instance.new("UIStroke")
        BoxStroke.Color = Color3.fromRGB(50, 60, 70)
        BoxStroke.Thickness = 1
        BoxStroke.Parent = TextBox

        TextBox.Focused:Connect(function()
            BoxStroke.Color = CyanShiny
        end)

        TextBox.FocusLost:Connect(function(enterPressed)
            BoxStroke.Color = Color3.fromRGB(50, 60, 70)
            callback(TextBox.Text, enterPressed)
        end)
    end

    return Elements
end

return Library
