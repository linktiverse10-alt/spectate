-- Spectate + Teleport — by Amar
-- Dropdown, recents, player info, hard-rebind camera, resizable, unload

-- ============================================================
--  EDIT YOUR INSULTS HERE
--  Add as many lines as you want between the { and the }.
--  Each line must end with a comma.
--  Any length works — the button expands to fit.
-- ============================================================
local INSULTS = {
    "your stupid is showing",
    "You must be a nigger, you can't even read the 'NO CHARACTER' sign. Maybe it's too many words for your monkey brain.",
    "Spectate failed. Guess that's what happens when a retard tries to use a computer.",
    "Character missing? Just like your dad when you were conceived, you worthless little cunt",
    "Spectate failed",
    "Fuck you",
    "Character missing? Just like your IQ when you decided to be a retard. Now go die in a gas chamber.",
    "Character missing? Just like your future, nonexistent. Now go hang yourself, faggot.",
    "Not streamed. Maybe because Roblox hates niggers as much as everyone else does.",
    "Your IQ is lower than the price of a nigger's soul. Now go back to Africa and die of AIDS.",
    "Not streamed. Maybe because Roblox banned all kikes and niggers last week. Oops.",
    "Not streamed? Maybe because your mom was a crackhead and you're a retard. Good job, spic.",
    "Not streamed? Maybe because your single-digit IQ can't even turn on a computer. Try playing with blocks instead.",
    "Target acquired. Unfortunately, the target has no body",
    "Your so stupid, you'd get lost in a fucking elevator.",
    "Your nigger ass just got purged from our white nation, stay mad, subhuman.",
    "Your coon name triggered our filters. Leave and never come back, ape.",
    "Your ape-like behavior triggered our filters. Stay gone, nigger.",
    "Kike detected, banning your entire synagogue's IP range.",

 
}

local Players          = game:GetService("Players")
local RunService       = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

-- ============================================================
--  LIFECYCLE
-- ============================================================
_G.SPECTATE_ACTIVE = true

local Connections = {}
local function bind(c)
    table.insert(Connections, c)
    return c
end

bind(workspace:GetPropertyChangedSignal("CurrentCamera"):Connect(function()
    if workspace.CurrentCamera then camera = workspace.CurrentCamera end
end))

-- ============================================================
--  GUI
-- ============================================================
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "SpectatePanel"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.Parent = player:WaitForChild("PlayerGui")

local toggleBtn = Instance.new("TextButton")
toggleBtn.Name = "ToggleBtn"
toggleBtn.Size = UDim2.new(0, 150, 0, 34)
toggleBtn.Position = UDim2.new(0, 16, 0, 44)
toggleBtn.BackgroundColor3 = Color3.fromRGB(140, 0, 160)
toggleBtn.BorderSizePixel = 0
toggleBtn.Text = "👁  SPECTATE"
toggleBtn.TextColor3 = Color3.new(1, 1, 1)
toggleBtn.Font = Enum.Font.GothamBold
toggleBtn.TextSize = 13
toggleBtn.Active = true
toggleBtn.Draggable = true
toggleBtn.Parent = screenGui
Instance.new("UICorner", toggleBtn).CornerRadius = UDim.new(1, 0)
local toggleStroke = Instance.new("UIStroke", toggleBtn)
toggleStroke.Color = Color3.fromRGB(200, 60, 220)
toggleStroke.Thickness = 1.5

local MIN_W, MIN_H = 260, 320
local DEFAULT_W, DEFAULT_H = 320, 330

local frame = Instance.new("Frame")
frame.Name = "Window"
frame.Size = UDim2.new(0, DEFAULT_W, 0, DEFAULT_H)
frame.Position = UDim2.new(0.5, -DEFAULT_W/2, 0.5, -DEFAULT_H/2)
frame.BackgroundColor3 = Color3.fromRGB(15, 15, 22)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true
frame.Parent = screenGui
Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 12)
local frameStroke = Instance.new("UIStroke", frame)
frameStroke.Color = Color3.fromRGB(0, 180, 216)
frameStroke.Thickness = 2

local header = Instance.new("TextLabel")
header.Size = UDim2.new(1, 0, 0, 38)
header.BackgroundColor3 = Color3.fromRGB(22, 22, 32)
header.BorderSizePixel = 0
header.Text = "  👁  SPECTATE"
header.TextColor3 = Color3.new(1, 1, 1)
header.Font = Enum.Font.GothamBold
header.TextSize = 14
header.TextXAlignment = Enum.TextXAlignment.Left
header.Parent = frame
Instance.new("UICorner", header).CornerRadius = UDim.new(0, 12)

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 26, 0, 26)
closeBtn.Position = UDim2.new(1, -32, 0, 6)
closeBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
closeBtn.Text = "X"
closeBtn.TextColor3 = Color3.new(1, 1, 1)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.TextSize = 14
closeBtn.BorderSizePixel = 0
closeBtn.Parent = frame
Instance.new("UICorner", closeBtn).CornerRadius = UDim.new(1, 0)

