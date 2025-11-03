-- PSF Remake 4.0 Ultimate - Complete Persistent Version
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local HttpService = game:GetService("HttpService")
local UserInputService = game:GetService("UserInputService")
local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

-- ===== ScreenGui =====
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "PSFRemakeUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = PlayerGui

-- ===== Main Frame =====
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0.6,0,0.35,0)
mainFrame.Position = UDim2.new(0.5,0,0.5,0)
mainFrame.AnchorPoint = Vector2.new(0.5,0.5)
mainFrame.BackgroundColor3 = Color3.fromRGB(30,30,30)
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = screenGui
Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0,12)

-- ===== Title =====
local title = Instance.new("TextLabel", mainFrame)
title.Size = UDim2.new(1,-140,0,28)
title.Position = UDim2.new(0,8,0,0)
title.BackgroundTransparency = 1
title.Text = "PSF Remake 4.0"
title.TextColor3 = Color3.fromRGB(255,255,255)
title.TextXAlignment = Enum.TextXAlignment.Left
title.Font = Enum.Font.Gotham
title.TextSize = 18

-- ===== Theme & Collapse Buttons =====
local themeBtn = Instance.new("TextButton", mainFrame)
themeBtn.Size = UDim2.new(0,80,0,24)
themeBtn.Position = UDim2.new(1,-120,0,2)
themeBtn.Text = "Light"
themeBtn.Font = Enum.Font.Gotham
themeBtn.TextSize = 16
themeBtn.BackgroundColor3 = Color3.fromRGB(60,60,60)
themeBtn.TextColor3 = Color3.fromRGB(255,255,255)
themeBtn.AutoButtonColor = true

local collapseBtn = Instance.new("TextButton", mainFrame)
collapseBtn.Size = UDim2.new(0,24,0,24)
collapseBtn.Position = UDim2.new(1,-32,0,2)
collapseBtn.Text = "▾"
collapseBtn.Font = Enum.Font.Gotham
collapseBtn.TextSize = 18
collapseBtn.BackgroundColor3 = Color3.fromRGB(60,60,60)
collapseBtn.AutoButtonColor = true
Instance.new("UICorner", collapseBtn).CornerRadius = UDim.new(0,4)

-- ===== Content Frame =====
local contentFrame = Instance.new("Frame", mainFrame)
contentFrame.Size = UDim2.new(1,-16,1,-36)
contentFrame.Position = UDim2.new(0,8,0,36)
contentFrame.BackgroundTransparency = 1

-- ===== Left Frame (Script List) =====
local leftFrame = Instance.new("Frame", contentFrame)
leftFrame.Size = UDim2.new(0.4,0,1,0)
leftFrame.Position = UDim2.new(0,0,0,0)
leftFrame.BackgroundTransparency = 1
local listLayout = Instance.new("UIListLayout", leftFrame)
listLayout.Padding = UDim.new(0,6)
local addBtn = Instance.new("TextButton", leftFrame)
addBtn.Size = UDim2.new(1,0,0,28)
addBtn.Text = "+ Add Script"
addBtn.Font = Enum.Font.Gotham
addBtn.TextSize = 16
addBtn.BackgroundColor3 = Color3.fromRGB(0,170,255)
addBtn.TextColor3 = Color3.fromRGB(255,255,255)
addBtn.AutoButtonColor = true
local delBtn = Instance.new("TextButton", leftFrame)
delBtn.Size = UDim2.new(1,0,0,28)
delBtn.Text = "- Delete Script"
delBtn.Font = Enum.Font.Gotham
delBtn.TextSize = 16
delBtn.BackgroundColor3 = Color3.fromRGB(255,50,50)
delBtn.TextColor3 = Color3.fromRGB(255,255,255)
delBtn.AutoButtonColor = true

-- ===== Right Frame (Editor + Console) =====
local rightFrame = Instance.new("Frame", contentFrame)
rightFrame.Size = UDim2.new(0.6,0,1,0)
rightFrame.Position = UDim2.new(0.4,10,0,0)
rightFrame.BackgroundTransparency = 1

-- Editor
local editorInput = Instance.new("TextBox", rightFrame)
editorInput.Size = UDim2.new(1,0,0.55,0)
editorInput.Position = UDim2.new(0,0,0,0)
editorInput.BackgroundColor3 = Color3.fromRGB(11,12,14)
editorInput.TextColor3 = Color3.fromRGB(255,255,255)
editorInput.Font = Enum.Font.Code
editorInput.TextSize = 16
editorInput.ClearTextOnFocus = false
editorInput.TextXAlignment = Enum.TextXAlignment.Left
editorInput.TextYAlignment = Enum.TextYAlignment.Top
editorInput.MultiLine = true
editorInput.Text = "-- Выберите скрипт или создайте новый\n"

-- Resizer Frame
local resizer = Instance.new("Frame", rightFrame)
resizer.Size = UDim2.new(1,0,0,6)
resizer.Position = UDim2.new(0,0,0.55,0)
resizer.BackgroundColor3 = Color3.fromRGB(80,80,80)
resizer.Active = true
resizer.Draggable = true
resizer.Cursor = "SizeUpDown"

