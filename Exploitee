
-- CLIENT AIM ASSIST + ESP
-- PC + MOBILE
-- ROLE TEAM DETECTION + ANTI CIVIL
-- DYNAMIC RETICLE + FIXED VERTICAL OFFSET
-- AIM PART SELECTOR: HEAD / NECK / CHEST

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer
if not LocalPlayer then return end

local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local Camera = workspace.CurrentCamera

-- REAL CROSSHAIR

local DynamicReticle = PlayerGui:WaitForChild("DynamicReticle")
local Crosshair = DynamicReticle:WaitForChild("Crosshair")

local AimYOffset = 56

local function GetRealAimPosition()
    if not Crosshair.Parent or not Crosshair.Visible then
        return nil
    end

    local position = Crosshair.AbsolutePosition
        + Crosshair.AbsoluteSize / 2

    return Vector2.new(position.X, position.Y + AimYOffset)
end

-- CLEANUP

local oldGui = PlayerGui:FindFirstChild("ClientAimAssistUI")
if oldGui then
    oldGui:Destroy()
end

local AimEnabled = false
local ESPEnabled = false
local TeamCheck = true
local WallCheck = true
local AntiCivil = true

local Strength = 35
local FOV = 150

local AimPart = "Neck"
local AimParts = {"Head", "Neck", "Chest"}
local AimPartIndex = 2

local Connections = {}
local ESPObjects = {}
local espTimer = 0

local RayParams = RaycastParams.new()
RayParams.FilterType = Enum.RaycastFilterType.Exclude

local function KeepConnection(connection)
    table.insert(Connections, connection)
    return connection
end

local function Create(className, parent, properties)
    local object = Instance.new(className)

    for property, value in pairs(properties or {}) do
        object[property] = value
    end

    object.Parent = parent
    return object
end

-- TEAM SYSTEM

local function GetTeamInfo(player)
    local role = player:GetAttribute("Role")

    if role == "Police" then
        return "Polícia", Color3.fromRGB(55, 145, 255)
    elseif role == "Criminal" then
        return "Criminal", Color3.fromRGB(255, 65, 65)
    elseif role == "Civilian" then
        return "Civil", Color3.fromRGB(80, 220, 120)
    elseif role == "Spectator" then
        return "Espectador", Color3.fromRGB(180, 180, 180)
    elseif role == nil then
        return "Lobby/Indefinido", Color3.fromRGB(180, 180, 180)
    end

    return tostring(role), Color3.fromRGB(220, 220, 220)
end

-- TEAM CHECK + ANTI CIVIL

local function IsTeammate(player)
    local myRole = LocalPlayer:GetAttribute("Role")
    local targetRole = player:GetAttribute("Role")

    if not myRole or not targetRole then
        return true
    end

    if myRole == "Spectator" or targetRole == "Spectator" then
        return true
    end

    if AntiCivil and targetRole == "Civilian" then
        return true
    end

    if TeamCheck and myRole == targetRole then
        return true
    end

    return false
end

-- GUI

local GUI = Create("ScreenGui", PlayerGui, {
    Name = "ClientAimAssistUI",
    ResetOnSpawn = false,
    IgnoreGuiInset = true,
    ZIndexBehavior = Enum.ZIndexBehavior.Sibling
})

local FOVCircle = Create("Frame", GUI, {
    Name = "FOVCircle",
    AnchorPoint = Vector2.new(0.5, 0.5),
    BackgroundTransparency = 1,
    BorderSizePixel = 0,
    Visible = false,
    ZIndex = 2
})

Create("UICorner", FOVCircle, {
    CornerRadius = UDim.new(1, 0)
})

Create("UIStroke", FOVCircle, {
    Color = Color3.fromRGB(80, 170, 255),
    Thickness = 1.5,
    Transparency = 0.2
})

local Panel = Create("Frame", GUI, {
    Name = "MainPanel",
    Size = UDim2.fromOffset(300, 480),
    Position = UDim2.new(0.5, -150, 0.5, -240),
    BackgroundColor3 = Color3.fromRGB(20, 22, 29),
    BorderSizePixel = 0,
    ZIndex = 5
})

