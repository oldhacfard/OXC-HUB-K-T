# OXC-HUB-K-T



code [PLEASE DONT STEAL OR COPY İT JUDT MOD İT]
code
{
--=====================================================================
--// OXC HUB - CLEAN TEMPLATE
--// Developed by oldhacfard
--=====================================================================

--// Services
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local StatsService = game:GetService("Stats")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local StarterGui = game:GetService("StarterGui")
local SoundService = game:GetService("SoundService")

local LocalPlayer = Players.LocalPlayer

--// GITHUB ICON DOWNLOADER
local function DownloadIcon(fileName, url, fallback)
	local path = nil
	pcall(function()
		if writefile and isfile and getcustomasset and game.HttpGet then
			if not isfile(fileName) then
				local ok, data = pcall(function()
					return game:HttpGet(url)
				end)
				if ok and data and #data > 0 then
					writefile(fileName, data)
				end
			end
			if isfile(fileName) then
				path = getcustomasset(fileName)
			end
		end
	end)
	if not path then path = fallback end
	return path
end

local SettingsIconPath = DownloadIcon(
	"oxc_settings_icon.png",
	"https://raw.githubusercontent.com/oldhacfard/010292910192ps-q11101000001011999881010-s-1111/main/44941-removebg-preview.png",
	"rbxassetid://118552205603493"
)

local HideIconPath = DownloadIcon(
	"oxc_hide_icon.png",
	"https://raw.githubusercontent.com/oldhacfard/010292910192ps-q11101000001011999881010-s-1111/main/44944-removebg-preview.png",
	"rbxassetid://126852701384321"
)

local UILogoPath = DownloadIcon(
	"oxc_ui_logo.jpg",
	"https://raw.githubusercontent.com/oldhacfard/010292910192ps-q11101000001011999881010-s-1111/main/Picsart_26-10-04_17-30-00-622.jpg",
	"rbxassetid://118552205603493"
)

--// BLUR
local Blur = Instance.new("BlurEffect")
Blur.Size = 0
Blur.Parent = Lighting

TweenService:Create(Blur, TweenInfo.new(0.9, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Size = 20}):Play()

--// SCREENGUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "OXCHub"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.IgnoreGuiInset = true

if gethui then
	ScreenGui.Parent = gethui()
elseif syn and syn.protect_gui then
	syn.protect_gui(ScreenGui)
	ScreenGui.Parent = game:GetService("CoreGui")
else
	ScreenGui.Parent = game:GetService("CoreGui")
end

--// NOTIFICATION
local NotifSound = Instance.new("Sound")
NotifSound.SoundId = "rbxassetid://98797174600699"
NotifSound.Volume = 0.6
NotifSound.Parent = SoundService

local function PlayNotifSound()
	local s = NotifSound:Clone()
	s.Parent = SoundService
	s:Play()
	s.Ended:Connect(function() s:Destroy() end)
end

local function SendNotif(title, text)
	pcall(function()
		StarterGui:SetCore("SendNotification", {
			Title = title, Text = text, Duration = 5, Icon = SettingsIconPath
		})
	end)
	PlayNotifSound()
end

task.spawn(function()
	task.wait(1)
	SendNotif("Thanks for using OXC hub", "by oldhacfard")
end)

--// MAIN FRAME
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 480, 0, 360)
MainFrame.Position = UDim2.new(0.5, -240, 0.5, -180)
MainFrame.BackgroundTransparency = 1
MainFrame.Active = true
MainFrame.Parent = ScreenGui

--// DRAG
do
	local dragging, dragStart, startPos
	MainFrame.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then
			local mouse = UserInputService:GetMouseLocation()
			if mouse.Y - MainFrame.AbsolutePosition.Y <= 44 then
				dragging = true
				dragStart = input.Position
				startPos = MainFrame.Position
			end
		end
	end)
	UserInputService.InputChanged:Connect(function(input)
		if not dragging then return end
		if input.UserInputType ~= Enum.UserInputType.MouseMovement
		and input.UserInputType ~= Enum.UserInputType.Touch then return end
		local delta = input.Position - dragStart
		MainFrame.Position = UDim2.new(
			startPos.X.Scale, startPos.X.Offset + delta.X,
			startPos.Y.Scale, startPos.Y.Offset + delta.Y
		)
	end)
	UserInputService.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then
			dragging = false
		end
	end)