-- Console
local console = Instance.new("TextLabel", rightFrame)
console.Size = UDim2.new(1,0,0.45,0)
console.Position = UDim2.new(0,0,0.55,6)
console.BackgroundColor3 = Color3.fromRGB(6,6,6)
console.TextColor3 = Color3.fromRGB(200,200,200)
console.Font = Enum.Font.Gotham
console.TextSize = 14
console.TextXAlignment = Enum.TextXAlignment.Left
console.TextYAlignment = Enum.TextYAlignment.Top
console.TextWrapped = true
console.Text = "Console:\n"

-- Run Button
local runBtn = Instance.new("TextButton", rightFrame)
runBtn.Size = UDim2.new(0,80,0,32)
runBtn.Position = UDim2.new(1,-88,1,-36)
runBtn.Text = "Run"
runBtn.Font = Enum.Font.Gotham
runBtn.TextSize = 16
runBtn.BackgroundColor3 = Color3.fromRGB(12,118,255)
runBtn.AutoButtonColor = true

-- ===== Script Storage =====
local selectedScriptIndex = 1
local demoScripts = {}
if player:FindFirstChild("PSFRemakeData") then
    local stored = player.PSFRemakeData:FindFirstChild("Scripts")
    if stored then
        local ok,tbl = pcall(function() return HttpService:JSONDecode(stored.Value) end)
        if ok and type(tbl)=="table" then demoScripts = tbl end
    end
end
if #demoScripts==0 then
    demoScripts = {
        {name="Hello", code="print('Hello from PSF')"},
        {name="Timer", code="for i=1,5 do print('tick', i) wait(1) end"}
    }
end
if not player:FindFirstChild("PSFRemakeData") then
    local dataFolder = Instance.new("Folder", player)
    dataFolder.Name = "PSFRemakeData"
    local scriptsValue = Instance.new("StringValue", dataFolder)
    scriptsValue.Name = "Scripts"
    scriptsValue.Value = HttpService:JSONEncode(demoScripts)
end
local scriptsValue = player.PSFRemakeData.Scripts

-- ===== Settings Storage =====
if not player.PSFRemakeData:FindFirstChild("Settings") then
    local settingsValue = Instance.new("StringValue", player.PSFRemakeData)
    settingsValue.Name = "Settings"
    settingsValue.Value = "{}"
end
local settingsValue = player.PSFRemakeData.Settings
local settings = {}
pcall(function() settings = HttpService:JSONDecode(settingsValue.Value) end)

-- Apply saved settings
if settings.mainFramePos then
    mainFrame.Position = UDim2.new(settings.mainFramePos.X.Scale, settings.mainFramePos.X.Offset, settings.mainFramePos.Y.Scale, settings.mainFramePos.Y.Offset)
end
if
-- Apply saved settings
if settings.editorSize then
    editorInput.Size = UDim2.new(settings.editorSize.X.Scale, settings.editorSize.X.Offset, settings.editorSize.Y.Scale, settings.editorSize.Y.Offset)
    resizer.Position = UDim2.new(settings.editorSize.X.Scale, settings.editorSize.X.Offset, 0, settings.editorSize.Y.Offset)
    console.Position = UDim2.new(0,0,0,settings.editorSize.Y.Offset+6)
end

local darkTheme = settings.darkTheme ~= nil and settings.darkTheme or true
themeBtn.Text = darkTheme and "Light" or "Dark"

if settings.selectedScriptIndex then
    selectedScriptIndex = settings.selectedScriptIndex
end

-- ===== Save Settings Function =====
local function saveSettings()
    settings.mainFramePos = {X = {Scale=mainFrame.Position.X.Scale, Offset=mainFrame.Position.X.Offset}, Y = {Scale=mainFrame.Position.Y.Scale, Offset=mainFrame.Position.Y.Offset}}
    settings.editorSize = {X = {Scale=editorInput.Size.X.Scale, Offset=editorInput.Size.X.Offset}, Y = {Scale=editorInput.Size.Y.Scale, Offset=editorInput.Size.Y.Offset}}
    settings.darkTheme = darkTheme
    settings.selectedScriptIndex = selectedScriptIndex
    settingsValue.Value = HttpService:JSONEncode(settings)
end

-- ===== GUI Helper Functions =====
local function safePrint(...)
    local args={...}
    local str=""
    for i,v in ipairs(args) do str=str..tostring(v).."\t" end
    console.Text = console.Text .. str.."\n"
end

local function saveScripts()
    scriptsValue.Value = HttpService:JSONEncode(demoScripts)
    saveSettings()
end