Create("UICorner", Panel, {
    CornerRadius = UDim.new(0, 10)
})

Create("UIStroke", Panel, {
    Color = Color3.fromRGB(65, 85, 115),
    Thickness = 1
})

local Header = Create("Frame", Panel, {
    Size = UDim2.new(1, 0, 0, 43),
    BackgroundColor3 = Color3.fromRGB(29, 35, 47),
    BorderSizePixel = 0,
    ZIndex = 6
})

Create("UICorner", Header, {
    CornerRadius = UDim.new(0, 10)
})

Create("TextLabel", Header, {
    Size = UDim2.new(1, -55, 1, 0),
    Position = UDim2.fromOffset(13, 0),
    BackgroundTransparency = 1,
    Text = "CLIENT ASSIST | ESP",
    TextColor3 = Color3.fromRGB(220, 230, 245),
    TextSize = 14,
    Font = Enum.Font.GothamBold,
    TextXAlignment = Enum.TextXAlignment.Left,
    ZIndex = 7
})

local CloseButton = Create("TextButton", Header, {
    Size = UDim2.fromOffset(35, 30),
    Position = UDim2.new(1, -40, 0, 6),
    BackgroundColor3 = Color3.fromRGB(160, 55, 65),
    Text = "X",
    TextColor3 = Color3.new(1, 1, 1),
    TextSize = 14,
    Font = Enum.Font.GothamBold,
    ZIndex = 8
})

Create("UICorner", CloseButton, {
    CornerRadius = UDim.new(0, 6)
})

local Content = Create("ScrollingFrame", Panel, {
    Position = UDim2.fromOffset(8, 50),
    Size = UDim2.new(1, -16, 1, -58),
    BackgroundTransparency = 1,
    BorderSizePixel = 0,
    ScrollBarThickness = 3,
    CanvasSize = UDim2.new(0, 0, 0, 0),
    AutomaticCanvasSize = Enum.AutomaticSize.Y,
    ZIndex = 6
})

Create("UIListLayout", Content, {
    Padding = UDim.new(0, 7),
    SortOrder = Enum.SortOrder.LayoutOrder
})

Create("UIPadding", Content, {
    PaddingTop = UDim.new(0, 3),
    PaddingBottom = UDim.new(0, 8)
})

local OpenButton = Create("TextButton", GUI, {
    Name = "FloatingButton",
    Size = UDim2.fromOffset(55, 55),
    Position = UDim2.new(1, -72, 1, -115),
    BackgroundColor3 = Color3.fromRGB(35, 105, 185),
    Text = "AIM",
    TextColor3 = Color3.new(1, 1, 1),
    TextSize = 16,
    Font = Enum.Font.GothamBold,
    ZIndex = 20
})

Create("UICorner", OpenButton, {
    CornerRadius = UDim.new(1, 0)
})

KeepConnection(CloseButton.Activated:Connect(function()
    Panel.Visible = false
end))

-- PANEL DRAG

local PanelDragging = false
local PanelDragStart
local PanelStartPosition

KeepConnection(Header.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then

        PanelDragging = true
        PanelDragStart = input.Position
        PanelStartPosition = Panel.Position
    end
end))

KeepConnection(UserInputService.InputChanged:Connect(function(input)
    if not PanelDragging then return end

    if input.UserInputType == Enum.UserInputType.MouseMovement
        or input.UserInputType == Enum.UserInputType.Touch then

        local delta = input.Position - PanelDragStart

        Panel.Position = UDim2.new(
            PanelStartPosition.X.Scale,
            PanelStartPosition.X.Offset + delta.X,
            PanelStartPosition.Y.Scale,
            PanelStartPosition.Y.Offset + delta.Y
        )
    end
end))

KeepConnection(UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
        PanelDragging = false
    end
end))

-- FLOATING BUTTON DRAG

local ButtonDragging = false
local ButtonMoved = false
local ButtonDragStart
local ButtonStartPosition
local ButtonDragInput