local unloadBtn = Instance.new("TextButton")
unloadBtn.Size = UDim2.new(0, 26, 0, 26)
unloadBtn.Position = UDim2.new(1, -64, 0, 6)
unloadBtn.BackgroundColor3 = Color3.fromRGB(90, 40, 40)
unloadBtn.Text = "⏻"
unloadBtn.TextColor3 = Color3.new(1, 1, 1)
unloadBtn.Font = Enum.Font.GothamBold
unloadBtn.TextSize = 14
unloadBtn.BorderSizePixel = 0
unloadBtn.Parent = frame
Instance.new("UICorner", unloadBtn).CornerRadius = UDim.new(1, 0)

local dropdownLabel = Instance.new("TextLabel")
dropdownLabel.Size = UDim2.new(1, -20, 0, 14)
dropdownLabel.Position = UDim2.new(0, 10, 0, 44)
dropdownLabel.BackgroundTransparency = 1
dropdownLabel.Text = "TARGET"
dropdownLabel.TextColor3 = Color3.fromRGB(140, 140, 155)
dropdownLabel.Font = Enum.Font.GothamBold
dropdownLabel.TextSize = 11
dropdownLabel.TextXAlignment = Enum.TextXAlignment.Left
dropdownLabel.Parent = frame

local dropdownBtn = Instance.new("TextButton")
dropdownBtn.Size = UDim2.new(1, -66, 0, 34)
dropdownBtn.Position = UDim2.new(0, 10, 0, 60)
dropdownBtn.BackgroundColor3 = Color3.fromRGB(28, 28, 38)
dropdownBtn.BorderSizePixel = 0
dropdownBtn.Text = "-- select a player --"
dropdownBtn.TextColor3 = Color3.new(1, 1, 1)
dropdownBtn.Font = Enum.Font.Gotham
dropdownBtn.TextSize = 14
dropdownBtn.TextXAlignment = Enum.TextXAlignment.Left
dropdownBtn.Parent = frame
Instance.new("UICorner", dropdownBtn).CornerRadius = UDim.new(0, 6)
Instance.new("UIStroke", dropdownBtn).Color = Color3.fromRGB(45, 45, 60)

local refreshBtn = Instance.new("TextButton")
refreshBtn.Size = UDim2.new(0, 36, 0, 34)
refreshBtn.Position = UDim2.new(1, -46, 0, 60)
refreshBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 199)
refreshBtn.BorderSizePixel = 0
refreshBtn.Text = "⟳"
refreshBtn.TextColor3 = Color3.new(1, 1, 1)
refreshBtn.Font = Enum.Font.GothamBold
refreshBtn.TextSize = 18
refreshBtn.Parent = frame
Instance.new("UICorner", refreshBtn).CornerRadius = UDim.new(0, 6)

local dropdownList = Instance.new("ScrollingFrame")
dropdownList.Size = UDim2.new(1, -20, 0, 0)
dropdownList.Position = UDim2.new(0, 10, 0, 96)
dropdownList.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
dropdownList.BorderSizePixel = 0
dropdownList.ScrollBarThickness = 5
dropdownList.CanvasSize = UDim2.new(0, 0, 0, 0)
dropdownList.Visible = false
dropdownList.ZIndex = 5
dropdownList.Parent = frame
Instance.new("UICorner", dropdownList).CornerRadius = UDim.new(0, 6)
Instance.new("UIStroke", dropdownList).Color = Color3.fromRGB(0, 180, 216)

local dropdownLayout = Instance.new("UIListLayout", dropdownList)
dropdownLayout.Padding = UDim.new(0, 2)
dropdownLayout.SortOrder = Enum.SortOrder.LayoutOrder

local infoFrame = Instance.new("Frame")
infoFrame.Size = UDim2.new(1, -20, 0, 46)
infoFrame.Position = UDim2.new(0, 10, 0, 100)
infoFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
infoFrame.BorderSizePixel = 0
infoFrame.Parent = frame
Instance.new("UICorner", infoFrame).CornerRadius = UDim.new(0, 6)
Instance.new("UIStroke", infoFrame).Color = Color3.fromRGB(45, 45, 60)

