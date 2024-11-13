-- config
getgenv().vipmode = false
getgenv().diamonds = 10000000000
getgenv().username = 'petsgo2002'
getgenv().magicshard = 1000
getgenv().diamondshard = 9000000
--
repeat wait() until game.Players
repeat wait() until game.Players.LocalPlayer
repeat wait() until game.ReplicatedStorage
repeat wait() until game:GetService("Players").LocalPlayer:FindFirstChild("PlayerGui")
repeat wait() until game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Main")
local vu = game:GetService("VirtualUser")
local plr = game.Players.LocalPlayer
local Client = require(game.ReplicatedStorage.Library.Client)
game:GetService("StarterPlayer").StarterPlayerScripts.Scripts.Core["Idle Tracking"].Disabled = true
function checkmail()
    local tb = {
        [1] = {
    
        }
    }
    local a = game:GetService("ReplicatedStorage").Network["Mailbox: Get"]:InvokeServer()
    if type(a) == 'table' then
        if a.Inbox then
            for i,v in pairs(a.Inbox) do
                table.insert(tb[1], v.uuid)
            end
        end
    end
    if #tb[1] > 0 then
        return tb
    else
        return false
    end
end

if game.PlaceId == 8737899170 then
    print(Client.TradingPlazaCmds.RequestTradingPlaza())
end

if getgenv().vipmode and game.PlaceId == 15502339080 then
    local ts = game:GetService("TeleportService")
    local p = game:GetService("Players").LocalPlayer
    ts:Teleport(15588442388, p)
end

function getmybooth()
    for i,v in pairs(game:GetService("Workspace")["__THINGS"].Booths:GetChildren()) do
        if v.Info:FindFirstChild('BoothBottom') then
            if string.find(v.Info.BoothBottom.Frame.Top.Text, game.Players.LocalPlayer.Name) then
                return v.Info
            end
        end
    end
    return false
end

function getindex()
    local num = 0
    for i,v in pairs(getmybooth():GetChildren()) do
        if v:IsA("Frame") then
            num = num + 1
        end
    end
    if num < 4 then
        return false
    else
        return true
    end
end

function getshard()
    for i,v in pairs(Client.InventoryCmds.State().container._store._byType.Misc._byUID) do
        if v._data.id == 'Magic Shard' and v._data._am ~= nil then
            if v._data._am >= getgenv().magicshard then
                return i 
            end
        end
    end
    return false
end

function getcoin()
    local a = Client.Save.Get()
    for i,v in pairs(a.Inventory.Currency) do
        if type(v) == "table" and v.id == "Diamonds" then
            return {v._am , i}
        end
    end
    return 0
end

getgenv().mybooth = 0

spawn(function()
    while true do wait(5)
        game:GetService("ReplicatedStorage").Network:FindFirstChild("Idle Tracking: Stop Timer"):FireServer()
    end
end)

function jump()
    game:GetService("VirtualInputManager"):SendKeyEvent(true, 32, false, game)
    wait(4)
    game:GetService("VirtualInputManager"):SendKeyEvent(false, 32, false, game)
end

spawn(function()
    while true do wait()
        pcall(function()
            local tickm = tick()
            repeat wait()
            until tick() - tickm >= 30
            jump()
        end)
    end
end)

while true do wait(15)
    local a, b = pcall(function()
        print(getcoin()[1])
        if getcoin()[1] >= getgenv().diamonds + 10000 then
            wait(5)
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-944.0957641601562, 283.9658508300781, -2273.31494140625)
            wait(2)
            local args = {
                [1] = getgenv().username,
                [2] = "quanhsendgemne",
                [3] = "Currency",
                [4] = getcoin()[2],
                [5] = getgenv().diamonds
            }
            game:GetService("ReplicatedStorage").Network:FindFirstChild("Mailbox: Send"):InvokeServer(unpack(args))
            return
        end
        if getmybooth() ~= false then
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = getmybooth().CFrame * CFrame.new(0,5,0)
            if getshard() and getindex() == false then
                local args = {
                    [1] = getshard(),
                    [2] = getgenv().diamondshard,
                    [3] = getgenv().magicshard
                }
                
                game:GetService("ReplicatedStorage").Network.Booths_CreateListing:InvokeServer(unpack(args))
            end
        else
            print('wtf')
            local a,b = game:GetService("ReplicatedStorage").Network["Booths_ClaimAnyBooth"]:InvokeServer()
            if a == false then
                wait(1)
                Client.TradingPlazaCmds.RequestTradingPlaza()
            end
            getgenv().mybooth = b
            print(getgenv().mybooth)
        end
    end)
    print(a, b)
end