KeepConnection(OpenButton.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then

        ButtonDragging = true
        ButtonMoved = false
        ButtonDragStart = input.Position
        ButtonStartPosition = OpenButton.AbsolutePosition

        if input.UserInputType == Enum.UserInputType.Touch then
            ButtonDragInput = input
        else
            ButtonDragInput = nil
        end
    end
end))

KeepConnection(UserInputService.InputChanged:Connect(function(input)
    if not ButtonDragging then return end

    local isMouseMove =
        input.UserInputType == Enum.UserInputType.MouseMovement

    local isTouchMove =
        ButtonDragInput ~= nil and input == ButtonDragInput

    if isMouseMove or isTouchMove then
        local delta = input.Position - ButtonDragStart

        if delta.Magnitude > 6 then
            ButtonMoved = true
        end

        local viewport = Camera.ViewportSize
        local size = OpenButton.AbsoluteSize

        local x = math.clamp(
            ButtonStartPosition.X + delta.X,
            0,
            viewport.X - size.X
        )

        local y = math.clamp(
            ButtonStartPosition.Y + delta.Y,
            0,
            viewport.Y - size.Y
        )

        OpenButton.Position = UDim2.fromOffset(x, y)
    end
end))

KeepConnection(UserInputService.InputEnded:Connect(function(input)
    local mouseEnded =
        input.UserInputType == Enum.UserInputType.MouseButton1

    local touchEnded =
        ButtonDragInput ~= nil and input == ButtonDragInput

    if ButtonDragging and (mouseEnded or touchEnded) then
        ButtonDragging = false

        if not ButtonMoved then
            Panel.Visible = not Panel.Visible
        end

        ButtonDragInput = nil
    end
end))

-- FOV

local function UpdateFOVCircle()
    if not Camera then return end

    local aimPosition = GetRealAimPosition()

    if not aimPosition then
        FOVCircle.Visible = false
        return
    end

    FOVCircle.Position = UDim2.fromOffset(
        aimPosition.X,
        aimPosition.Y
    )

    FOVCircle.Size = UDim2.fromOffset(FOV * 2, FOV * 2)
    FOVCircle.Visible = AimEnabled
end

-- ESP CLEANUP

local function RemoveESP(player)
    local record = ESPObjects[player]
    if not record then return end

    if record.Highlight then
        record.Highlight:Destroy()
    end

    if record.Billboard then
        record.Billboard:Destroy()
    end

    ESPObjects[player] = nil
end

local function ClearESP()
    for player in pairs(ESPObjects) do
        RemoveESP(player)
    end
end

-- ESP UPDATE

local function UpdateESPForPlayer(player)
    if not ESPEnabled or player == LocalPlayer then
        RemoveESP(player)
        return
    end

    local character = player.Character
    local head = character and character:FindFirstChild("Head")

    if not character or not head then
        RemoveESP(player)
        return
    end

    local teamName, teamColor = GetTeamInfo(player)
    local record = ESPObjects[player]

    if record and record.Character ~= character then
        RemoveESP(player)
        record = nil
    end

    if not record then
        local highlight = Create("Highlight", character, {
            Name = "ClientESPHighlight",
            Adornee = character,
            FillColor = teamColor,
            OutlineColor = teamColor,
            FillTransparency = 0.55,
            OutlineTransparency = 0,
            DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
        })

        local billboard = Create("BillboardGui", PlayerGui, {
            Name = "ClientESPLabel",
            Adornee = head,
            Size = UDim2.fromOffset(180, 40),
            StudsOffset = Vector3.new(0, 2.8, 0),
            AlwaysOnTop = true
        })

        local label = Create("TextLabel", billboard, {
            Size = UDim2.fromScale(1, 1),
            BackgroundTransparency = 1,
            TextColor3 = teamColor,
            TextStrokeTransparency = 0.2,
            TextSize = 12,
            TextScaled = true,
            Font = Enum.Font.GothamBold,
            Text = ""
        })

        record = {
            Character = character,
            Highlight = highlight,
            Billboard = billboard,
            Label = label
        }

        ESPObjects[player] = record
    end

    record.Highlight.FillColor = teamColor
    record.Highlight.OutlineColor = teamColor
    record.Billboard.Adornee = head
    record.Label.TextColor3 = teamColor
    record.Label.Text = player.DisplayName .. "\n" .. teamName