local infoName = Instance.new("TextLabel")
infoName.Size = UDim2.new(1, -12, 0, 20)
infoName.Position = UDim2.new(0, 6, 0, 4)
infoName.BackgroundTransparency = 1
infoName.Text = "no target selected"
infoName.TextColor3 = Color3.fromRGB(180, 180, 195)
infoName.Font = Enum.Font.GothamBold
infoName.TextSize = 13
infoName.TextXAlignment = Enum.TextXAlignment.Left
infoName.TextTruncate = Enum.TextTruncate.AtEnd
infoName.Parent = infoFrame

local infoStatus = Instance.new("TextLabel")
infoStatus.Size = UDim2.new(1, -12, 0, 16)
infoStatus.Position = UDim2.new(0, 6, 0, 24)
infoStatus.BackgroundTransparency = 1
infoStatus.Text = "—"
infoStatus.TextColor3 = Color3.fromRGB(120, 120, 135)
infoStatus.Font = Enum.Font.Gotham
infoStatus.TextSize = 11
infoStatus.TextXAlignment = Enum.TextXAlignment.Left
infoStatus.Parent = infoFrame

local specBtn = Instance.new("TextButton")
specBtn.Size = UDim2.new(0.5, -15, 0, 40)
specBtn.Position = UDim2.new(0, 10, 0, 152)
specBtn.BackgroundColor3 = Color3.fromRGB(140, 0, 160)
specBtn.BorderSizePixel = 0
specBtn.Text = "SPECTATE"
specBtn.TextColor3 = Color3.new(1, 1, 1)
specBtn.Font = Enum.Font.GothamBold
specBtn.TextSize = 13
specBtn.TextWrapped = true
specBtn.TextXAlignment = Enum.TextXAlignment.Center
specBtn.TextYAlignment = Enum.TextYAlignment.Center
specBtn.Parent = frame
Instance.new("UICorner", specBtn).CornerRadius = UDim.new(0, 6)

local tpBtn = Instance.new("TextButton")
tpBtn.Size = UDim2.new(0.5, -15, 0, 40)
tpBtn.Position = UDim2.new(0.5, 5, 0, 152)
tpBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 199)
tpBtn.BorderSizePixel = 0
tpBtn.Text = "TELEPORT TO"
tpBtn.TextColor3 = Color3.new(1, 1, 1)
tpBtn.Font = Enum.Font.GothamBold
tpBtn.TextSize = 13
tpBtn.Parent = frame
Instance.new("UICorner", tpBtn).CornerRadius = UDim.new(0, 6)

local recentsLabel = Instance.new("TextLabel")
recentsLabel.Size = UDim2.new(1, -20, 0, 12)
recentsLabel.Position = UDim2.new(0, 10, 0, 198)
recentsLabel.BackgroundTransparency = 1
recentsLabel.Text = "RECENT"
recentsLabel.TextColor3 = Color3.fromRGB(140, 140, 155)
recentsLabel.Font = Enum.Font.GothamBold
recentsLabel.TextSize = 10
recentsLabel.TextXAlignment = Enum.TextXAlignment.Left
recentsLabel.Parent = frame

local recentsRow = Instance.new("Frame")
recentsRow.Size = UDim2.new(1, -20, 0, 26)
recentsRow.Position = UDim2.new(0, 10, 0, 212)
recentsRow.BackgroundTransparency = 1
recentsRow.Parent = frame

local recentsLayout = Instance.new("UIListLayout", recentsRow)
recentsLayout.FillDirection = Enum.FillDirection.Horizontal
recentsLayout.Padding = UDim.new(0, 4)
recentsLayout.SortOrder = Enum.SortOrder.LayoutOrder

local status = Instance.new("TextLabel")
status.Position = UDim2.new(0, 10, 0, 246)
status.Size = UDim2.new(1, -20, 1, -288)
status.BackgroundTransparency = 1
status.Text = "pick a player from the list, then SPECTATE or TELEPORT TO"
status.TextColor3 = Color3.fromRGB(150, 150, 165)
status.Font = Enum.Font.Gotham
status.TextSize = 12
status.TextWrapped = true
status.TextXAlignment = Enum.TextXAlignment.Left
status.TextYAlignment = Enum.TextYAlignment.Top
status.Parent = frame

local hint = Instance.new("TextLabel")
hint.Size = UDim2.new(1, -20, 0, 14)
hint.Position = UDim2.new(0, 10, 1, -36)
hint.BackgroundTransparency = 1
hint.Text = "drag edges to resize · floating button hides/shows"
hint.TextColor3 = Color3.fromRGB(100, 100, 115)
hint.Font = Enum.Font.Gotham
hint.TextSize = 10
hint.TextXAlignment = Enum.TextXAlignment.Left
hint.Parent = frame

