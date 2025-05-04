-- Jamba Hub Mobile UI - Completo com ESP, Fly, Minimizar, Speed, etc.

-- Configs iniciais local player = game.Players.LocalPlayer local gui = player:WaitForChild("PlayerGui")

if gui:FindFirstChild("JambaHub") then gui.JambaHub:Destroy() end

local ScreenGui = Instance.new("ScreenGui") ScreenGui.Name = "JambaHub" ScreenGui.ResetOnSpawn = false ScreenGui.Parent = gui

local MainFrame = Instance.new("Frame") MainFrame.Name = "MainFrame" MainFrame.Size = UDim2.new(0, 500, 0, 300) MainFrame.Position = UDim2.new(0.2, 0, 0.2, 0) MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25) MainFrame.Parent = ScreenGui MainFrame.Active = true MainFrame.Draggable = true

local Title = Instance.new("TextLabel") Title.Size = UDim2.new(1, -40, 0, 30) Title.Position = UDim2.new(0, 0, 0, 0) Title.BackgroundColor3 = Color3.fromRGB(30, 30, 30) Title.Text = "Jamba Hub" Title.TextColor3 = Color3.new(1, 1, 1) Title.Font = Enum.Font.SourceSansBold Title.TextSize = 20 Title.Parent = MainFrame

local MinimizeButton = Instance.new("TextButton") MinimizeButton.Size = UDim2.new(0, 30, 0, 30) MinimizeButton.Position = UDim2.new(1, -30, 0, 0) MinimizeButton.Text = "–" MinimizeButton.TextColor3 = Color3.new(1, 1, 1) MinimizeButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60) MinimizeButton.Font = Enum.Font.SourceSansBold MinimizeButton.TextSize = 20 MinimizeButton.Parent = MainFrame

local minimized = false MinimizeButton.MouseButton1Click:Connect(function() minimized = not minimized for _, child in ipairs(MainFrame:GetChildren()) do if child ~= Title and child ~= MinimizeButton then child.Visible = not minimized end end end)

local SideMenu = Instance.new("Frame") SideMenu.Size = UDim2.new(0, 120, 1, -30) SideMenu.Position = UDim2.new(0, 0, 0, 30) SideMenu.BackgroundColor3 = Color3.fromRGB(35, 35, 35) SideMenu.Parent = MainFrame

local TabsFrame = Instance.new("Frame") TabsFrame.Size = UDim2.new(1, -120, 1, -30) TabsFrame.Position = UDim2.new(0, 120, 0, 30) TabsFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40) TabsFrame.Parent = MainFrame

local tabs = {"MAIN", "PLAYER", "VISUALS", "FLY", "TROLLS"} local tabFrames, tabButtons, activeTab = {}, {}, nil

for i, tab in ipairs(tabs) do local btn = Instance.new("TextButton") btn.Size = UDim2.new(1, 0, 0, 35) btn.Position = UDim2.new(0, 0, 0, (i - 1) * 35) btn.Text = tab btn.TextColor3 = Color3.new(1, 1, 1) btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50) btn.Font = Enum.Font.SourceSans btn.TextSize = 16 btn.Parent = SideMenu tabButtons[tab] = btn

local frame = Instance.new("Frame")
frame.Size = UDim2.new(1, 0, 1, 0)
frame.BackgroundTransparency = 1
frame.Visible = false
frame.Parent = TabsFrame
tabFrames[tab] = frame

btn.MouseButton1Click:Connect(function()
    if activeTab then tabFrames[activeTab].Visible = false end
    activeTab = tab
    tabFrames[activeTab].Visible = true
end)

end

-- ESP (VISUALS) local espEnabled = false local espObjects, espConns = {}, {} local btnESP = Instance.new("TextButton") btnESP.Size = UDim2.new(0, 150, 0, 40) btnESP.Position = UDim2.new(0, 20, 0, 20) btnESP.Text = "ESP OFF" btnESP.TextColor3 = Color3.new(1, 1, 1) btnESP.BackgroundColor3 = Color3.fromRGB(70, 70, 70) btnESP.Font = Enum.Font.SourceSansBold btnESP.TextSize = 18 btnESP.Parent = tabFrames["VISUALS"]

