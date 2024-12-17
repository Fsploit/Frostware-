# parry used in this project

note, please credit The original creator of this script, ive only modified dead AutoParry codes.

```
-- Updated and coded by Fsploit
getgenv().Paws = {
    ["AutoParry"] = true,
    ["PingBased"] = true,
    ["PingBasedOffset"] = 0,
    ["DistanceToParry"] = 0.5,
    ["BallSpeedCheck"] = true,
    ["SpamDelay"] = 0.1, -- Delay between spam clicks
}

local Players = game:GetService("Players")
local Player = Players.LocalPlayer or Players.PlayerAdded:Wait()
local ReplicatedPaw = game:GetService("ReplicatedStorage")
local VirtualInputManager = game:GetService("VirtualInputManager")

local Paws = ReplicatedPaw:WaitForChild("Remotes", 9e9)
local PawsBalls = workspace:WaitForChild("Balls", 9e9)
local PawsTable = getgenv().Paws

local function IsTheTarget()
    return Player.Character:FindFirstChild("Highlight")
end

local function FindBall()
    for _, v in pairs(PawsBalls:GetChildren()) do
        if v:GetAttribute("realBall") == true then
            return v
        end
    end
end

local function SendMouseClick(spamDelay)
    VirtualInputManager:SendMouseButtonEvent(0, 0, 0, true, game, spamDelay)
    VirtualInputManager:SendMouseButtonEvent(0, 0, 0, false, game, spamDelay)
end

game:GetService("RunService").PreRender:Connect(function()
    if not FindBall() then return end

    local Ball = FindBall()
    local BallPosition = Ball.Position
    local BallVelocity = Ball.AssemblyLinearVelocity.Magnitude
    local Distance = Player:DistanceFromCharacter(BallPosition)
    local Ping = BallVelocity * (game.Stats.Network.ServerStatsItem["Data Ping"]:GetValue() / 1000)

    if PawsTable.PingBased then
        Distance -= Ping + PawsTable.PingBasedOffset
    end

    if PawsTable.BallSpeedCheck and BallVelocity == 0 then return end

    if (Distance / BallVelocity) <= PawsTable.DistanceToParry and IsTheTarget() and PawsTable.AutoParry then
        SendMouseClick(PawsTable.SpamDelay)
    end
end)

task.spawn(function()
    while task.wait() do
        if getgenv().DetectSpam then
            local Ball = FindBall()
            if Ball then
                local TargetPlayer = Ball:GetAttribute("target")
                if TargetPlayer == Player.Name or (Player.Character and Player.Character:FindFirstChild("Highlight")) then
                    SendMouseClick(PawsTable.SpamDelay)
                end
            end
        end
    end
end)
```