local credit = Instance.new("TextLabel")
credit.Size = UDim2.new(1, -20, 0, 16)
credit.Position = UDim2.new(0, 10, 1, -18)
credit.BackgroundTransparency = 1
credit.Text = "made by Amar"
credit.TextColor3 = Color3.fromRGB(0, 180, 216)
credit.Font = Enum.Font.GothamBold
credit.TextSize = 11
credit.TextXAlignment = Enum.TextXAlignment.Right
credit.Parent = frame

local resizeMode = nil
local resizeStartMouse = nil
local resizeStartSize = nil

local function makeHandle(name, size, position, mode, hoverShape)
    local h = Instance.new("TextButton")
    h.Name = name
    h.Size = size
    h.Position = position
    h.BackgroundTransparency = 1
    h.Text = ""
    h.AutoButtonColor = false
    h.ZIndex = 10
    h.Parent = frame

    local hover = Instance.new("Frame")
    hover.Name = "Hover"
    hover.BackgroundColor3 = Color3.fromRGB(0, 180, 216)
    hover.BackgroundTransparency = 1
    hover.BorderSizePixel = 0
    hover.ZIndex = 10
    hover.Parent = h
    if hoverShape == "vertical" then
        hover.Size = UDim2.new(0, 2, 1, -12)
        hover.Position = UDim2.new(1, -2, 0, 6)
    elseif hoverShape == "horizontal" then
        hover.Size = UDim2.new(1, -12, 0, 2)
        hover.Position = UDim2.new(0, 6, 1, -2)
    else
        hover.Size = UDim2.new(0, 10, 0, 10)
        hover.Position = UDim2.new(1, -10, 1, -10)
    end
    Instance.new("UICorner", hover).CornerRadius = UDim.new(0, 2)

    h.MouseEnter:Connect(function() hover.BackgroundTransparency = 0.4 end)
    h.MouseLeave:Connect(function() hover.BackgroundTransparency = 1 end)
    h.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            resizeMode = mode
            resizeStartMouse = UserInputService:GetMouseLocation()
            resizeStartSize = frame.AbsoluteSize
        end
    end)
    return h
end

makeHandle("HandleRight",  UDim2.new(0, 8, 1, 0),   UDim2.new(1, -8, 0, 0),     "right",  "vertical")
makeHandle("HandleBottom", UDim2.new(1, 0, 0, 8),   UDim2.new(0, 0, 1, -8),     "bottom", "horizontal")
makeHandle("HandleCorner", UDim2.new(0, 16, 0, 16), UDim2.new(1, -16, 1, -16), "corner", "corner")

bind(UserInputService.InputChanged:Connect(function(input)
    if not resizeMode then return end
    if input.UserInputType ~= Enum.UserInputType.MouseMovement then return end

    local now = UserInputService:GetMouseLocation()
    local delta = now - resizeStartMouse

    local newW = resizeStartSize.X
    local newH = resizeStartSize.Y

    if resizeMode == "right" or resizeMode == "corner" then
        newW = math.max(MIN_W, resizeStartSize.X + delta.X)
    end
    if resizeMode == "bottom" or resizeMode == "corner" then
        newH = math.max(MIN_H, resizeStartSize.Y + delta.Y)
    end

    frame.Size = UDim2.new(0, newW, 0, newH)
end))

bind(UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        resizeMode = nil
    end
end))

local isSpectating = false
local spectatedPlayer = nil
local selectedPlayer = nil
local lastTeleport = 0
local TELEPORT_COOLDOWN = 0.5

local RECENTS_MAX = 5
local recents = {}

local function setStatus(text, kind)
    status.Text = text
    if kind == "ok" then
        status.TextColor3 = Color3.fromRGB(74, 222, 128)
    elseif kind == "err" then
        status.TextColor3 = Color3.fromRGB(248, 113, 113)
    else
        status.TextColor3 = Color3.fromRGB(150, 150, 165)
    end
end

local function getRealHumanoid(plr)
    if not plr then return nil end
    local c = plr.Character
    if not c then return nil end
    if not c:IsDescendantOf(workspace) then return nil end
    local hrp = c:FindFirstChild("HumanoidRootPart")
    if not hrp or not hrp:IsDescendantOf(workspace) then return nil end
    local hum = c:FindFirstChildOfClass("Humanoid")
    if not hum then return nil end
    return hum