local function createESP(plr) if plr == player then return end local box = Drawing.new("Square") box.Size = Vector2.new(10, 20) box.Filled = false box.Color = Color3.new(1, 1, 1) box.Thickness = 1

local tracer = Drawing.new("Line")
tracer.Thickness = 1
tracer.Color = Color3.new(1, 1, 1)

espObjects[plr] = {box = box, tracer = tracer}

end

local function removeESP(plr) if espObjects[plr] then espObjects[plr].box:Remove() espObjects[plr].tracer:Remove() espObjects[plr] = nil end end

local function updateESP() for plr, objs in pairs(espObjects) do local char = plr.Character if char and char:FindFirstChild("HumanoidRootPart") then local pos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(char.HumanoidRootPart.Position) if onScreen then objs.box.Visible = true objs.box.Position = Vector2.new(pos.X - 5, pos.Y - 10) objs.tracer.Visible = true objs.tracer.From = Vector2.new(workspace.CurrentCamera.ViewportSize.X / 2, workspace.CurrentCamera.ViewportSize.Y) objs.tracer.To = Vector2.new(pos.X, pos.Y) else objs.box.Visible = false objs.tracer.Visible = false end else objs.box.Visible = false objs.tracer.Visible = false end end end

btnESP.MouseButton1Click:Connect(function() espEnabled = not espEnabled btnESP.Text = espEnabled and "ESP ON" or "ESP OFF" if espEnabled then for _, plr in pairs(game.Players:GetPlayers()) do createESP(plr) end table.insert(espConns, game.Players.PlayerAdded:Connect(createESP)) table.insert(espConns, game.Players.PlayerRemoving:Connect(removeESP)) table.insert(espConns, game:GetService("RunService").RenderStepped:Connect(updateESP)) else for _, c in pairs(espConns) do c:Disconnect() end for _, obj in pairs(espObjects) do obj.box:Remove(); obj.tracer:Remove() end espObjects, espConns = {}, {} end end)

-- Fly (FLY) local flying, speed = false, 50 local btnFly = Instance.new("TextButton") btnFly.Size = UDim2.new(0, 150, 0, 40) btnFly.Position = UDim2.new(0, 20, 0, 20) btnFly.Text = "Fly OFF" btnFly.TextColor3 = Color3.new(1, 1, 1) btnFly.BackgroundColor3 = Color3.fromRGB(70, 70, 70) btnFly.Font = Enum.Font.SourceSansBold btnFly.TextSize = 18 btnFly.Parent = tabFrames["FLY"]

local function setFly(state) flying = state btnFly.Text = flying and "Fly ON" or "Fly OFF" end

btnFly.MouseButton1Click:Connect(function() setFly(not flying) end)

game:GetService("RunService").RenderStepped:Connect(function() if flying then local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart") if hrp then hrp.Velocity = workspace.CurrentCamera.CFrame.LookVector * speed end end end)

-- Botões + e – para velocidade do Fly local btnPlus = Instance.new("TextButton") btnPlus.Size = UDim2.new(0, 40, 0, 40) btnPlus.Position = UDim2.new(0, 180, 0, 20) btnPlus.Text = "+" btnPlus.Parent = tabFrames["FLY"]

local btnMinus = Instance.new("TextButton") btnMinus.Size = UDim2.new(0, 40, 0, 40) btnMinus.Position = UDim2.new(0, 230, 0, 20) btnMinus.Text = "–" btnMinus.Parent = tabFrames["FLY"]

btnPlus.MouseButton1Click:Connect(function() speed = speed + 10 end) btnMinus.MouseButton1Click:Connect(function() speed = math.max(10, speed - 10) end)

-- Você pode seguir adicionando funções nas outras abas como PLAYER, MAIN, TROLLS etc. -- Se quiser que eu continue com essas abas, posso expandir esse script para você!