end

--// HELPERS
local function FadeOutAll(root, duration)
	duration = duration or 0.45
	local info = TweenInfo.new(duration, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
	for _, v in ipairs(root:GetDescendants()) do
		if v:IsA("GuiObject") then
			if v.BackgroundTransparency < 1 then
				TweenService:Create(v, info, {BackgroundTransparency = 1}):Play()
			end
			if v:IsA("TextLabel") or v:IsA("TextButton") then
				TweenService:Create(v, info, {TextTransparency = 1}):Play()
			end
			if v:IsA("ImageLabel") or v:IsA("ImageButton") then
				TweenService:Create(v, info, {ImageTransparency = 1}):Play()
			end
		elseif v:IsA("UIStroke") then
			TweenService:Create(v, info, {Transparency = 1}):Play()
		end
	end
end

local function CreateGlassPanel(name, size, position, parent)
	local Panel = Instance.new("Frame")
	Panel.Name = name
	Panel.Size = size
	Panel.Position = position
	Panel.BackgroundColor3 = Color3.fromRGB(15, 15, 22)
	Panel.BackgroundTransparency = 0.25
	Panel.BorderSizePixel = 0
	Panel.Parent = parent

	local Corner = Instance.new("UICorner")
	Corner.CornerRadius = UDim.new(0, 12)
	Corner.Parent = Panel

	local Stroke = Instance.new("UIStroke")
	Stroke.Color = Color3.fromRGB(255, 255, 255)
	Stroke.Transparency = 0.88
	Stroke.Thickness = 1.2
	Stroke.Parent = Panel

	local Gradient = Instance.new("UIGradient")
	Gradient.Color = ColorSequence.new({
		ColorSequenceKeypoint.new(0, Color3.fromRGB(30, 30, 45)),
		ColorSequenceKeypoint.new(1, Color3.fromRGB(12, 12, 18))
	})
	Gradient.Rotation = 45
	Gradient.Parent = Panel

	return Panel
end

--// TOP BAR
local TopBar = CreateGlassPanel("TopBar", UDim2.new(1, 0, 0, 42), UDim2.new(0, 0, 0, 0), MainFrame)

-- UI Logo
local UILogo = Instance.new("ImageLabel")
UILogo.Name = "UILogo"
UILogo.Size = UDim2.new(0, 26, 0, 26)
UILogo.Position = UDim2.new(0, 10, 0.5, -13)
UILogo.BackgroundTransparency = 1
UILogo.Image = UILogoPath
UILogo.ScaleType = Enum.ScaleType.Fit
UILogo.Parent = TopBar

local UILogoCorner = Instance.new("UICorner")
UILogoCorner.CornerRadius = UDim.new(0, 6)
UILogoCorner.Parent = UILogo

local TitleLabel = Instance.new("TextLabel")
TitleLabel.Size = UDim2.new(0, 220, 1, 0)
TitleLabel.Position = UDim2.new(0, 44, 0, 0)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Text = "OXC HUB"
TitleLabel.Font = Enum.Font.Michroma
TitleLabel.TextSize = 14
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
TitleLabel.Parent = TopBar

local function CreateIconBtn(name, posOffset, iconPath)
	local Btn = Instance.new("TextButton")
	Btn.Name = name
	Btn.Size = UDim2.new(0, 32, 0, 32)
	Btn.Position = UDim2.new(1, posOffset, 0.5, -16)
	Btn.BackgroundTransparency = 1
	Btn.Text = ""
	Btn.AutoButtonColor = false
	Btn.Parent = TopBar

	local Icon = Instance.new("ImageLabel")
	Icon.Size = UDim2.new(0, 28, 0, 28)
	Icon.Position = UDim2.new(0.5, -14, 0.5, -14)
	Icon.BackgroundTransparency = 1
	Icon.Image = iconPath
	Icon.ImageColor3 = Color3.fromRGB(255, 255, 255)
	Icon.ImageTransparency = 0.15
	Icon.Parent = Btn

	return Btn, Icon
end

local HideBtn, HideIcon = CreateIconBtn("HideBtn", -118, HideIconPath)
local SettingsBtn, SettingsIcon = CreateIconBtn("SettingsBtn", -78, SettingsIconPath)
local CloseBtn, CloseIcon = CreateIconBtn("CloseBtn", -38, "rbxassetid://126290678159164")

local function HookIconHover(btn, icon, hoverColor)
	btn.MouseEnter:Connect(function()
		TweenService:Create(icon, TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
			{ImageColor3 = hoverColor, ImageTransparency = 0,
			 Size = UDim2.new(0, 32, 0, 32), Position = UDim2.new(0.5, -16, 0.5, -16)}):Play()
	end)
	btn.MouseLeave:Connect(function()
		TweenService:Create(icon, TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
			{ImageColor3 = Color3.fromRGB(255, 255, 255), ImageTransparency = 0.15,
			 Size = UDim2.new(0, 28, 0, 28), Position = UDim2.new(0.5, -14, 0.5, -14)}):Play()
	end)
end

HookIconHover(HideBtn, HideIcon, Color3.fromRGB(255, 200, 0))
HookIconHover(SettingsBtn, SettingsIcon, Color3.fromRGB(0, 220, 255))
HookIconHover(CloseBtn, CloseIcon, Color3.fromRGB(255, 60, 60))

-- UI Logo hover
UILogo.MouseEnter:Connect(function()
	TweenService:Create(UILogo, TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
		{Size = UDim2.new(0, 30, 0, 30), Position = UDim2.new(0, 8, 0.5, -15)}):Play()
end)
UILogo.MouseLeave:Connect(function()
	TweenService:Create(UILogo, TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
		{Size = UDim2.new(0, 26, 0, 26), Position = UDim2.new(0, 10, 0.5, -13)}):Play()
end)

--// MINIMIZE / REOPEN
local isMinimized = false

local ReopenBtn = Instance.new("TextButton")
ReopenBtn.Name = "ReopenBtn"
ReopenBtn.Size = UDim2.new(0, 46, 0, 46)
ReopenBtn.Position = UDim2.new(0, 20, 0.5, -23)
ReopenBtn.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
ReopenBtn.BackgroundTransparency = 0.15
ReopenBtn.Text = ""
ReopenBtn.AutoButtonColor = false
ReopenBtn.Visible = false
ReopenBtn.Parent = ScreenGui

local ReopenCorner = Instance.new("UICorner")
ReopenCorner.CornerRadius = UDim.new(1, 0)
ReopenCorner.Parent = ReopenBtn

local ReopenStroke = Instance.new("UIStroke")
ReopenStroke.Color = Color3.fromRGB(0, 230, 255)
ReopenStroke.Transparency = 0.4
ReopenStroke.Thickness = 1.5
ReopenStroke.Parent = ReopenBtn

local ReopenGradient = Instance.new("UIGradient")
ReopenGradient.Color = ColorSequence.new({
	ColorSequenceKeypoint.new(0, Color3.fromRGB(40, 40, 60)),
	ColorSequenceKeypoint.new(1, Color3.fromRGB(10, 10, 18))
})
ReopenGradient.Rotation = 45
ReopenGradient.Parent = ReopenBtn

local ReopenIcon = Instance.new("ImageLabel")
ReopenIcon.Size = UDim2.new(0, 28, 0, 28)
ReopenIcon.Position = UDim2.new(0.5, -14, 0.5, -14)
ReopenIcon.BackgroundTransparency = 1
ReopenIcon.Image = HideIconPath
ReopenIcon.ImageColor3 = Color3.fromRGB(0, 230, 255)
ReopenIcon.Parent = ReopenBtn

ReopenBtn.MouseEnter:Connect(function()
	TweenService:Create(ReopenBtn, TweenInfo.new(0.25),
		{BackgroundColor3 = Color3.fromRGB(40, 40, 60), BackgroundTransparency = 0.05}):Play()
	TweenService:Create(ReopenStroke, TweenInfo.new(0.25), {Transparency = 0.1}):Play()
	TweenService:Create(ReopenIcon, TweenInfo.new(0.25),
		{ImageColor3 = Color3.fromRGB(120, 255, 255), Size = UDim2.new(0, 32, 0, 32),
		 Position = UDim2.new(0.5, -16, 0.5, -16)}):Play()
end)
ReopenBtn.MouseLeave:Connect(function()
	TweenService:Create(ReopenBtn, TweenInfo.new(0.25),
		{BackgroundColor3 = Color3.fromRGB(20, 20, 30), BackgroundTransparency = 0.15}):Play()
	TweenService:Create(ReopenStroke, TweenInfo.new(0.25), {Transparency = 0.4}):Play()
	TweenService:Create(ReopenIcon, TweenInfo.new(0.25),
		{ImageColor3 = Color3.fromRGB(0, 230, 255), Size = UDim2.new(0, 28, 0, 28),
		 Position = UDim2.new(0.5, -14, 0.5, -14)}):Play()
end)

do
	local dragging, dragStart, startPos
	ReopenBtn.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true
			dragStart = input.Position
			startPos = ReopenBtn.Position
		end
	end)
	UserInputService.InputChanged:Connect(function(input)
		if not dragging then return end
		if input.UserInputType ~= Enum.UserInputType.MouseMovement
		and input.UserInputType ~= Enum.UserInputType.Touch then return end
		local delta = input.Position - dragStart
		ReopenBtn.Position = UDim2.new(
			startPos.X.Scale, startPos.X.Offset + delta.X,
			startPos.Y.Scale, startPos.Y.Offset + delta.Y
		)
	end)
	UserInputService.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then
			dragging = false
		end
	end)
