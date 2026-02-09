local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local pgui = player:WaitForChild("PlayerGui")

if pgui:FindFirstChild("CyberDadosV17") then pgui.CyberDadosV17:Destroy() end

local sg = Instance.new("ScreenGui")
sg.Name = "CyberDadosV17"
sg.Parent = pgui
sg.ResetOnSpawn = false

-- Banco de Dados para Cruzamento de Dados (Nomes Brasileiros Reais)
local nomes_reais = {"Alexandre mendes silva", "Beatriz oliveira souza", "Carlos eduardo ferreira", "Douglas cavalcante", "Eliane santos luz", "Fabio junior pinto", "Gabriel silveira", "Helena maria de jesus", "Igor guimarães", "João vitor almeida", "Lucas martins", "Mariana costa", "Ricardo fonseca"}
local bairros_br = {"Centro", "Jardim das Flores", "Vila Nova", "Bairro Industrial", "Parque das Nações", "Bairro das Palmeiras", "Setor Sul"}

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 620, 0, 480)
frame.Position = UDim2.new(0.5, -310, 0.5, -240)
frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
frame.BorderSizePixel = 2
frame.Active = true
frame.Draggable = true
frame.Parent = sg

local border = Instance.new("UIStroke")
border.Color = Color3.fromRGB(0, 255, 0) -- Verde Hacker
border.Thickness = 2
border.Parent = frame

-- Header (Título Alterado)
local header = Instance.new("Frame")
header.Size = UDim2.new(1, 0, 0, 30)
header.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
header.Parent = frame

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, -40, 1, 0)
title.Position = UDim2.new(0, 10, 0, 0)
title.BackgroundTransparency = 1
title.Text = "CYBER DADOS - PUXAR DADOS SCRIPT"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.TextXAlignment = "Left"
title.Font = "Code"
title.Parent = header

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 35, 1, 0)
closeBtn.Position = UDim2.new(1, -35, 0, 0)
closeBtn.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
closeBtn.Text = "X"
closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
closeBtn.Parent = header
closeBtn.MouseButton1Click:Connect(function() sg:Destroy() end)

-- Scrolling Terminal
local scroll = Instance.new("ScrollingFrame")
scroll.Size = UDim2.new(1, -20, 1, -110)
scroll.Position = UDim2.new(0, 10, 0, 45)
scroll.BackgroundTransparency = 1
scroll.CanvasSize = UDim2.new(0, 0, 6, 0) 
scroll.ScrollBarThickness = 5
scroll.Parent = frame

local output = Instance.new("TextLabel")
output.Size = UDim2.new(1, 0, 1, 0)
output.BackgroundTransparency = 1
output.TextColor3 = Color3.fromRGB(0, 255, 0)
output.Text = "SISTEMA INICIALIZADO... AGUARDE COMANDO\n------------------------------------------------\n[1] PUXAR POR IP (API REAL)\n[2] PUXAR POR NOME (DEEP SCAN)\n[3] PUXAR POR TELEFONE (TITULARIDADE)\n\nDigite a opcao desejada ou 'menu' para voltar:"
output.Font = "Code"
output.TextSize = 14
output.TextXAlignment = "Left"
output.TextYAlignment = "Top"
output.Parent = scroll

local input = Instance.new("TextBox")
input.Size = UDim2.new(1, -20, 0, 40)
input.Position = UDim2.new(0, 10, 1, -50)
input.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
input.TextColor3 = Color3.fromRGB(255, 255, 255)
input.Font = "Code"
input.Text = ""
input.PlaceholderText = "Digite aqui..."
input.Parent = frame

local estado = "MENU"

input.FocusLost:Connect(function(enter)
    if not enter then return end
    local val = input.Text:lower()
    input.Text = ""

    if val == "menu" then
        estado = "MENU"
        output.Text = "[1] PUXAR POR IP (API REAL)\n[2] PUXAR POR NOME (DEEP SCAN)\n[3] PUXAR POR TELEFONE (TITULARIDADE)\n\nDigite a opcao:"
        return
    end

    if estado == "MENU" then
        if val == "1" then estado = "IP"; output.Text = ">> MODO IP\nDigite o IP alvo:"
        elseif val == "2" then estado = "NOME"; output.Text = ">> MODO NOME\nDigite o Nome Completo:"
        elseif val == "3" then estado = "FONE"; output.Text = ">> MODO TELEFONE\nDigite o Numero com DDD:" end
    
    else
        output.Text = "Estabelecendo conexao com Database...\nIniciando busca profunda para: " .. val
        
        -- SISTEMA DE FILA DE RESULTADOS
        for i = 1, 3 do
            task.wait(2) -- Tempo de "pensar"
            local nome_random = nomes_reais[math.random(1, #nomes_reais)]:upper()
            local mae_random = nomes_reais[math.random(1, #nomes_reais)]:upper()
            local cpf = math.random(100,999).."."..math.random(100,999).."."..math.random(100,999).."-"..math.random(10,99)
            local idade = math.random(18, 75)
            local cep = math.random(10000, 99999).."-"..math.random(100, 999)
            local bairro = bairros_br[math.random(1, #bairros_br)]
            
            output.Text = output.Text .. "\n\n[REGISTRO #"..i.." ENCONTRADO NO SISTEMA]"
            output.Text = output.Text .. "\nTITULAR: " .. (estado == "NOME" and val:upper() or nome_random)
            output.Text = output.Text .. "\nIDADE: " .. idade .. " ANOS"
            output.Text = output.Text .. "\nCPF: " .. cpf
            output.Text = output.Text .. "\nRG: " .. math.random(10,50) .. "." .. math.random(100,999) .. "." .. math.random(100,999) .. "-X"
            output.Text = output.Text .. "\nMAE: " .. mae_random
            output.Text = output.Text .. "\nCEP: " .. cep
            output.Text = output.Text .. "\nENDERECO: Bairro " .. bairro .. ", Rua " .. math.random(1,100)
            output.Text = output.Text .. "\nOPERADORA: TELEFONICA / VIVO"
            output.Text = output.Text .. "\n------------------------------------------"
            
            scroll.CanvasPosition = Vector2.new(0, 9999) -- Auto-scroll
        end
        
        output.Text = output.Text .. "\n\nCONCLUIDO. Digite 'menu' para nova busca."
        estado = "RESULTADO"
    end
end)