end

local function requestStream(targetPlayer)
    if not targetPlayer or not targetPlayer.Parent then return end
    local char = targetPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    pcall(function()
        if hrp then
            targetPlayer:RequestStreamAroundAsync(hrp.Position, 2)
        else
            targetPlayer:RequestStreamAroundAsync(Vector3.zero, 2)
        end
    end)
end

local rebinding = false

local function forceCameraBind(subject)
    if rebinding then return false end
    rebinding = true

    if not camera and workspace.CurrentCamera then
        camera = workspace.CurrentCamera
    end
    if not camera then
        rebinding = false
        return false
    end

    pcall(function() camera.CameraType = Enum.CameraType.Scriptable end)
    task.wait()
    pcall(function() camera.CameraSubject = nil end)
    task.wait()
    task.wait()

    local ok = pcall(function()
        camera.CameraType = Enum.CameraType.Custom
        camera.CameraSubject = subject
    end)
    task.wait()

    local stuck = (camera and camera.CameraSubject == subject)
    rebinding = false
    return ok and stuck
end

local function refreshInfo()
    if not selectedPlayer or not selectedPlayer.Parent then
        infoName.Text = "no target selected"
        infoName.TextColor3 = Color3.fromRGB(180, 180, 195)
        infoStatus.Text = "—"
        infoStatus.TextColor3 = Color3.fromRGB(120, 120, 135)
        return
    end

    local p = selectedPlayer
    infoName.Text = string.format("%s  (@%s)", p.DisplayName, p.Name)
    infoName.TextColor3 = Color3.new(1, 1, 1)

    local hum = getRealHumanoid(p)
    if not hum then
        infoStatus.Text = "not streamed / no character"
        infoStatus.TextColor3 = Color3.fromRGB(248, 180, 113)
        return
    end

    if hum.Health <= 0 then
        infoStatus.Text = "dead"
        infoStatus.TextColor3 = Color3.fromRGB(248, 113, 113)
    else
        infoStatus.Text = string.format("alive · %d/%d HP", math.floor(hum.Health), math.floor(hum.MaxHealth))
        infoStatus.TextColor3 = Color3.fromRGB(74, 222, 128)
    end
end

task.spawn(function()
    while _G.SPECTATE_ACTIVE do
        task.wait(0.25)
        if selectedPlayer then refreshInfo() end
    end
end)

local function renderRecents()
    for _, c in ipairs(recentsRow:GetChildren()) do
        if c:IsA("TextButton") or c:IsA("TextLabel") then c:Destroy() end
    end

    if #recents == 0 then
        local empty = Instance.new("TextLabel")
        empty.Size = UDim2.new(1, 0, 1, 0)
        empty.BackgroundTransparency = 1
        empty.Text = "no recent targets yet"
        empty.TextColor3 = Color3.fromRGB(90, 90, 105)
        empty.Font = Enum.Font.Gotham
        empty.TextSize = 11
        empty.TextXAlignment = Enum.TextXAlignment.Left
        empty.Parent = recentsRow
        return
    end

    for i, name in ipairs(recents) do
        local chip = Instance.new("TextButton")
        chip.Size = UDim2.new(0, 56, 1, 0)
        chip.BackgroundColor3 = Color3.fromRGB(30, 30, 42)
        chip.BorderSizePixel = 0
        chip.Text = name:sub(1, 7)
        chip.TextColor3 = Color3.fromRGB(200, 200, 215)
        chip.Font = Enum.Font.Gotham
        chip.TextSize = 11
        chip.LayoutOrder = i
        chip.Parent = recentsRow
        Instance.new("UICorner", chip).CornerRadius = UDim.new(0, 4)
        Instance.new("UIStroke", chip).Color = Color3.fromRGB(60, 60, 80)

        chip.MouseButton1Click:Connect(function()
            if not _G.SPECTATE_ACTIVE then return end
            local p = Players:FindFirstChild(name)
            if not p then
                setStatus(name .. " left the server", "err")
                for idx, n in ipairs(recents) do
                    if n == name then table.remove(recents, idx); break end
                end
                renderRecents()
                return
            end
            selectedPlayer = p
            dropdownBtn.Text = "-- " .. p.Name .. " --"
            dropdownBtn.TextColor3 = Color3.fromRGB(74, 222, 128)
            refreshInfo()
            setStatus("selected " .. p.Name, "ok")
        end)
    end
end

local function pushRecent(name)
    for idx, n in ipairs(recents) do
        if n == name then table.remove(recents, idx); break end
    end
    table.insert(recents, 1, name)
    while #recents > RECENTS_MAX do table.remove(recents) end
    renderRecents()