end

local function Minimize()
	if isMinimized then return end
	isMinimized = true
	TweenService:Create(Blur, TweenInfo.new(0.9, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Size = 0}):Play()
	TweenService:Create(MainFrame, TweenInfo.new(0.35, Enum.EasingStyle.Back, Enum.EasingDirection.In),
		{Size = UDim2.new(0, 40, 0, 40), Position = UDim2.new(0, 20, 0.5, -23)}):Play()
	task.wait(0.36)
	MainFrame.Visible = false
	MainFrame.Size = UDim2.new(0, 480, 0, 360)
	MainFrame.Position = UDim2.new(0.5, -240, 0.5, -180)
	ReopenBtn.Size = UDim2.new(0, 0, 0, 0)
	ReopenBtn.Position = UDim2.new(0, 43, 0.5, -23)
	ReopenBtn.Visible = true
	TweenService:Create(ReopenBtn, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.Out),
		{Size = UDim2.new(0, 46, 0, 46), Position = UDim2.new(0, 20, 0.5, -23)}):Play()
end

local function Restore()
	if not isMinimized then return end
	isMinimized = false
	TweenService:Create(ReopenBtn, TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.In),
		{Size = UDim2.new(0, 0, 0, 0), Position = UDim2.new(0, 43, 0.5, -23)}):Play()
	task.wait(0.25)
	ReopenBtn.Visible = false
	ReopenBtn.Size = UDim2.new(0, 46, 0, 46)
	ReopenBtn.Position = UDim2.new(0, 20, 0.5, -23)
	TweenService:Create(Blur, TweenInfo.new(0.9, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Size = 20}):Play()
	MainFrame.Visible = true
	MainFrame.Size = UDim2.new(0, 40, 0, 40)
	MainFrame.Position = UDim2.new(0, 20, 0.5, -23)
	TweenService:Create(MainFrame, TweenInfo.new(0.45, Enum.EasingStyle.Back, Enum.EasingDirection.Out),
		{Size = UDim2.new(0, 480, 0, 360), Position = UDim2.new(0.5, -240, 0.5, -180)}):Play()