end

-- CONTROLS

local function CreateToggle(label, getter, setter)
    local button = Create("TextButton", Content, {
        Size = UDim2.new(1, -4, 0, 40),
        BackgroundColor3 = Color3.fromRGB(35, 40, 51),
        TextColor3 = Color3.fromRGB(230, 235, 245),
        TextSize = 12,
        Font = Enum.Font.GothamSemibold,
        TextXAlignment = Enum.TextXAlignment.Left,
        ZIndex = 7
    })

    Create("UICorner", button, {
        CornerRadius = UDim.new(0, 6)
    })

    Create("UIPadding", button, {
        PaddingLeft = UDim.new(0, 12)
    })

    local function refresh()
        local enabled = getter()

        button.Text = label .. ": " .. (enabled and "ON" or "OFF")

        button.BackgroundColor3 = enabled
            and Color3.fromRGB(35, 100, 75)
            or Color3.fromRGB(35, 40, 51)
    end

    KeepConnection(button.Activated:Connect(function()
        local newValue = not getter()
        setter(newValue)

        if label == "ESP" and not newValue then
            ClearESP()
        end

        refresh()
        UpdateFOVCircle()
    end))

    refresh()
end

-- AIM PART SELECTOR

local function CreateAimPartSelector()
    local button = Create("TextButton", Content, {
        Size = UDim2.new(1, -4, 0, 40),
        BackgroundColor3 = Color3.fromRGB(35, 65, 100),
        TextColor3 = Color3.fromRGB(230, 235, 245),
        TextSize = 12,
        Font = Enum.Font.GothamSemibold,
        Text = "Aim Part: " .. AimPart .. "  >",
        ZIndex = 7
    })

    Create("UICorner", button, {
        CornerRadius = UDim.new(0, 6)
    })

    local function refresh()
        button.Text = "Aim Part: " .. AimPart .. "  >"
    end

    KeepConnection(button.Activated:Connect(function()
        AimPartIndex += 1

        if AimPartIndex > #AimParts then
            AimPartIndex = 1
        end

        AimPart = AimParts[AimPartIndex]
        refresh()
    end))

    refresh()
end

local function CreateSlider(label, minimum, maximum, initial, suffix, callback)
    local container = Create("Frame", Content, {
        Size = UDim2.new(1, -4, 0, 62),
        BackgroundColor3 = Color3.fromRGB(30, 35, 45),
        BorderSizePixel = 0,
        ZIndex = 7
    })

    Create("UICorner", container, {
        CornerRadius = UDim.new(0, 6)
    })

    local title = Create("TextLabel", container, {
        Position = UDim2.fromOffset(10, 3),
        Size = UDim2.new(1, -20, 0, 23),
        BackgroundTransparency = 1,
        TextColor3 = Color3.fromRGB(225, 230, 240),
        TextSize = 12,
        Font = Enum.Font.GothamSemibold,
        TextXAlignment = Enum.TextXAlignment.Left,
        ZIndex = 8
    })

    local track = Create("Frame", container, {
        Position = UDim2.new(0, 12, 0, 40),
        Size = UDim2.new(1, -24, 0, 6),
        BackgroundColor3 = Color3.fromRGB(65, 70, 85),
        BorderSizePixel = 0,
        ZIndex = 8
    })

    Create("UICorner", track, {
        CornerRadius = UDim.new(1, 0)
    })

    local fill = Create("Frame", track, {
        Size = UDim2.new(0, 0, 1, 0),
        BackgroundColor3 = Color3.fromRGB(65, 155, 245),
        BorderSizePixel = 0,
        ZIndex = 9
    })

    Create("UICorner", fill, {
        CornerRadius = UDim.new(1, 0)
    })

    local knob = Create("Frame", track, {
        Size = UDim2.fromOffset(13, 13),
        AnchorPoint = Vector2.new(0.5, 0.5),
        Position = UDim2.new(0, 0, 0.5, 0),
        BackgroundColor3 = Color3.fromRGB(235, 240, 250),
        BorderSizePixel = 0,
        ZIndex = 10
    })

    Create("UICorner", knob, {
        CornerRadius = UDim.new(1, 0)
    })

    local function setValue(value)
        value = math.clamp(
            math.floor(value + 0.5),
            minimum,
            maximum
        )

        local alpha = (value - minimum) / (maximum - minimum)

        title.Text = label .. ": " .. value .. suffix
        fill.Size = UDim2.new(alpha, 0, 1, 0)
        knob.Position = UDim2.new(alpha, 0, 0.5, 0)

        callback(value)
    end

    local sliderDragging = false
    local sliderTouchInput = nil

    local function updateFromX(x)
        local width = track.AbsoluteSize.X
        if width <= 0 then return end

        local alpha = math.clamp(
            (x - track.AbsolutePosition.X) / width,
            0,
            1
        )

        setValue(minimum + (maximum - minimum) * alpha)
    end

    KeepConnection(track.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
            or input.UserInputType == Enum.UserInputType.Touch then

            sliderDragging = true

            if input.UserInputType == Enum.UserInputType.Touch then
                sliderTouchInput = input
            end

            updateFromX(input.Position.X)
        end
    end))

    KeepConnection(UserInputService.InputChanged:Connect(function(input)
        if not sliderDragging then return end

        if input.UserInputType == Enum.UserInputType.MouseMovement then
            updateFromX(input.Position.X)
        elseif input.UserInputType == Enum.UserInputType.Touch
            and sliderTouchInput then
            updateFromX(input.Position.X)
        end
    end))

    KeepConnection(UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
            or (sliderTouchInput and input == sliderTouchInput) then

            sliderDragging = false
            sliderTouchInput = nil
        end
    end))

    setValue(initial)