local function rebuildList()
    for _,child in pairs(leftFrame:GetChildren()) do
        if child:IsA("TextButton") and child ~= addBtn and child ~= delBtn then child:Destroy() end
    end
    for i,s in ipairs(demoScripts) do
        local btn = Instance.new("TextButton", leftFrame)
        btn.Size = UDim2.new(1,0,0,28)
        btn.Text = s.name
        btn.Font = Enum.Font.Gotham
        btn.TextSize = editorInput.TextSize
        btn.BackgroundColor3 = Color3.fromRGB(24,24,24)
        btn.TextColor3 = Color3.fromRGB(255,255,255)
        btn.AutoButtonColor = true
        btn.MouseButton1Click:Connect(function()
            selectedScriptIndex = i
            editorInput.Text = s.code
            saveSettings()
        end)
    end
end

rebuildList()

-- ===== Run Button Logic =====
runBtn.MouseButton1Click:Connect(function()
    if demoScripts[selectedScriptIndex] then
        demoScripts[selectedScriptIndex].code = editorInput.Text
        saveScripts()
    end
    safePrint("Running script: "..demoScripts[selectedScriptIndex].name)
    local ok,f = pcall(loadstring,demoScripts[selectedScriptIndex].code)
    if ok and type(f)=="function" then
        local success,err = pcall(f)
        if not success then safePrint("Error: "..err) end
    else
        safePrint("Compile error.")
    end
end)

-- ===== Add/Delete Buttons =====
addBtn.MouseButton1Click:Connect(function()
    table.insert(demoScripts, {name="New Script "..#demoScripts+1, code="-- New script"})
    selectedScriptIndex = #demoScripts
    editorInput.Text = demoScripts[selectedScriptIndex].code
    rebuildList()
    saveScripts()
end)

delBtn.MouseButton1Click:Connect(function()
    if demoScripts[selectedScriptIndex] then
        table.remove(demoScripts, selectedScriptIndex)
        selectedScriptIndex = math.max(1, selectedScriptIndex-1)
        editorInput.Text = demoScripts[selectedScriptIndex] and demoScripts[selectedScriptIndex].code or ""
        rebuildList()
        saveScripts()
    end
end)

-- ===== Collapse Button Logic =====
collapseBtn.MouseButton1Click:Connect(function()
    if contentFrame.Visible then
        contentFrame.Visible = false
        collapseBtn.Text = "▸"
        mainFrame.Size = UDim2.new(0.2,0,0.05,0)
    else
        contentFrame.Visible = true
        collapseBtn.Text = "▾"
        mainFrame.Size = UDim2.new(0.6,0,0.35,0)
    end
    saveSettings()
end)

-- ===== Theme Toggle =====
themeBtn.MouseButton1Click:Connect(function()
    darkTheme = not darkTheme
    if darkTheme then
        mainFrame.BackgroundColor3 = Color3.fromRGB(30,30,30)
        editorInput.BackgroundColor3 = Color3.fromRGB(11,12,14)
        editorInput.TextColor3 = Color3.fromRGB(255,255,255)
        console.BackgroundColor3 = Color3.fromRGB(6,6,6)
        console.TextColor3 = Color3.fromRGB(200,200,200)
        themeBtn.Text = "Light"
    else
        mainFrame.BackgroundColor3 = Color3.fromRGB(240,240,240)
        editorInput.BackgroundColor3 = Color3.fromRGB(255,255,255)
        editorInput.TextColor3 = Color3.fromRGB(0,0,0)
        console.BackgroundColor3 = Color3.fromRGB(230,230,230)
        console.TextColor3 = Color3.fromRGB(0,0,0)
        themeBtn.Text = "Dark"
    end
    saveSettings()
end)

-- ===== Resizer Logic =====
local resizing = false
resizer.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then resizing = true end
end)

resizer.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then resizing = false end
end)

RunService.RenderStepped:Connect(function()
    if resizing then
        local mousePos = UserInputService:GetMouseLocation()
        local topY = rightFrame.AbsolutePosition.Y
        local newHeight = math.clamp(mousePos.Y - topY, 50, rightFrame.AbsoluteSize.Y - 50)
        editorInput.Size = UDim2.new(1,0,0,newHeight)
        resizer.Position = UDim2.new(0,0,0,newHeight)
        console.Position = UDim2.new(0,0,0,newHeight+6)
        console.Size = UDim2.new(1,0,0,rightFrame.AbsoluteSize.Y-newHeight-6)
        saveSettings()
    end
end)

-- ===== Adaptive Scaling =====
RunService.RenderStepped:Connect(function()
    local screenSize = workspace.CurrentCamera.ViewportSize
    mainFrame.Position = UDim2.new(0.5,0,0.5,0)
    if screenSize.X < 500 then
        mainFrame.Size = UDim2.new(0.9,0,0.4,0)
        editorInput.TextSize = 12
        console.TextSize = 12
    elseif screenSize.X < 800 then
        mainFrame.Size = UDim2.new(0.7,0,0.35,0)
        editorInput.TextSize = 14
        console.TextSize = 14
    else
        mainFrame.Size = UDim2.new(0.6,0,0.35,0)
        editorInput.TextSize = 16
        console.TextSize = 14
    end
end)

-- ===== Periodic Auto-Save =====
RunService.Heartbeat:Connect(function()
    saveSettings()
end)

-- ===== Initial Theme Apply =====
themeBtn.MouseButton1Click:Fire()