end

HideBtn.MouseButton1Click:Connect(Minimize)
ReopenBtn.MouseButton1Click:Connect(Restore)

UserInputService.InputBegan:Connect(function(input, gpe)
	if gpe then return end
	if input.KeyCode == Enum.KeyCode.RightShift then
		if isMinimized then Restore() else Minimize() end
	end
end)

--// CLOSE
local closing = false
CloseBtn.MouseButton1Click:Connect(function()
	if closing then return end
	closing = true
	FadeOutAll(MainFrame, 0.5)
	TweenService:Create(Blur, TweenInfo.new(1.2, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {Size = 0}):Play()
	task.wait(1.25)
	if Blur and Blur.Parent then Blur:Destroy() end
	ScreenGui:Destroy()
end)

--// TAB BAR
local TabBar = CreateGlassPanel("TabBar", UDim2.new(1, 0, 0, 38), UDim2.new(0, 0, 0, 50), MainFrame)

local TabBarLayout = Instance.new("UIListLayout")
TabBarLayout.FillDirection = Enum.FillDirection.Horizontal
TabBarLayout.SortOrder = Enum.SortOrder.LayoutOrder
TabBarLayout.Padding = UDim.new(0, 8)
TabBarLayout.Parent = TabBar

local TabBarPadding = Instance.new("UIPadding")
TabBarPadding.PaddingLeft = UDim.new(0, 8)
TabBarPadding.PaddingRight = UDim.new(0, 8)
TabBarPadding.PaddingTop = UDim.new(0, 5)
TabBarPadding.PaddingBottom = UDim.new(0, 5)
TabBarPadding.Parent = TabBar

--// CONTENT
local ContentPanel = CreateGlassPanel("ContentPanel", UDim2.new(1, 0, 1, -144), UDim2.new(0, 0, 0, 96), MainFrame)
ContentPanel.ClipsDescendants = true

local ContentPadding = Instance.new("UIPadding")
ContentPadding.PaddingLeft = UDim.new(0, 10)
ContentPadding.PaddingRight = UDim.new(0, 10)
ContentPadding.PaddingTop = UDim.new(0, 10)
ContentPadding.PaddingBottom = UDim.new(0, 10)
ContentPadding.Parent = ContentPanel

--// BOTTOM BAR
local BottomBar = CreateGlassPanel("BottomBar", UDim2.new(1, 0, 0, 42), UDim2.new(0, 0, 1, -42), MainFrame)

local ProfileImg = Instance.new("ImageLabel")
ProfileImg.Size = UDim2.new(0, 30, 0, 30)
ProfileImg.Position = UDim2.new(0, 8, 0.5, -15)
ProfileImg.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
ProfileImg.BackgroundTransparency = 0.9
ProfileImg.Image = Players:GetUserThumbnailAsync(LocalPlayer.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size420x420)
ProfileImg.Parent = BottomBar

local ProfileCorner = Instance.new("UICorner")
ProfileCorner.CornerRadius = UDim.new(1, 0)
ProfileCorner.Parent = ProfileImg

local ProfileName = Instance.new("TextLabel")
ProfileName.Size = UDim2.new(0, 140, 1, 0)
ProfileName.Position = UDim2.new(0, 44, 0, 0)
ProfileName.BackgroundTransparency = 1
ProfileName.Text = LocalPlayer.DisplayName
ProfileName.Font = Enum.Font.SciFi
ProfileName.TextSize = 13
ProfileName.TextColor3 = Color3.fromRGB(240, 240, 240)
ProfileName.TextXAlignment = Enum.TextXAlignment.Left
ProfileName.Parent = BottomBar

local StatsContainer = Instance.new("Frame")
StatsContainer.Size = UDim2.new(0, 150, 1, 0)
StatsContainer.Position = UDim2.new(1, -158, 0, 0)
StatsContainer.BackgroundTransparency = 1
StatsContainer.Parent = BottomBar

local StatsLayout = Instance.new("UIListLayout")
StatsLayout.FillDirection = Enum.FillDirection.Horizontal
StatsLayout.HorizontalAlignment = Enum.HorizontalAlignment.Right
StatsLayout.VerticalAlignment = Enum.VerticalAlignment.Center
StatsLayout.Padding = UDim.new(0, 10)
StatsLayout.Parent = StatsContainer

local FpsLabel = Instance.new("TextLabel")
FpsLabel.Size = UDim2.new(0, 65, 0, 25)
FpsLabel.BackgroundTransparency = 1
FpsLabel.Text = "FPS: --"
FpsLabel.Font = Enum.Font.Michroma
FpsLabel.TextSize = 10
FpsLabel.TextColor3 = Color3.fromRGB(0, 230, 150)
FpsLabel.Parent = StatsContainer

local PingLabel = Instance.new("TextLabel")
PingLabel.Size = UDim2.new(0, 65, 0, 25)
PingLabel.BackgroundTransparency = 1
PingLabel.Text = "PING: --"
PingLabel.Font = Enum.Font.Michroma
PingLabel.TextSize = 10
PingLabel.TextColor3 = Color3.fromRGB(0, 180, 255)
PingLabel.Parent = StatsContainer

local lastUpdate = tick()
local frameCount = 0
RunService.RenderStepped:Connect(function()
	frameCount = frameCount + 1
	if tick() - lastUpdate >= 1 then
		FpsLabel.Text = "FPS: " .. tostring(frameCount)
		frameCount = 0
		lastUpdate = tick()
		local ok, ping = pcall(function()
			return math.floor(StatsService.Network.ServerStatsItem["Data Ping"]:GetValue())
		end)
		if ok then
			PingLabel.Text = "PING: " .. tostring(ping) .. "ms"
		end
	end
end)

--// TAB SYSTEM
local Tabs = {}
local CurrentTab = nil

local function CreateTab(tabName, icon)
	local TabButton = Instance.new("TextButton")
	TabButton.Size = UDim2.new(0, 110, 1, 0)
	TabButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
	TabButton.BackgroundTransparency = 0.95
	TabButton.Text = (icon or "📁") .. " " .. tabName
	TabButton.Font = Enum.Font.SciFi
	TabButton.TextSize = 12
	TabButton.TextColor3 = Color3.fromRGB(180, 180, 200)
	TabButton.AutoButtonColor = false
	TabButton.Parent = TabBar

	local TabBtnCorner = Instance.new("UICorner")
	TabBtnCorner.CornerRadius = UDim.new(0, 8)
	TabBtnCorner.Parent = TabButton

	local TabPage = Instance.new("ScrollingFrame")
	TabPage.Size = UDim2.new(1, 0, 1, 0)
	TabPage.BackgroundTransparency = 1
	TabPage.BorderSizePixel = 0
	TabPage.ScrollBarThickness = 3
	TabPage.ScrollBarImageColor3 = Color3.fromRGB(120, 120, 180)
	TabPage.Visible = false
	TabPage.Parent = ContentPanel

	local PageLayout = Instance.new("UIListLayout")
	PageLayout.SortOrder = Enum.SortOrder.LayoutOrder
	PageLayout.Padding = UDim.new(0, 8)
	PageLayout.Parent = TabPage

	local function setActive(active)
		if active then
			TweenService:Create(TabButton, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
				{BackgroundTransparency = 0.75, TextColor3 = Color3.fromRGB(0, 230, 255)}):Play()
		else
			TweenService:Create(TabButton, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
				{BackgroundTransparency = 0.95, TextColor3 = Color3.fromRGB(180, 180, 200)}):Play()
		end
	end

	TabButton.MouseEnter:Connect(function()
		if CurrentTab ~= tabName then
			TweenService:Create(TabButton, TweenInfo.new(0.25),
				{BackgroundTransparency = 0.85, TextColor3 = Color3.fromRGB(230, 230, 255)}):Play()
		end
	end)
	TabButton.MouseLeave:Connect(function()
		if CurrentTab ~= tabName then
			TweenService:Create(TabButton, TweenInfo.new(0.25),
				{BackgroundTransparency = 0.95, TextColor3 = Color3.fromRGB(180, 180, 200)}):Play()
		end
	end)

	TabButton.MouseButton1Click:Connect(function()
		for name, data in pairs(Tabs) do
			data.Page.Visible = false
			if name ~= tabName then
				TweenService:Create(data.Button, TweenInfo.new(0.25),
					{BackgroundTransparency = 0.95, TextColor3 = Color3.fromRGB(180, 180, 200)}):Play()
			end
		end
		CurrentTab = tabName
		TabPage.Visible = true
		setActive(true)
	end)

	Tabs[tabName] = {Button = TabButton, Page = TabPage}

	if not CurrentTab then
		CurrentTab = tabName
		TabPage.Visible = true
		setActive(true)
	end

	return TabPage
end

--// BUTTON BUILDER
local function CreateButtonUI(parentPage, text)
	local Btn = Instance.new("TextButton")
	Btn.Size = UDim2.new(1, -6, 0, 36)
	Btn.BackgroundColor3 = Color3.fromRGB(45, 45, 65)
	Btn.BackgroundTransparency = 0.4
	Btn.Text = text
	Btn.Font = Enum.Font.SciFi
	Btn.TextSize = 13
	Btn.TextColor3 = Color3.fromRGB(255, 255, 255)
	Btn.AutoButtonColor = false
	Btn.Parent = parentPage

	local BtnCorner = Instance.new("UICorner")
	BtnCorner.CornerRadius = UDim.new(0, 8)
	BtnCorner.Parent = Btn

	local BtnStroke = Instance.new("UIStroke")
	BtnStroke.Color = Color3.fromRGB(255, 255, 255)
	BtnStroke.Transparency = 0.9
	BtnStroke.Parent = Btn

	Btn.MouseEnter:Connect(function()
		TweenService:Create(Btn, TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
			{BackgroundColor3 = Color3.fromRGB(70, 70, 115), BackgroundTransparency = 0.15,
			 TextColor3 = Color3.fromRGB(0, 230, 255)}):Play()
		TweenService:Create(BtnStroke, TweenInfo.new(0.25), {Transparency = 0.5}):Play()
	end)
	Btn.MouseLeave:Connect(function()
		TweenService:Create(Btn, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
			{BackgroundColor3 = Color3.fromRGB(45, 45, 65), BackgroundTransparency = 0.4,
			 TextColor3 = Color3.fromRGB(255, 255, 255)}):Play()
		TweenService:Create(BtnStroke, TweenInfo.new(0.3), {Transparency = 0.9}):Play()
	end)
	Btn.MouseButton1Down:Connect(function()
		TweenService:Create(Btn, TweenInfo.new(0.08, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
			{Size = UDim2.new(0.96, -6, 0, 33), BackgroundTransparency = 0.05}):Play()
	end)
	Btn.MouseButton1Up:Connect(function()
		TweenService:Create(Btn, TweenInfo.new(0.25, Enum.EasingStyle.Back, Enum.EasingDirection.Out),
			{Size = UDim2.new(1, -6, 0, 36), BackgroundTransparency = 0.15}):Play()
	end)

	return Btn
end

--// SETTINGS PANEL
local SettingsPanel = CreateGlassPanel("SettingsPanel", UDim2.new(1, 0, 1, 0), UDim2.new(0, 0, 0, 0), MainFrame)
SettingsPanel.Visible = false
SettingsPanel.ZIndex = 20

local SettingsTitle = Instance.new("TextLabel")
SettingsTitle.Size = UDim2.new(1, 0, 0, 40)
SettingsTitle.BackgroundTransparency = 1
SettingsTitle.Text = "AYARLAR"
SettingsTitle.Font = Enum.Font.Michroma
SettingsTitle.TextSize = 14
SettingsTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
SettingsTitle.ZIndex = 21
SettingsTitle.Parent = SettingsPanel

local CloseSettings = Instance.new("TextButton")
CloseSettings.Size = UDim2.new(0, 30, 0, 30)
CloseSettings.Position = UDim2.new(1, -38, 0, 5)
CloseSettings.BackgroundTransparency = 1
CloseSettings.Text = "✕"
CloseSettings.Font = Enum.Font.Michroma
CloseSettings.TextSize = 14
CloseSettings.TextColor3 = Color3.fromRGB(255, 100, 100)
CloseSettings.AutoButtonColor = false
CloseSettings.ZIndex = 21
CloseSettings.Parent = SettingsPanel

local SettingsContent = Instance.new("ScrollingFrame")
SettingsContent.Name = "SettingsContent"
SettingsContent.Size = UDim2.new(1, -20, 1, -55)
SettingsContent.Position = UDim2.new(0, 10, 0, 45)
SettingsContent.BackgroundTransparency = 1
SettingsContent.BorderSizePixel = 0
SettingsContent.ScrollBarThickness = 3
SettingsContent.ScrollBarImageColor3 = Color3.fromRGB(120, 120, 180)
SettingsContent.ZIndex = 21
SettingsContent.Parent = SettingsPanel

local SettingsContentLayout = Instance.new("UIListLayout")
SettingsContentLayout.SortOrder = Enum.SortOrder.LayoutOrder
SettingsContentLayout.Padding = UDim.new(0, 8)
SettingsContentLayout.Parent = SettingsContent

CloseSettings.MouseButton1Click:Connect(function()
	SettingsPanel.Visible = false
end)

SettingsBtn.MouseButton1Click:Connect(function()
	SettingsPanel.Visible = not SettingsPanel.Visible
end)

--=====================================================================
--// HUB KIT API
--=====================================================================
-- Ahcttab("TabName", "icon")               → Tab oluşturur
-- ahctbtn("TabName", "BtnName")            → Tab'a buton ekler
-- ahmdbtsp("TabName", "BtnName", script)   → Butona script bağlar (string/function)
-- ahmdsetctbtn("BtnName")                  → Settings'e buton ekler
-- ahmdspset("BtnName", script)             → Settings butonuna script bağlar
--=====================================================================

local TabRegistry = {}
local SettingsRegistry = {}

local function ExecuteCallback(cb)
	if type(cb) == "function" then
		task.spawn(cb)
	elseif type(cb) == "string" then
		local fn, err = loadstring(cb)
		if fn then
			task.spawn(fn)
		else
			warn("[OXC Hub] Script derlenemedi: " .. tostring(err))
		end
	else
		warn("[OXC Hub] Geçersiz script tipi: " .. typeof(cb))
	end
end

local function Ahcttab(tabName, icon)
	if type(tabName) ~= "string" then
		warn("[OXC Hub] Ahcttab: tabName string olmalı")
		return nil
	end
	if TabRegistry[tabName] then
		warn("[OXC Hub] Tab zaten var: " .. tabName)
		return tabName
	end
	local page = CreateTab(tabName, icon or "📁")
	TabRegistry[tabName] = { page = page, buttons = {} }
	return tabName
end

local function ahctbtn(tabName, btnName)
	if type(tabName) ~= "string" or type(btnName) ~= "string" then
		warn("[OXC Hub] ahctbtn: parametreler string olmalı")
		return nil
	end
	local tab = TabRegistry[tabName]
	if not tab then
		warn("[OXC Hub] Tab bulunamadı: " .. tabName)
		return nil
	end
	if tab.buttons[btnName] then
		warn("[OXC Hub] Buton zaten var: " .. btnName)
		return btnName
	end
	local btnData = { callback = nil }
	local btn = CreateButtonUI(tab.page, btnName)
	btnData.button = btn
	tab.buttons[btnName] = btnData

	btn.MouseButton1Click:Connect(function()
		if btnData.callback then
			ExecuteCallback(btnData.callback)
		end
	end)
	return btnName
end

local function ahmdbtsp(tabName, btnName, script_)
	local tab = TabRegistry[tabName]
	if not tab then
		warn("[OXC Hub] ahmdbtsp: Tab bulunamadı: " .. tostring(tabName))
		return
	end
	local btnData = tab.buttons[btnName]
	if not btnData then
		warn("[OXC Hub] ahmdbtsp: Buton bulunamadı: " .. tostring(btnName))
		return
	end
	btnData.callback = script_
end

local function ahmdsetctbtn(btnName)
	if type(btnName) ~= "string" then
		warn("[OXC Hub] ahmdsetctbtn: btnName string olmalı")
		return nil
	end
	if SettingsRegistry[btnName] then
		warn("[OXC Hub] Settings butonu zaten var: " .. btnName)
		return btnName
	end
	local btnData = { callback = nil }
	local btn = CreateButtonUI(SettingsContent, btnName)
	btnData.button = btn
	SettingsRegistry[btnName] = btnData

	btn.MouseButton1Click:Connect(function()
		if btnData.callback then
			ExecuteCallback(btnData.callback)
		end
	end)
	return btnName
end

local function ahmdspset(btnName, script_)
	local btnData = SettingsRegistry[btnName]
	if not btnData then
		warn("[OXC Hub] ahmdspset: Settings butonu bulunamadı: " .. tostring(btnName))
		return
	end
	btnData.callback = script_
end

-- Global expose
_G.Ahcttab = Ahcttab
_G.ahctbtn = ahctbtn
_G.ahmdbtsp = ahmdbtsp
_G.ahmdsetctbtn = ahmdsetctbtn
_G.ahmdspset = ahmdspset

--=====================================================================
--// KENDİ UI'NI BURADAN AŞAĞIYA EKLE
--=====================================================================

-- Örnek:
-- Ahcttab("Ana Sayfa", "🏠")
-- ahctbtn("Ana Sayfa", "Buton Adı")
-- ahmdbtsp("Ana Sayfa", "Buton Adı", function()
--     print("Çalıştı!")
-- end)

print("[OXC Hub] Template yüklendi. API hazır.")
}
