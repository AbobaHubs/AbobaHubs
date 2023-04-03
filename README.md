local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/shlexware/Orion/main/source')))()

local Window = OrionLib:MakeWindow({Name = "AbobaHubs 2.0 GUI | CounterBlox ",IntroText = "AbobaHubs Project", HidePremium = false, SaveConfig = true, ConfigFolder = "OrionTest"})

local Tab = Window:MakeTab({
    Name = "Aim",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

Tab:AddButton({
    Name = "AIM(RIGHT CLICK)",
    Callback = function()
            local teamCheck = false
local fov = 150
local smoothing = 1

local RunService = game:GetService("RunService")

local FOVring = Drawing.new("Circle")
FOVring.Visible = true
FOVring.Thickness = 1.5
FOVring.Radius = fov
FOVring.Transparency = 1
FOVring.Color = Color3.fromRGB(255, 128, 128)
FOVring.Position = workspace.CurrentCamera.ViewportSize/2

local function getClosest(cframe)
   local ray = Ray.new(cframe.Position, cframe.LookVector).Unit
   
   local target = nil
   local mag = math.huge
   
   for i,v in pairs(game.Players:GetPlayers()) do
       if v.Character and v.Character:FindFirstChild("Head") and v.Character:FindFirstChild("Humanoid") and v.Character:FindFirstChild("HumanoidRootPart") and v ~= game.Players.LocalPlayer and (v.Team ~= game.Players.LocalPlayer.Team or (not teamCheck)) then
           local magBuf = (v.Character.Head.Position - ray:ClosestPoint(v.Character.Head.Position)).Magnitude
           
           if magBuf < mag then
               mag = magBuf
               target = v
           end
       end
   end
   
   return target
end

loop = RunService.RenderStepped:Connect(function()
   local UserInputService = game:GetService("UserInputService")
   local pressed = --[[UserInputService:IsKeyDown(Enum.KeyCode.E)]] UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) --Enum.UserInputType.MouseButton2
   local localPlay = game.Players.localPlayer.Character
   local cam = workspace.CurrentCamera
   local zz = workspace.CurrentCamera.ViewportSize/2
   
   if pressed then
       local Line = Drawing.new("Line")
       local curTar = getClosest(cam.CFrame)
       local ssHeadPoint = cam:WorldToScreenPoint(curTar.Character.Head.Position)
       ssHeadPoint = Vector2.new(ssHeadPoint.X, ssHeadPoint.Y)
       if (ssHeadPoint - zz).Magnitude < fov then
           workspace.CurrentCamera.CFrame = workspace.CurrentCamera.CFrame:Lerp(CFrame.new(cam.CFrame.Position, curTar.Character.Head.Position), smoothing)
       end
   end
   
   if UserInputService:IsKeyDown(Enum.KeyCode.Delete) then
       loop:Disconnect()
       FOVring:Remove()
   end
end)
  end    
})

local Tab = Window:MakeTab({
    Name = "Visuals",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

Tab:AddButton({
    Name = "Box Esp",
    Callback = function()
            -- settings
local settings = {
   defaultcolor = Color3.fromRGB(255,0,0),
   teamcheck = false,
   teamcolor = true
};

-- services
local runService = game:GetService("RunService");
local players = game:GetService("Players");

-- variables
local localPlayer = players.LocalPlayer;
local camera = workspace.CurrentCamera;

-- functions
local newVector2, newColor3, newDrawing = Vector2.new, Color3.new, Drawing.new;
local tan, rad = math.tan, math.rad;
local round = function(...) local a = {}; for i,v in next, table.pack(...) do a[i] = math.round(v); end return unpack(a); end;
local wtvp = function(...) local a, b = camera.WorldToViewportPoint(camera, ...) return newVector2(a.X, a.Y), b, a.Z end;

local espCache = {};
local function createEsp(player)
   local drawings = {};
   
   drawings.box = newDrawing("Square");
   drawings.box.Thickness = 1;
   drawings.box.Filled = false;
   drawings.box.Color = settings.defaultcolor;
   drawings.box.Visible = false;
   drawings.box.ZIndex = 2;

   drawings.boxoutline = newDrawing("Square");
   drawings.boxoutline.Thickness = 3;
   drawings.boxoutline.Filled = false;
   drawings.boxoutline.Color = newColor3();
   drawings.boxoutline.Visible = false;
   drawings.boxoutline.ZIndex = 1;

   espCache[player] = drawings;
end

local function removeEsp(player)
   if rawget(espCache, player) then
       for _, drawing in next, espCache[player] do
           drawing:Remove();
       end
       espCache[player] = nil;
   end
end

local function updateEsp(player, esp)
   local character = player and player.Character;
   if character then
       local cframe = character:GetModelCFrame();
       local position, visible, depth = wtvp(cframe.Position);
       esp.box.Visible = visible;
       esp.boxoutline.Visible = visible;

       if cframe and visible then
           local scaleFactor = 1 / (depth * tan(rad(camera.FieldOfView / 2)) * 2) * 1000;
           local width, height = round(4 * scaleFactor, 5 * scaleFactor);
           local x, y = round(position.X, position.Y);

           esp.box.Size = newVector2(width, height);
           esp.box.Position = newVector2(round(x - width / 2, y - height / 2));
           esp.box.Color = settings.teamcolor and player.TeamColor.Color or settings.defaultcolor;

           esp.boxoutline.Size = esp.box.Size;
           esp.boxoutline.Position = esp.box.Position;
       end
   else
       esp.box.Visible = false;
       esp.boxoutline.Visible = false;
   end
end

-- main
for _, player in next, players:GetPlayers() do
   if player ~= localPlayer then
       createEsp(player);
   end
end

players.PlayerAdded:Connect(function(player)
   createEsp(player);
end);

players.PlayerRemoving:Connect(function(player)
   removeEsp(player);
end)

runService:BindToRenderStep("esp", Enum.RenderPriority.Camera.Value, function()
   for player, drawings in next, espCache do
       if settings.teamcheck and player.Team == localPlayer.Team then
           continue;
       end

       if drawings and player ~= localPlayer then
           updateEsp(player, drawings);
       end
   end
end)
    end    
})

Tab:AddButton({
    Name = "Glow Esp",
    Callback = function()
            if not game:IsLoaded() then
    game.Loaded:Wait()
end

--//Toggle\\--
getgenv().Toggle = true -- This toggles the esp, turning it to false will turn it off
getgenv().TC = false -- This toggles team check, turning it on will turn on team check
local PlayerName = "DisplayName" -- You can decide if you want the Player's name to be a display name which is "DisplayName", or username which is "Name"

--//Variables\\--
local P = game:GetService("Players")
local LP = P.LocalPlayer

--//Debounce\\--
local DB = false

--//Function\\--
local ESP = function(PLR)
    local ESP = Instance.new("Highlight")

    ESP.Parent = PLR.Character
    ESP.Name = "Totally NOT Esp"
    ESP.Adornee = PLR.Character
    ESP.Archivable = true
    ESP.Enabled = true
    ESP.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    ESP.FillColor = PLR.TeamColor.Color
    ESP.FillTransparency = 0.5
    ESP.OutlineColor = Color3.fromRGB(255, 255, 255)
    ESP.OutlineTransparency = 0
end

local ESPT = function(PLR)
    --//ESP-Text\\--
    local Icon = Instance.new("BillboardGui")
    local ESPText = Instance.new("TextLabel")

    Icon.Parent = PLR.Character
    Icon.Name = "Icon"
    Icon.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    Icon.Active = true
    Icon.AlwaysOnTop = true
    Icon.ExtentsOffset = Vector3.new(0, 1, 0)
    Icon.LightInfluence = 1.000
    Icon.Size = UDim2.new(0, 800, 0, 50)

    ESPText.Name = "ESP Text"
    ESPText.Parent = Icon
    ESPText.BackgroundColor3 = PLR.TeamColor.Color
    ESPText.BackgroundTransparency = 1.000
    ESPText.Size = UDim2.new(0, 800, 0, 50)
    ESPText.Font = Enum.Font.SciFi
    --ESPText.Text = "Name: "..PLR[PlayerName].." | Health: "..PLR.Character:FindFirstChildOfClass("Humanoid").Health
    ESPText.Text = "NIL"
    ESPText.TextColor3 = PLR.TeamColor.Color
    ESPText.TextSize = 18.000
    ESPText.TextWrapped = true
end

local ESPTF = function(PLR, ICON)
    local ESPText = Instance.new("TextLabel")

    ESPText.Name = "ESP Text"
    ESPText.Parent = ICON
    ESPText.BackgroundColor3 = PLR.TeamColor.Color
    ESPText.BackgroundTransparency = 1.000
    ESPText.Size = UDim2.new(0, 800, 0, 50)
    ESPText.Font = Enum.Font.SciFi
    --ESPText.Text = "Name: "..PLR[PlayerName].." | Health: "..PLR.Character:FindFirstChildOfClass("Humanoid").Health
    ESPText.Text = "NIL"
    ESPText.TextColor3 = PLR.TeamColor.Color
    ESPText.TextSize = 18.000
    ESPText.TextWrapped = true
end
--//Notification\\--
game.StarterGui:SetCore("SendNotification", {
    Title = "Notification",
    Text = "This Script Was Made By Psygen" ,
    Button1 = "Ok",
    Button2 = "Shut Up",
    Duration = math.huge
})

game.StarterGui:SetCore("SendNotification", {
    Title = "Notification",
    Text = "Please Subscribe To Psygen On Youtube" ,
    Button1 = "Ok",
    Button2 = "Shut Up",
    Duration = math.huge
})

--//Loop\\--
while task.wait() do
    if not getgenv().Toggle then
        break
    end
    if DB then 
        return 
    end
    DB = true

    pcall(function()
        for i,v in pairs(P:GetChildren()) do
            if v:IsA("Player") then
                if v ~= game.Players.LocalPlayer then
                    if v.Character ~= nil then

                        if getgenv().TC == false and v.Character:FindFirstChild("Totally NOT Esp") == nil then
                            ESP(v)
                        else
                            if getgenv().TC == false and v.Character:FindFirstChild("Icon") == nil then
                                ESPT(v)
                            else
                                if getgenv().TC == false and v.Character.Icon:FindFirstChild("ESP Text") == nil then
                                    ESPTF(v, v.Character.Icon)
                                else
                                    if v.Character:FindFirstChild("Totally NOT Esp").Enabled == false then
                                        v.Character:FindFirstChild("Totally NOT Esp").Enabled = true
                                    else
                                        if v.Character:FindFirstChild("Icon").Enabled == false then
                                            v.Character:FindFirstChild("Icon").Enabled = true
                                        else
                                            if v.Character:FindFirstChild("Icon") and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") ~= nil then
                                                local pos = math.floor(((game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart")).Position - (v.Character:FindFirstChild("HumanoidRootPart")).Position).magnitude)
                                                -- Credits to Infinite Yield for this part (pos) ^^^^^^
                                                v.Character.Icon["ESP Text"].Text = "Name: "..v[PlayerName].." | Health: "..v.Character:FindFirstChildOfClass("Humanoid").Health.." | Distance: "..pos
                                            else
                                                if v.Character:FindFirstChild("Icon") and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") == nil then
                                                    v.Character.Icon["ESP Text"].Text = "Name: "..v[PlayerName].." | Health: "..v.Character:FindFirstChildOfClass("Humanoid").Health
                                                else
                                                    if getgenv().TC == true and v.TeamColor ~= game.Players.LocalPlayer.TeamColor and v.Character:FindFirstChild("Totally NOT Esp") == nil then
                                                        ESP(v)
                                                    else
                                                        if getgenv().TC == true and v.TeamColor ~= game.Players.LocalPlayer.TeamColor and v.Character:FindFirstChild("Icon") == nil then
                                                            ESPT(v)
                                                        else
                                                            if getgenv().TC == true and v.TeamColor ~= game.Players.LocalPlayer.TeamColor and v.Character.Icon:FindFirstChild("ESP Text") == nil then
                                                                ESPTF(v, v.Character.Icon)
                                                            else
                                                                if getgenv().TC == true and v.Character:FindFirstChild("Totally NOT Esp").FillColor ~= v.TeamColor.Color then
                                                                    v.Character:FindFirstChild("Totally NOT Esp").FillColor = v.TeamColor.Color
                                                                else
                                                                    if getgenv().TC == true and v.Character.Icon:FindFirstChild("ESP Text").TextColor3 ~= v.TeamColor.Color then
                                                                        v.Character.Icon["ESP Text"].TextColor3 = v.TeamColor.Color
                                                                    end
                                                                end
                                                            end
                                                        end
                                                    end
                                                end
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
            end
        end
    end)

    wait()

    DB = false
end
    end    
})

Tab:AddButton({
    Name = "Name ESP",
    Callback = function()
            local c = workspace.CurrentCamera
local ps = game:GetService("Players")
local lp = ps.LocalPlayer
local rs = game:GetService("RunService")

local function esp(p,cr)
    local h = cr:WaitForChild("Humanoid")
    local hrp = cr:WaitForChild("Head")

    local text = Drawing.new("Text")
    text.Visible = false
    text.Center = true
    text.Outline = false 
    text.Font = 3
    text.Size = 16.16
    text.Color = Color3.new(170,170,170)

    local conection
    local conection2
    local conection3

    local function dc()
        text.Visible = false
        text:Remove()
        if conection then
            conection:Disconnect()
            conection = nil 
        end
        if conection2 then
            conection2:Disconnect()
            conection2 = nil 
        end
        if conection3 then
            conection3:Disconnect()
            conection3 = nil 
        end
    end

    conection2 = cr.AncestryChanged:Connect(function(_,parent)
        if not parent then
            dc()
        end
    end)

    conection3 = h.HealthChanged:Connect(function(v)
        if (v<=0) or (h:GetState() == Enum.HumanoidStateType.Dead) then
            dc()
        end
    end)

    conection = rs.RenderStepped:Connect(function()
        local hrp_pos,hrp_onscreen = c:WorldToViewportPoint(hrp.Position)
        if hrp_onscreen then
            text.Position = Vector2.new(hrp_pos.X, hrp_pos.Y - 27)
            text.Text = "[ "..p.Name.." ]"
            text.Visible = true
        else
            text.Visible = false
        end
    end)
end

local function p_added(p)
    if p.Character then
        esp(p,p.Character)
    end
    p.CharacterAdded:Connect(function(cr)
        esp(p,cr)
    end)
end

for i,p in next, ps:GetPlayers() do 
    if p ~= lp then
        p_added(p)
    end
end

ps.PlayerAdded:Connect(p_added)
    end    
})

Tab:AddButton({
    Name = "Line Trace + Box(Green)",
    Callback = function()
            --// Made by Blissful#4992
--// Locals:
local workspace = game:GetService("Workspace")
local player = game:GetService("Players").LocalPlayer
local camera = workspace.CurrentCamera

--// Settings:
local on = true -- Use this if your making gui

local Box_Color = Color3.fromRGB(0, 255, 50)
local Box_Thickness = 1.4
local Box_Transparency = 1 -- 1 Visible, 0 Not Visible

local Tracers = true
local Tracer_Color = Color3.fromRGB(0, 255, 50)
local Tracer_Thickness = 1.4
local Tracer_Transparency = 1 -- 1 Visible, 0 Not Visible

local Autothickness = false -- Makes screen less encumbered

local Team_Check = false
local red = Color3.fromRGB(227, 52, 52)
local green = Color3.fromRGB(88, 217, 24)

local function NewLine()
    local line = Drawing.new("Line")
    line.Visible = false
    line.From = Vector2.new(0, 0)
    line.To = Vector2.new(1, 1)
    line.Color = Box_Color
    line.Thickness = Box_Thickness
    line.Transparency = Box_Transparency
    return line
end

--// Main Function:
for i, v in pairs(game.Players:GetChildren()) do
    --// Lines for 3D box (12)
    local lines = {
        line1 = NewLine(),
        line2 = NewLine(),
        line3 = NewLine(),
        line4 = NewLine(),
        line5 = NewLine(),
        line6 = NewLine(),
        line7 = NewLine(),
        line8 = NewLine(),
        line9 = NewLine(),
        line10 = NewLine(),
        line11 = NewLine(),
        line12 = NewLine(),
        Tracer = NewLine()
    }

    lines.Tracer.Color = Tracer_Color
    lines.Tracer.Thickness = Tracer_Thickness
    lines.Tracer.Transparency = Tracer_Transparency

    --// Updates ESP (lines) in render loop
    local function ESP()
        local connection
        connection = game:GetService("RunService").RenderStepped:Connect(function()
            if on and v.Character ~= nil and v.Character:FindFirstChild("Humanoid") ~= nil and v.Character:FindFirstChild("HumanoidRootPart") ~= nil and v.Name ~= player.Name and v.Character.Humanoid.Health > 0 and v.Character:FindFirstChild("Head") ~= nil then
                local pos, vis = camera:WorldToViewportPoint(v.Character.HumanoidRootPart.Position)
                if vis then
                    local Scale = v.Character.Head.Size.Y/2
                    local Size = Vector3.new(2, 3, 1.5) * (Scale * 2) -- Change this for different box size

                    local Top1 = camera:WorldToViewportPoint((v.Character.HumanoidRootPart.CFrame * CFrame.new(-Size.X, Size.Y, -Size.Z)).p)
                    local Top2 = camera:WorldToViewportPoint((v.Character.HumanoidRootPart.CFrame * CFrame.new(-Size.X, Size.Y, Size.Z)).p)
                    local Top3 = camera:WorldToViewportPoint((v.Character.HumanoidRootPart.CFrame * CFrame.new(Size.X, Size.Y, Size.Z)).p)
                    local Top4 = camera:WorldToViewportPoint((v.Character.HumanoidRootPart.CFrame * CFrame.new(Size.X, Size.Y, -Size.Z)).p)

                    local Bottom1 = camera:WorldToViewportPoint((v.Character.HumanoidRootPart.CFrame * CFrame.new(-Size.X, -Size.Y, -Size.Z)).p)
                    local Bottom2 = camera:WorldToViewportPoint((v.Character.HumanoidRootPart.CFrame * CFrame.new(-Size.X, -Size.Y, Size.Z)).p)
                    local Bottom3 = camera:WorldToViewportPoint((v.Character.HumanoidRootPart.CFrame * CFrame.new(Size.X, -Size.Y, Size.Z)).p)
                    local Bottom4 = camera:WorldToViewportPoint((v.Character.HumanoidRootPart.CFrame * CFrame.new(Size.X, -Size.Y, -Size.Z)).p)

                    --// Top:
                    lines.line1.From = Vector2.new(Top1.X, Top1.Y)
                    lines.line1.To = Vector2.new(Top2.X, Top2.Y)

                    lines.line2.From = Vector2.new(Top2.X, Top2.Y)
                    lines.line2.To = Vector2.new(Top3.X, Top3.Y)

                    lines.line3.From = Vector2.new(Top3.X, Top3.Y)
                    lines.line3.To = Vector2.new(Top4.X, Top4.Y)

                    lines.line4.From = Vector2.new(Top4.X, Top4.Y)
                    lines.line4.To = Vector2.new(Top1.X, Top1.Y)

                    --// Bottom:
                    lines.line5.From = Vector2.new(Bottom1.X, Bottom1.Y)
                    lines.line5.To = Vector2.new(Bottom2.X, Bottom2.Y)

                    lines.line6.From = Vector2.new(Bottom2.X, Bottom2.Y)
                    lines.line6.To = Vector2.new(Bottom3.X, Bottom3.Y)

                    lines.line7.From = Vector2.new(Bottom3.X, Bottom3.Y)
                    lines.line7.To = Vector2.new(Bottom4.X, Bottom4.Y)

                    lines.line8.From = Vector2.new(Bottom4.X, Bottom4.Y)
                    lines.line8.To = Vector2.new(Bottom1.X, Bottom1.Y)

                    --//S ides:
                    lines.line9.From = Vector2.new(Bottom1.X, Bottom1.Y)
                    lines.line9.To = Vector2.new(Top1.X, Top1.Y)

                    lines.line10.From = Vector2.new(Bottom2.X, Bottom2.Y)
                    lines.line10.To = Vector2.new(Top2.X, Top2.Y)

                    lines.line11.From = Vector2.new(Bottom3.X, Bottom3.Y)
                    lines.line11.To = Vector2.new(Top3.X, Top3.Y)

                    lines.line12.From = Vector2.new(Bottom4.X, Bottom4.Y)
                    lines.line12.To = Vector2.new(Top4.X, Top4.Y)

                    --// Tracer:
                    if Tracers then
                        local trace = camera:WorldToViewportPoint((v.Character.HumanoidRootPart.CFrame * CFrame.new(0, -Size.Y, 0)).p)

                        lines.Tracer.From = Vector2.new(camera.ViewportSize.X/2, camera.ViewportSize.Y)
                        lines.Tracer.To = Vector2.new(trace.X, trace.Y)
                    end

                    --// Teamcheck:
                    if Team_Check then
                        if v.TeamColor == player.TeamColor then
                            for u, x in pairs(lines) do
                                x.Color = green
                            end
                        else 
                            for u, x in pairs(lines) do
                                x.Color = red
                            end
                        end
                    end

                    --// Autothickness:
                    if Autothickness then
                        local distance = (player.Character.HumanoidRootPart.Position - v.Character.HumanoidRootPart.Position).magnitude
                        local value = math.clamp(1/distance*100, 0.1, 4) --0.1 is min thickness, 6 is max
                        for u, x in pairs(lines) do
                            x.Thickness = value
                        end
                    else 
                        for u, x in pairs(lines) do
                            x.Thickness = Box_Thickness
                        end
                    end

                    for u, x in pairs(lines) do
                        if x ~= lines.Tracer then
                            x.Visible = true
                        end
                    end
                    if Tracers then
                        lines.Tracer.Visible = true
                    end
                else 
                    for u, x in pairs(lines) do
                        x.Visible = false
                    end
                end
            else 
                for u, x in pairs(lines) do
                    x.Visible = false
                end
                if game.Players:FindFirstChild(v.Name) == nil then
                    connection:Disconnect()
                end
            end
        end)
    end
    coroutine.wrap(ESP)()
end

game.Players.PlayerAdded:Connect(function(newplr)
    --// Lines for 3D box (12)
    local lines = {
        line1 = NewLine(),
        line2 = NewLine(),
        line3 = NewLine(),
        line4 = NewLine(),
        line5 = NewLine(),
        line6 = NewLine(),
        line7 = NewLine(),
        line8 = NewLine(),
        line9 = NewLine(),
        line10 = NewLine(),
        line11 = NewLine(),
        line12 = NewLine(),
        Tracer = NewLine()
    }

    lines.Tracer.Color = Tracer_Color
    lines.Tracer.Thickness = Tracer_Thickness
    lines.Tracer.Transparency = Tracer_Transparency

    --// Updates ESP (lines) in render loop
    local function ESP()
        local connection
        connection = game:GetService("RunService").RenderStepped:Connect(function()
            if on and newplr.Character ~= nil and newplr.Character:FindFirstChild("Humanoid") ~= nil and newplr.Character:FindFirstChild("HumanoidRootPart") ~= nil and newplr.Name ~= player.Name and newplr.Character.Humanoid.Health > 0 and newplr.Character:FindFirstChild("Head") ~= nil then
                local pos, vis = camera:WorldToViewportPoint(newplr.Character.HumanoidRootPart.Position)
                if vis then
                    local Scale = newplr.Character.Head.Size.Y/2
                    local Size = Vector3.new(2, 3, 1.5) * (Scale * 2) -- Change this for different box size

                    local Top1 = camera:WorldToViewportPoint((newplr.Character.HumanoidRootPart.CFrame * CFrame.new(-Size.X, Size.Y, -Size.Z)).p)
                    local Top2 = camera:WorldToViewportPoint((newplr.Character.HumanoidRootPart.CFrame * CFrame.new(-Size.X, Size.Y, Size.Z)).p)
                    local Top3 = camera:WorldToViewportPoint((newplr.Character.HumanoidRootPart.CFrame * CFrame.new(Size.X, Size.Y, Size.Z)).p)
                    local Top4 = camera:WorldToViewportPoint((newplr.Character.HumanoidRootPart.CFrame * CFrame.new(Size.X, Size.Y, -Size.Z)).p)

                    local Bottom1 = camera:WorldToViewportPoint((newplr.Character.HumanoidRootPart.CFrame * CFrame.new(-Size.X, -Size.Y, -Size.Z)).p)
                    local Bottom2 = camera:WorldToViewportPoint((newplr.Character.HumanoidRootPart.CFrame * CFrame.new(-Size.X, -Size.Y, Size.Z)).p)
                    local Bottom3 = camera:WorldToViewportPoint((newplr.Character.HumanoidRootPart.CFrame * CFrame.new(Size.X, -Size.Y, Size.Z)).p)
                    local Bottom4 = camera:WorldToViewportPoint((newplr.Character.HumanoidRootPart.CFrame * CFrame.new(Size.X, -Size.Y, -Size.Z)).p)

                    --// Top:
                    lines.line1.From = Vector2.new(Top1.X, Top1.Y)
                    lines.line1.To = Vector2.new(Top2.X, Top2.Y)

                    lines.line2.From = Vector2.new(Top2.X, Top2.Y)
                    lines.line2.To = Vector2.new(Top3.X, Top3.Y)

                    lines.line3.From = Vector2.new(Top3.X, Top3.Y)
                    lines.line3.To = Vector2.new(Top4.X, Top4.Y)

                    lines.line4.From = Vector2.new(Top4.X, Top4.Y)
                    lines.line4.To = Vector2.new(Top1.X, Top1.Y)

                    --// Bottom:
                    lines.line5.From = Vector2.new(Bottom1.X, Bottom1.Y)
                    lines.line5.To = Vector2.new(Bottom2.X, Bottom2.Y)

                    lines.line6.From = Vector2.new(Bottom2.X, Bottom2.Y)
                    lines.line6.To = Vector2.new(Bottom3.X, Bottom3.Y)

                    lines.line7.From = Vector2.new(Bottom3.X, Bottom3.Y)
                    lines.line7.To = Vector2.new(Bottom4.X, Bottom4.Y)

                    lines.line8.From = Vector2.new(Bottom4.X, Bottom4.Y)
                    lines.line8.To = Vector2.new(Bottom1.X, Bottom1.Y)

                    --//S ides:
                    lines.line9.From = Vector2.new(Bottom1.X, Bottom1.Y)
                    lines.line9.To = Vector2.new(Top1.X, Top1.Y)

                    lines.line10.From = Vector2.new(Bottom2.X, Bottom2.Y)
                    lines.line10.To = Vector2.new(Top2.X, Top2.Y)

                    lines.line11.From = Vector2.new(Bottom3.X, Bottom3.Y)
                    lines.line11.To = Vector2.new(Top3.X, Top3.Y)

                    lines.line12.From = Vector2.new(Bottom4.X, Bottom4.Y)
                    lines.line12.To = Vector2.new(Top4.X, Top4.Y)

                    --// Tracer:
                    if Tracers then
                        local trace = camera:WorldToViewportPoint((newplr.Character.HumanoidRootPart.CFrame * CFrame.new(0, -Size.Y, 0)).p)
                        lines.Tracer.From = Vector2.new(camera.ViewportSize.X/2, camera.ViewportSize.Y)
                        lines.Tracer.To = Vector2.new(trace.X, trace.Y)
                    end

                    --// Teamcheck:
                    if Team_Check then
                        if newplr.TeamColor == player.TeamColor then
                            for u, x in pairs(lines) do
                                x.Color = green
                            end
                        else 
                            for u, x in pairs(lines) do
                                x.Color = red
                            end
                        end
                    end

                    --// Autothickness:
                    if Autothickness then
                        local distance = (player.Character.HumanoidRootPart.Position - newplr.Character.HumanoidRootPart.Position).magnitude
                        local value = math.clamp(1/distance*100, 0.1, 4) --0.1 is min thickness, 6 is max
                        for u, x in pairs(lines) do
                            x.Thickness = value
                        end
                    else 
                        for u, x in pairs(lines) do
                            x.Thickness = Box_Thickness
                        end
                    end

                    for u, x in pairs(lines) do
                        if x ~= lines.Tracer then
                            x.Visible = true
                        end
                    end
                    if Tracers then
                        lines.Tracer.Visible = true
                    end
                else 
                    for u, x in pairs(lines) do
                        x.Visible = false
                    end
                end
            else 
                for u, x in pairs(lines) do
                    x.Visible = false
                end
                if game.Players:FindFirstChild(newplr.Name) == nil then
                    connection:Disconnect()
                end
            end
        end)
    end
    coroutine.wrap(ESP)()
end)
    end    
})

--[[
Name = <string> - The name of the button.
Callback = <function> - The function of the button.
]]

local Tab = Window:MakeTab({
    Name = "Misc",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

Tab:AddButton({
    Name = "infJump",
    Callback = function()
            -- //~ F to toggle ~\ --
-- I have no idea who made this! 
_G.infinjump = true
 
local Player = game:GetService("Players").LocalPlayer
local Mouse = Player:GetMouse()
Mouse.KeyDown:connect(function(k)
if _G.infinjump then
if k:byte() == 32 then
Humanoid = game:GetService("Players").LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
Humanoid:ChangeState("Jumping")
wait(0.1)
Humanoid:ChangeState("Seated")
end
end
end)
 
local Player = game:GetService("Players").LocalPlayer
local Mouse = Player:GetMouse()
Mouse.KeyDown:connect(function(k)
k = k:lower()
if k == "f" then
if _G.infinjump == true then
_G.infinjump = false
else
_G.infinjump = true
end
end
end)
    end    
})

Tab:AddButton({
    Name = "ClickTeleport",
    Callback = function()
            local Player = game.Players.LocalPlayer
local Mouse = Player:GetMouse()
local UserInputService = game:GetService('UserInputService')
 
local HoldingControl = false
 
Mouse.Button1Down:connect(function()
if HoldingControl then
Player.Character:MoveTo(Mouse.Hit.p)
end
end)
 
UserInputService.InputBegan:connect(function(Input, Processed)
if Input.UserInputType == Enum.UserInputType.Keyboard then
if Input.KeyCode == Enum.KeyCode.LeftControl then
HoldingControl = true
elseif Input.KeyCode == Enum.KeyCode.RightControl then
HoldingControl = true
end
end
end)
 
UserInputService.InputEnded:connect(function(Input, Processed)
if Input.UserInputType == Enum.UserInputType.Keyboard then
if Input.KeyCode == Enum.KeyCode.LeftControl then
HoldingControl = false
elseif Input.KeyCode == Enum.KeyCode.RightControl then
HoldingControl = false
end
end
end)
    end    
})

Tab:AddButton({
    Name = "BunnyHop",
    Callback = function()
            local plrs = game:GetService("Players")
    local plr = plrs.LocalPlayer
    local UserInputService = game:GetService("UserInputService")
    local space = UserInputService:IsKeyDown(Enum.KeyCode.Space)
    local Client = getsenv(game.Players.LocalPlayer.PlayerGui.Client)
    local backup = {}
    backup.speed = Client.speedupdate
    local j = 0

while wait() do
space = UserInputService:IsKeyDown(Enum.KeyCode.Space)
    if space==true then
    if workspace:FindFirstChild(plr.Name) then
    if plr.Character:FindFirstChild("Humanoid") then
    j = 1
    game.Players.LocalPlayer.Character.Humanoid.Jump = true
    game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = 18
    game.Players.LocalPlayer.Character.Humanoid.JumpPower = 22
    Client.speedupdate = function()
    end
    end
    end
    else
    if j == 1 then
    j = 0
    wait(0.5)
    Client.speedupdate = backup.speed
    wait()
    end
    end
    end
    end    
})

       

local Tab = Window:MakeTab({
    Name = "Creators",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

local Section = Tab:AddSection({
    Name = "Sripter:"
})




local Section = Tab:AddSection({
    Name = "Discord PARADOGSSS#4969"
})

local Section = Tab:AddSection({
    Name = "the script was created"
})

local Section = Tab:AddSection({
    Name = "April 3, 2020, 19:34"
})