end

-- ============================================================
--  DROPDOWN
--  Each row shows the player name + a streamed marker.
--    · streamed     -> green text
--    · not streamed -> amber text
--  Green means you can spectate them right now.
-- ============================================================
local function refreshDropdown()
    for _, c in ipairs(dropdownList:GetChildren()) do
        if c:IsA("TextButton") or c:IsA("TextLabel") then c:Destroy() end
    end

    if selectedPlayer and not selectedPlayer.Parent then
        selectedPlayer = nil
        dropdownBtn.Text = "-- select a player --"
        dropdownBtn.TextColor3 = Color3.new(1, 1, 1)
        refreshInfo()
    end

    local count = 0
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= player then
            count = count + 1

            local isStreamed = (getRealHumanoid(p) ~= nil)

            local b = Instance.new("TextButton")
            b.Size = UDim2.new(1, -4, 0, 30)
            b.BackgroundColor3 = Color3.fromRGB(30, 30, 42)
            b.BorderSizePixel = 0
            b.Font = Enum.Font.Gotham
            b.TextSize = 13
            b.TextXAlignment = Enum.TextXAlignment.Left
            b.LayoutOrder = count
            b.Parent = dropdownList
            Instance.new("UICorner", b).CornerRadius = UDim.new(0, 5)

            if isStreamed then
                b.Text = "  " .. p.Name .. "   · streamed"
                b.TextColor3 = Color3.fromRGB(74, 222, 128)
            else
                b.Text = "  " .. p.Name .. "   · not streamed"
                b.TextColor3 = Color3.fromRGB(248, 180, 113)
            end

            b.MouseButton1Click:Connect(function()
                if not _G.SPECTATE_ACTIVE then return end
                selectedPlayer = p
                dropdownBtn.Text = "-- " .. p.Name .. " --"
                dropdownBtn.TextColor3 = Color3.fromRGB(74, 222, 128)
                dropdownList.Visible = false
                pushRecent(p.Name)
                refreshInfo()
            end)
        end
    end

    if count == 0 then
        local empty = Instance.new("TextLabel")
        empty.Size = UDim2.new(1, -4, 0, 30)
        empty.BackgroundTransparency = 1
        empty.Text = "  no other players in server"
        empty.TextColor3 = Color3.fromRGB(120, 120, 135)
        empty.Font = Enum.Font.Gotham
        empty.TextSize = 12
        empty.TextXAlignment = Enum.TextXAlignment.Left
        empty.Parent = dropdownList
    end

    dropdownList.CanvasSize = UDim2.new(0, 0, 0, math.max(count, 1) * 32)
    local visibleH = math.min(math.max(count, 1) * 32, 150)
    dropdownList.Size = UDim2.new(1, -20, 0, visibleH)
end

local function stopSpectate(reason)
    if not isSpectating then return end
    isSpectating = false
    spectatedPlayer = nil
    specBtn.Text = "SPECTATE"
    specBtn.BackgroundColor3 = Color3.fromRGB(140, 0, 160)
    specBtn.TextSize = 13
    specBtn.Size = UDim2.new(0.5, -15, 0, 40)
    tpBtn.Visible = true

    if not camera and workspace.CurrentCamera then
        camera = workspace.CurrentCamera
    end

    if camera then
        local char = player.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        pcall(function()
            camera.CameraType = Enum.CameraType.Custom
            if hum then
                camera.CameraSubject = hum
            else
                camera.CameraSubject = nil
            end
        end)
    end

    if reason then
        setStatus(reason, "info")
    end
end

-- ============================================================
--  INSULT DISPLAY
-- ============================================================
local insultGen = 0

