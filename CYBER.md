local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local pgui = player:WaitForChild("PlayerGui")

-- Limpa a UI antiga para não sobrepor
if pgui:FindFirstChild("CyberDadosV17") then pgui.CyberDadosV17:Destroy() end

local sg = Instance.new("ScreenGui", pgui)
sg.Name = "CyberDadosV17"
sg.ResetOnSpawn = false

local frame = Instance.new("Frame", sg)
frame.Size = UDim2.new(0, 620, 0, 480)
frame.Position = UDim2.new(0.5, -310, 0.5, -240)
frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
frame.BorderSizePixel = 2
frame.Active = true
frame.Draggable = true

local border = Instance.new("UIStroke", frame)
border.Color = Color3.fromRGB(0, 255, 0) 
border.Thickness = 2

local header = Instance.new("Frame", frame)
header.Size = UDim2.new(1, 0, 0, 30)
header.BackgroundColor3 = Color3.fromRGB(30, 30, 30)

local title = Instance.new("TextLabel", header)
title.Size = UDim2.new(1, -40, 1, 0)
title.Position = UDim2.new(0, 10, 0, 0)
title.BackgroundTransparency = 1
title.Text = "CYBER DADOS - XENO REAL SCANNER"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = "Code"
title.TextXAlignment = "Left"

local closeBtn = Instance.new("TextButton", header)
closeBtn.Size = UDim2.new(0, 35, 1, 0)
closeBtn.Position = UDim2.new(1, -35, 0, 0)
closeBtn.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
closeBtn.Text = "X"
closeBtn.MouseButton1Click:Connect(function() sg:Destroy() end)

local scroll = Instance.new("ScrollingFrame", frame)
scroll.Size = UDim2.new(1, -20, 1, -110)
scroll.Position = UDim2.new(0, 10, 0, 45)
scroll.BackgroundTransparency = 1
scroll.CanvasSize = UDim2.new(0, 0, 10, 0) 
scroll.ScrollBarThickness = 5

local output = Instance.new("TextLabel", scroll)
output.Size = UDim2.new(1, 0, 1, 0)
output.BackgroundTransparency = 1
output.TextColor3 = Color3.fromRGB(0, 255, 0)
output.Text = "SISTEMA INICIALIZADO...\n[1] PUXAR POR IP (API REAL)\n[2] PUXAR POR NOME (REDE SOCIAL)\n\nDigite a opcao:"
output.Font = "Code"
output.TextSize = 14
output.TextXAlignment = "Left"
output.TextYAlignment = "Top"

local input = Instance.new("TextBox", frame)
input.Size = UDim2.new(1, -20, 0, 40)
input.Position = UDim2.new(0, 10, 1, -50)
input.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
input.TextColor3 = Color3.fromRGB(255, 255, 255)
input.Font = "Code"
input.PlaceholderText = "Digite aqui..."

local estado = "MENU"

-- VOLTANDO O SISTEMA DE IP QUE FUNCIONOU NO XENO
local function RequestRealIP(target_ip)
    local httpRequest = (http and http.request) or (http_request) or (request) or (syn and syn.request)
    if not httpRequest then return nil, "Executor sem suporte" end

    local success, response = pcall(function()
        return httpRequest({
            Url = "http://ip-api.com/json/" .. target_ip .. "?fields=66846719",
            Method = "GET",
            Headers = {["Content-Type"] = "application/json"}
        })
    end)

    if success and response then
        local body = response.Body or response.Data
        return HttpService:JSONDecode(body), nil
    end
    return nil, "Erro na resposta do executor"
end

input.FocusLost:Connect(function(enter)
    if not enter then return end
    local val = input.Text:gsub("%s+", "") -- Limpa espaços
    input.Text = ""

    if val:lower() == "menu" then
        estado = "MENU"
        output.Text = "[1] PUXAR POR IP (REAL)\n[2] PUXAR POR NOME (REAIS)\n\nDigite a opcao:"
        return
    end

    if estado == "MENU" then
        if val == "1" then estado = "IP"; output.Text = ">> MODO IP REAL\nDigite o IP:"
        elseif val == "2" then estado = "NOME"; output.Text = ">> MODO NOME\nDigite o Nome Completo:" end
    else
        output.Text = "Iniciando varredura...\nAlvo: " .. val .. "\n"
        
        if estado == "IP" then
            local data, err = RequestRealIP(val)
            if data and data.status == "success" then
                output.Text = output.Text .. "\n[DADOS LOCALIZADOS]"
                output.Text = output.Text .. "\nIP: " .. data.query
                output.Text = output.Text .. "\nPAIS: " .. data.country
                output.Text = output.Text .. "\nESTADO: " .. data.regionName
                output.Text = output.Text .. "\nCIDADE: " .. data.city
                output.Text = output.Text .. "\nOPERADORA: " .. data.isp
                output.Text = output.Text .. "\nCOORDENADAS: " .. data.lat .. ", " .. data.lon
                output.Text = output.Text .. "\n------------------------------------------"
            else
                output.Text = output.Text .. "\n[ERRO]: " .. (err or "IP Invalido")
            end
        elseif estado == "NOME" then
            local search = val:gsub(" ", "+")
            output.Text = output.Text .. "\n[VARREDURA OSINT FINALIZADA]"
            output.Text = output.Text .. "\nPERFIL FACEBOOK: https://www.facebook.com/public/" .. search
            output.Text = output.Text .. "\nBUSCA LINKEDIN: https://www.google.com/search?q=site:linkedin.com/in/+" .. search
            output.Text = output.Text .. "\n------------------------------------------"
            output.Text = output.Text .. "\n[AVISO]: Dados civis (CPF/RG) sao protegidos por lei e nao ficam expostos em APIs gratuitas."
        end
        
        output.Text = output.Text .. "\n\nDigite 'menu' para nova busca."
        scroll.CanvasPosition = Vector2.new(0, 9999)
        estado = "RESULTADO"
    end
end)