end

-- CREATE CONTROLS

CreateToggle("Aim Assist", function()
    return AimEnabled
end, function(value)
    AimEnabled = value
end)

CreateToggle("ESP", function()
    return ESPEnabled
end, function(value)
    ESPEnabled = value
end)

CreateToggle("Team Check", function()
    return TeamCheck
end, function(value)
    TeamCheck = value
end)

CreateToggle("Anti Civil", function()
    return AntiCivil
end, function(value)
    AntiCivil = value
end)

CreateToggle("Wall Check", function()
    return WallCheck
end, function(value)
    WallCheck = value
end)

CreateAimPartSelector()

CreateSlider("Strength", 1, 100, Strength, "%", function(value)
    Strength = value
end)

CreateSlider("FOV", 50, 400, FOV, " px", function(value)
    FOV = value
    UpdateFOVCircle()
end)

-- AIM POSITION

local function GetAimPosition(character)
    if not character then return nil end

    local head = character:FindFirstChild("Head")
    local upperTorso = character:FindFirstChild("UpperTorso")
    local torso = character:FindFirstChild("Torso")

    if AimPart == "Head" then
        return head and head.Position
    elseif AimPart == "Chest" then
        local chest = upperTorso or torso
        return chest and chest.Position
    elseif AimPart == "Neck" then
        local chest = upperTorso or torso

        if head and chest then
            return head.Position:Lerp(chest.Position, 0.25)
        end

        return head and head.Position
    end

    return head and head.Position
end

-- WALL CHECK

local function IsVisible(character, targetPosition)
    local localCharacter = LocalPlayer.Character

    RayParams.FilterDescendantsInstances = localCharacter
        and {localCharacter}
        or {}

    local origin = Camera.CFrame.Position
    local direction = targetPosition - origin

    local result = workspace:Raycast(
        origin,
        direction,
        RayParams
    )

    return result == nil
        or result.Instance:IsDescendantOf(character)
end

-- TARGET SELECTION

local function GetBestTarget()
    if not Camera then return nil end

    local screenCenter = GetRealAimPosition()

    if not screenCenter then
        return nil
    end

    local bestPlayer = nil
    local bestPosition = nil
    local bestDistance = FOV

    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            if IsTeammate(player) then
                continue
            end

            local character = player.Character
            local humanoid = character
                and character:FindFirstChildOfClass("Humanoid")