local function showInsult()
    insultGen = insultGen + 1
    local myGen = insultGen

    local msg = INSULTS[math.random(1, #INSULTS)] or "your stupid is showing"

    tpBtn.Visible = false
    specBtn.Size = UDim2.new(1, -20, 0, 40)
    specBtn.TextWrapped = true
    specBtn.TextXAlignment = Enum.TextXAlignment.Center
    specBtn.TextYAlignment = Enum.TextYAlignment.Center

    local len = utf8.len(msg) or #msg
    local size = 13
    if len > 16 then size = 12 end
    if len > 24 then size = 11 end
    if len > 36 then size = 10 end
    if len > 60 then size = 9 end
    if len > 100 then size = 8 end

    specBtn.Text = msg
    specBtn.TextSize = size
    specBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)

    setStatus("that player isn't streamed in", "err")

    task.spawn(function()
        task.wait(3.9)
        if not _G.SPECTATE_ACTIVE then return end
        if myGen ~= insultGen then return end
        if isSpectating then return end
        tpBtn.Visible = true
        specBtn.Size = UDim2.new(0.5, -15, 0, 40)
        specBtn.Text = "SPECTATE"
        specBtn.TextSize = 13
        specBtn.BackgroundColor3 = Color3.fromRGB(140, 0, 160)
    end)
end

specBtn.MouseButton1Click:Connect(function()
    if not _G.SPECTATE_ACTIVE then return end
    if isSpectating then
        stopSpectate("stopped by you")
        return
    end

    if not selectedPlayer then
        setStatus("no player selected — open the dropdown first", "err")
        return
    end

    local target = selectedPlayer

    local hum = getRealHumanoid(target)
    if not hum then
        showInsult()
        return
    end

    specBtn.Text = "LOADING..."
    specBtn.TextSize = 13
    specBtn.BackgroundColor3 = Color3.fromRGB(180, 120, 0)
    setStatus("loading " .. target.Name .. "...", "info")

    task.spawn(function()
        local ok = forceCameraBind(hum)

        if not _G.SPECTATE_ACTIVE then return end
        if target ~= selectedPlayer then return end

        if ok then
            spectatedPlayer = target
            isSpectating = true
            specBtn.Text = "STOP SPECTATE"
            specBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
            specBtn.TextSize = 13
            setStatus("spectating " .. target.Name, "ok")
        else
            specBtn.Text = "SPECTATE"
            specBtn.BackgroundColor3 = Color3.fromRGB(140, 0, 160)
            setStatus("bind failed — retrying...", "info")
            task.wait(0.4)
            if forceCameraBind(hum) then
                spectatedPlayer = target
                isSpectating = true
                specBtn.Text = "STOP SPECTATE"
                specBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
                setStatus("spectating " .. target.Name, "ok")
            else
                setStatus("KAIZEN is blocking camera bind — try a different target", "err")
            end
        end
    end)
end)

tpBtn.MouseButton1Click:Connect(function()
    if not _G.SPECTATE_ACTIVE then return end
    if not selectedPlayer then
        setStatus("no player selected — open the dropdown first", "err")
        return
    end

    local now = tick()
    if now - lastTeleport < TELEPORT_COOLDOWN then return end
    lastTeleport = now

    local myChar = player.Character
    local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
    if not myHRP then
        setStatus("you have no character to teleport", "err")
        return
    end

    local targetHRP = selectedPlayer.Character and selectedPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not targetHRP then
        setStatus(selectedPlayer.Name .. " has no character right now", "err")
        return
    end

    local dest = targetHRP.Position + Vector3.new(0, 3, 0)

    local ok = pcall(function()
        myHRP.CFrame = CFrame.new(dest)
    end)

    if ok then
        setStatus("teleported to " .. selectedPlayer.Name, "ok")
    else
        setStatus("teleport failed — try again", "err")
    end
end)

local function setPanelVisible(v)
    frame.Visible = v
    if v then
        toggleBtn.Text = "✕  CLOSE"
        toggleBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
        toggleStroke.Color = Color3.fromRGB(240, 80, 80)
    else
        toggleBtn.Text = "👁  SPECTATE"
        toggleBtn.BackgroundColor3 = Color3.fromRGB(140, 0, 160)
        toggleStroke.Color = Color3.fromRGB(200, 60, 220)
    end
end

toggleBtn.MouseButton1Click:Connect(function()
    if not _G.SPECTATE_ACTIVE then return end
    setPanelVisible(not frame.Visible)
end)

closeBtn.MouseButton1Click:Connect(function()
    if not _G.SPECTATE_ACTIVE then return end
    setPanelVisible(false)
end)

refreshBtn.MouseButton1Click:Connect(function()
    if not _G.SPECTATE_ACTIVE then return end
    refreshDropdown()
    refreshInfo()
    setStatus("refreshed (" .. tostring(#Players:GetPlayers() - 1) .. " others)", "ok")
    dropdownList.Visible = true
end)

dropdownBtn.MouseButton1Click:Connect(function()
    if not _G.SPECTATE_ACTIVE then return end
    dropdownList.Visible = not dropdownList.Visible
    if dropdownList.Visible then refreshDropdown() end
end)

local softFail = 0
local lastHardRebind = 0

local function pinCamera()
    if not _G.SPECTATE_ACTIVE then return end
    if not isSpectating then return end
    if rebinding then return end

    if not spectatedPlayer or not spectatedPlayer.Parent then
        stopSpectate("target lost")
        return
    end

    if workspace.CurrentCamera and workspace.CurrentCamera ~= camera then
        camera = workspace.CurrentCamera
    end
    if not camera then return end

    local hum = getRealHumanoid(spectatedPlayer)
    if not hum then
        requestStream(spectatedPlayer)
        return
    end

    local subject = nil
    if hum.Health > 0 then
        subject = hum
    else
        local c = spectatedPlayer.Character
        subject = c and c:FindFirstChild("HumanoidRootPart") or hum
    end
    if not subject then return end

    if camera.CameraType ~= Enum.CameraType.Custom then
        pcall(function() camera.CameraType = Enum.CameraType.Custom end)
    end

    if camera.CameraSubject ~= subject then
        pcall(function() camera.CameraSubject = subject end)
        softFail = softFail + 1
    else
        softFail = 0
    end

    if softFail >= 15 and (tick() - lastHardRebind) > 2 then
        lastHardRebind = tick()
        softFail = 0
        task.spawn(function()
            if not _G.SPECTATE_ACTIVE or not isSpectating then return end
            local h = getRealHumanoid(spectatedPlayer)
            if h then
                forceCameraBind(h)
            end
        end)
    end
end

pcall(function()
    RunService:UnbindFromRenderStep("PL_SpectateCamera")
end)
RunService:BindToRenderStep("PL_SpectateCamera", Enum.RenderPriority.Last.Value, pinCamera)

bind(RunService.Heartbeat:Connect(function()
    if not _G.SPECTATE_ACTIVE then return end
    if not isSpectating then return end
    pinCamera()
end))

bind(Players.PlayerRemoving:Connect(function(p)
    if not _G.SPECTATE_ACTIVE then return end
    if selectedPlayer == p then
        selectedPlayer = nil
        dropdownBtn.Text = "-- select a player --"
        dropdownBtn.TextColor3 = Color3.new(1, 1, 1)
        refreshInfo()
    end
    if p == spectatedPlayer then
        stopSpectate("target left the server")
    end
    if dropdownList.Visible then refreshDropdown() end

    local dirty = false
    for idx, n in ipairs(recents) do
        if n == p.Name then table.remove(recents, idx); dirty = true; break end
    end
    if dirty then renderRecents() end
end))

bind(Players.PlayerAdded:Connect(function()
    if not _G.SPECTATE_ACTIVE then return end
    if dropdownList.Visible then refreshDropdown() end
end))

local function watchTarget(p)
    bind(p.CharacterAdded:Connect(function(c)
        if not _G.SPECTATE_ACTIVE then return end
        if p == selectedPlayer then refreshInfo() end
        if p ~= spectatedPlayer then return end
        task.wait(0.5)
        if not _G.SPECTATE_ACTIVE then return end
        if p ~= spectatedPlayer or not isSpectating then return end
        local hum = getRealHumanoid(p)
        if hum then
            forceCameraBind(hum)
        end
    end))
    bind(p.CharacterRemoving:Connect(function()
        if not _G.SPECTATE_ACTIVE then return end
        if p == selectedPlayer then refreshInfo() end
    end))
end
for _, p in ipairs(Players:GetPlayers()) do watchTarget(p) end
bind(Players.PlayerAdded:Connect(watchTarget))

bind(player.CharacterAdded:Connect(function()
    if not _G.SPECTATE_ACTIVE then return end
    if isSpectating then
        task.wait(0.5)
        stopSpectate("you respawned")
    end
end))

local function unload()
    if not _G.SPECTATE_ACTIVE then return end
    _G.SPECTATE_ACTIVE = false

    pcall(function()
        if not camera and workspace.CurrentCamera then
            camera = workspace.CurrentCamera
        end
        if camera then
            camera.CameraType = Enum.CameraType.Custom
            local char = player.Character
            local hum = char and char:FindFirstChildOfClass("Humanoid")
            if hum then
                camera.CameraSubject = hum
            end
        end
    end)

    pcall(function()
        RunService:UnbindFromRenderStep("PL_SpectateCamera")
    end)
    for _, c in ipairs(Connections) do
        pcall(function() c:Disconnect() end)
    end
    Connections = {}

    pcall(function()
        if screenGui and screenGui.Parent then
            screenGui:Destroy()
        end
    end)

    _G.SPECTATE_ACTIVE = nil
    _G.SPECTATE_UNLOAD = nil
end

_G.SPECTATE_UNLOAD = unload

unloadBtn.MouseButton1Click:Connect(function()
    unload()
end)

renderRecents()
refreshDropdown()
refreshInfo()
