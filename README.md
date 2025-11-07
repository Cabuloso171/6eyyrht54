local imgui = require "mimgui"
local faicons = require "fAwesome6"
local json = require "json"
local a = require 'lib.samp.events'

local imageConfig = {
    enabled = imgui.new.bool(false),
    x = imgui.new.int(100),
    y = imgui.new.int(100),
    width = imgui.new.int(200),
    height = imgui.new.int(200),
    texture = nil,
    texture2 = nil,
    path = getWorkingDirectory() .. "/lib/Voip/shellder.png",
    path2 = getWorkingDirectory() .. "/lib/Voip/shellder2.png",
    currentTexture = nil,
    voipActive = false
}

local buttonConfig = {
    enabled = imgui.new.bool(true),
    x = imgui.new.int(50),
    y = imgui.new.int(50),
    size = imgui.new.int(60),
    color = imgui.new.float[4](0.0, 0.0, 0.0, 0.5),
    hoverColor = imgui.new.float[4](0.0, 0.0, 0.0, 0.7),
    activeColor = imgui.new.float[4](0.0, 0.0, 0.0, 0.9)
}

local function loadImageTexture()
    if doesFileExist(imageConfig.path) then
        if imageConfig.texture then
            imgui.DestroyTexture(imageConfig.texture)
        end
        imageConfig.texture = imgui.CreateTextureFromFile(imageConfig.path)
    end
    
    if doesFileExist(imageConfig.path2) then
        if imageConfig.texture2 then
            imgui.DestroyTexture(imageConfig.texture2)
        end
        imageConfig.texture2 = imgui.CreateTextureFromFile(imageConfig.path2)
    end
    
    imageConfig.currentTexture = imageConfig.texture
end

local function toggleVoip()
    imageConfig.voipActive = not imageConfig.voipActive
    if imageConfig.voipActive then
        imageConfig.currentTexture = imageConfig.texture2
    else
        imageConfig.currentTexture = imageConfig.texture
    end
end

local vida = 100.0
local colete = 100.0
local dinheiro = 0
local editMode = imgui.new.bool(false)

local shellderTextOpacity = imgui.new.float(1.0)
local shellderFadeDirection = 1
local shellderColorIndex = 1
local shellderColorList = {
    {0.8, 0.8, 0.8, 1.0},
    {1.0, 1.0, 1.0, 1.0},
    {0.6, 0.4, 0.2, 1.0},
    {0.0, 1.0, 0.0, 1.0},
    {1.0, 0.0, 0.0, 1.0},
    {1.0, 0.0, 1.0, 1.0}
}

local miraConfig = {
    enabled = true,
    x = 0,
    y = 0,
    size = 10.0,
    width = 2.0,
    color = {1.0, 1.0, 1.0, 1.0},
    borderEnabled = true,
    borderSize = 1.0,
    borderColor = {0.0, 0.0, 0.0, 1.0},
    tipo = 1
}

local miraTipos = {
    "Cruz Simples",
    "Cruz Dupla", 
    "Circulo",
    "Quadrado",
    "X",
    "Ponto",
    "Alvo",
    "Diamante",
    "Estrela",
    "Triangulo"
}

local config = {
    vidaX = 1479,
    vidaY = 92,
    coleteX = 1479,
    coleteY = 134,
    moneyX = 1551,
    moneyY = 175,
    vidaWidth = 50,
    vidaHeight = 29,
    coleteWidth = 50,
    coleteHeight = 29,
    moneySize = 63.0,
    moneyBorderSize = 1.0,
    borderRadius = 6.8,
    vidaColor = {0.1, 0.3, 0.8, 1.0},
    coleteColor = {0.6, 0.6, 0.6, 1.0},
    moneyColor = {1.0, 1.0, 1.0, 1.0},
    moneyBorderColor = {0.0, 0.0, 0.0, 1.0},
    iconOffsetX = -35,
    iconSize = 18.0,
    fontColor = {1.0, 1.0, 1.0, 1.0},
    hpFontSize = 13.0,
    apFontSize = 13.0,
    moneyFontSize = 13.0,
    borderEnabled = true,
    borderSize = 2.0,
    borderColor = {1.0, 1.0, 1.0, 1.0},
    miraEnabled = miraConfig.enabled,
    miraX = miraConfig.x,
    miraY = miraConfig.y,
    miraSize = miraConfig.size,
    miraWidth = miraConfig.width,
    miraColor = {miraConfig.color[1], miraConfig.color[2], miraConfig.color[3], miraConfig.color[4]},
    miraBorderEnabled = miraConfig.borderEnabled,
    miraBorderSize = miraConfig.borderSize,
    miraBorderColor = {miraConfig.borderColor[1], miraConfig.borderColor[2], miraConfig.borderColor[3], miraConfig.borderColor[4]},
    miraTipo = miraConfig.tipo,
    
    timeId = 12,
    weatherId = 0,
    isWeatherActive = false,
    isFakePcActive = false,
    fovEnabled = false,
    fovValue = 60,
    
    imageEnabled = false,
    imageX = 100,
    imageY = 100,
    imageWidth = 200,
    imageHeight = 200,
    
    buttonEnabled = true,
    buttonX = 50,
    buttonY = 50,
    buttonSize = 60,
    buttonColor = {0.0, 0.0, 0.0, 0.5},
    buttonHoverColor = {0.0, 0.0, 0.0, 0.7},
    buttonActiveColor = {0.0, 0.0, 0.0, 0.9}
}

local vidaX = imgui.new.int(config.vidaX)
local vidaY = imgui.new.int(config.vidaY)
local vidaWidth = imgui.new.int(config.vidaWidth)
local vidaHeight = imgui.new.int(config.vidaHeight)
local coleteX = imgui.new.int(config.coleteX)
local coleteY = imgui.new.int(config.coleteY)
local coleteWidth = imgui.new.int(config.coleteWidth)
local coleteHeight = imgui.new.int(config.coleteHeight)
local moneyX = imgui.new.int(config.moneyX)
local moneyY = imgui.new.int(config.moneyY)
local moneySize = imgui.new.float(config.moneySize)
local moneyBorderSize = imgui.new.float(config.moneyBorderSize)
local borderRadius = imgui.new.float(config.borderRadius)
local hpFontSize = imgui.new.float(config.hpFontSize)
local apFontSize = imgui.new.float(config.apFontSize)
local moneyFontSize = imgui.new.float(config.moneyFontSize)

local borderEnabled = imgui.new.bool(config.borderEnabled)
local borderSize = imgui.new.float(config.borderSize)
local borderColor = imgui.new.float[4](config.borderColor[1], config.borderColor[2], config.borderColor[3], config.borderColor[4])

local miraEnabled = imgui.new.bool(config.miraEnabled)
local miraX = imgui.new.int(config.miraX)
local miraY = imgui.new.int(config.miraY)
local miraSize = imgui.new.float(config.miraSize)
local miraWidth = imgui.new.float(config.miraWidth)
local miraColor = imgui.new.float[4](config.miraColor[1], config.miraColor[2], config.miraColor[3], config.miraColor[4])
local miraBorderEnabled = imgui.new.bool(config.miraBorderEnabled)
local miraBorderSize = imgui.new.float(config.miraBorderSize)
local miraBorderColor = imgui.new.float[4](config.miraBorderColor[1], config.miraBorderColor[2], config.miraBorderColor[3], config.miraBorderColor[4])
local miraTipo = imgui.new.int(config.miraTipo)

local vidaColor = imgui.new.float[4](config.vidaColor[1], config.vidaColor[2], config.vidaColor[3], config.vidaColor[4])
local coleteColor = imgui.new.float[4](config.coleteColor[1], config.coleteColor[2], config.coleteColor[3], config.coleteColor[4])
local moneyColor = imgui.new.float[4](config.moneyColor[1], config.moneyColor[2], config.moneyColor[3], config.moneyColor[4])
local moneyBorderColor = imgui.new.float[4](config.moneyBorderColor[1], config.moneyBorderColor[2], config.moneyBorderColor[3], config.moneyBorderColor[4])
local fontColor = imgui.new.float[4](config.fontColor[1], config.fontColor[2], config.fontColor[3], config.fontColor[4])

local moveTogether = imgui.new.bool(true)
local moveFontsTogether = imgui.new.bool(true)
local moneyFont = nil
local hpFont = nil
local apFont = nil

local timeId = imgui.new.int(config.timeId)
local weatherId = imgui.new.int(config.weatherId)
local isWeatherActive = imgui.new.bool(config.isWeatherActive)

local isFakePcActive = imgui.new.bool(config.isFakePcActive)

local fovEnabled = imgui.new.bool(config.fovEnabled)
local fovValue = imgui.new.int(config.fovValue)

local ffi = require("ffi")
local gtasa = ffi.load("GTASA")
ffi.cdef("void _Z12AND_OpenLinkPKc(const char* link);")

local function openLink(link)
    gtasa._Z12AND_OpenLinkPKc(link)
end

local function rgba(r, g, b, a)
    return imgui.ColorConvertFloat4ToU32(imgui.ImVec4(r, g, b, a))
end

local function formatMoney(amount)
    local formatted = tostring(amount)
    local k
    while true do
        formatted, k = string.gsub(formatted, "^(-?%d+)(%d%d%d)", '%1.%2')
        if k == 0 then break end
    end
    return formatted
end

local function loadConfig()
    local filePath = "/storage/emulated/0/.hudeditshellderwsw.json"
    if doesFileExist(filePath) then
        local file = io.open(filePath, "r")
        if file then
            local content = file:read("*a")
            file:close()
            local saved = json.decode(content)
            if saved then
                vidaX[0] = saved.vidaX or config.vidaX
                vidaY[0] = saved.vidaY or config.vidaY
                vidaWidth[0] = saved.vidaWidth or config.vidaWidth
                vidaHeight[0] = saved.vidaHeight or config.vidaHeight
                coleteX[0] = saved.coleteX or config.coleteX
                coleteY[0] = saved.coleteY or config.coleteY
                coleteWidth[0] = saved.coleteWidth or config.coleteWidth
                coleteHeight[0] = saved.coleteHeight or config.coleteHeight
                moneyX[0] = saved.moneyX or config.moneyX
                moneyY[0] = saved.moneyY or config.moneyY
                moneySize[0] = saved.moneySize or config.moneySize
                moneyBorderSize[0] = saved.moneyBorderSize or config.moneyBorderSize
                borderRadius[0] = saved.borderRadius or config.borderRadius
                hpFontSize[0] = saved.hpFontSize or config.hpFontSize
                apFontSize[0] = saved.apFontSize or config.apFontSize
                moneyFontSize[0] = saved.moneyFontSize or config.moneyFontSize
                
                borderEnabled[0] = saved.borderEnabled ~= nil and saved.borderEnabled or config.borderEnabled
                borderSize[0] = saved.borderSize or config.borderSize
                
                miraEnabled[0] = saved.miraEnabled ~= nil and saved.miraEnabled or config.miraEnabled
                miraX[0] = saved.miraX or config.miraX
                miraY[0] = saved.miraY or config.miraY
                miraSize[0] = saved.miraSize or config.miraSize
                miraWidth[0] = saved.miraWidth or config.miraWidth
                miraBorderEnabled[0] = saved.miraBorderEnabled ~= nil and saved.miraBorderEnabled or config.miraBorderEnabled
                miraBorderSize[0] = saved.miraBorderSize or config.miraBorderSize
                miraTipo[0] = saved.miraTipo or config.miraTipo

                timeId[0] = saved.timeId or config.timeId
                weatherId[0] = saved.weatherId or config.weatherId
                isWeatherActive[0] = saved.isWeatherActive ~= nil and saved.isWeatherActive or config.isWeatherActive
                isFakePcActive[0] = saved.isFakePcActive ~= nil and saved.isFakePcActive or config.isFakePcActive
                fovEnabled[0] = saved.fovEnabled ~= nil and saved.fovEnabled or config.fovEnabled
                fovValue[0] = saved.fovValue or config.fovValue
                
                imageConfig.enabled[0] = saved.imageEnabled or config.imageEnabled
                imageConfig.x[0] = saved.imageX or config.imageX
                imageConfig.y[0] = saved.imageY or config.imageY
                imageConfig.width[0] = saved.imageWidth or config.imageWidth
                imageConfig.height[0] = saved.imageHeight or config.imageHeight
                
                buttonConfig.enabled[0] = saved.buttonEnabled ~= nil and saved.buttonEnabled or config.buttonEnabled
                buttonConfig.x[0] = saved.buttonX or config.buttonX
                buttonConfig.y[0] = saved.buttonY or config.buttonY
                buttonConfig.size[0] = saved.buttonSize or config.buttonSize
                
                if saved.vidaColor then
                    vidaColor[0] = saved.vidaColor[1] or config.vidaColor[1]
                    vidaColor[1] = saved.vidaColor[2] or config.vidaColor[2]
                    vidaColor[2] = saved.vidaColor[3] or config.vidaColor[3]
                    vidaColor[3] = saved.vidaColor[4] or config.vidaColor[4]
                end
                
                if saved.coleteColor then
                    coleteColor[0] = saved.coleteColor[1] or config.coleteColor[1]
                    coleteColor[1] = saved.coleteColor[2] or config.coleteColor[2]
                    coleteColor[2] = saved.coleteColor[3] or config.coleteColor[3]
                    coleteColor[3] = saved.coleteColor[4] or config.coleteColor[4]
                end
                
                if saved.moneyColor then
                    moneyColor[0] = saved.moneyColor[1] or config.moneyColor[1]
                    moneyColor[1] = saved.moneyColor[2] or config.moneyColor[2]
                    moneyColor[2] = saved.moneyColor[3] or config.moneyColor[3]
                    moneyColor[3] = saved.moneyColor[4] or config.moneyColor[4]
                end
                
                if saved.moneyBorderColor then
                    moneyBorderColor[0] = saved.moneyBorderColor[1] or config.moneyBorderColor[1]
                    moneyBorderColor[1] = saved.moneyBorderColor[2] or config.moneyBorderColor[2]
                    moneyBorderColor[2] = saved.moneyBorderColor[3] or config.moneyBorderColor[3]
                    moneyBorderColor[3] = saved.moneyBorderColor[4] or config.moneyBorderColor[4]
                end
                
                if saved.fontColor then
                    fontColor[0] = saved.fontColor[1] or config.fontColor[1]
                    fontColor[1] = saved.fontColor[2] or config.fontColor[2]
                    fontColor[2] = saved.fontColor[3] or config.fontColor[3]
                    fontColor[3] = saved.fontColor[4] or config.fontColor[4]
                end
                
                if saved.borderColor then
                    borderColor[0] = saved.borderColor[1] or config.borderColor[1]
                    borderColor[1] = saved.borderColor[2] or config.borderColor[2]
                    borderColor[2] = saved.borderColor[3] or config.borderColor[3]
                    borderColor[3] = saved.borderColor[4] or config.borderColor[4]
                end
                
                if saved.miraColor then
                    miraColor[0] = saved.miraColor[1] or config.miraColor[1]
                    miraColor[1] = saved.miraColor[2] or config.miraColor[2]
                    miraColor[2] = saved.miraColor[3] or config.miraColor[3]
                    miraColor[3] = saved.miraColor[4] or config.miraColor[4]
                end
                
                if saved.miraBorderColor then
                    miraBorderColor[0] = saved.miraBorderColor[1] or config.miraBorderColor[1]
                    miraBorderColor[1] = saved.miraBorderColor[2] or config.miraBorderColor[2]
                    miraBorderColor[2] = saved.miraBorderColor[3] or config.miraBorderColor[3]
                    miraBorderColor[3] = saved.miraBorderColor[4] or config.miraBorderColor[4]
                end
                
                if saved.buttonColor then
                    buttonConfig.color[0] = saved.buttonColor[1] or config.buttonColor[1]
                    buttonConfig.color[1] = saved.buttonColor[2] or config.buttonColor[2]
                    buttonConfig.color[2] = saved.buttonColor[3] or config.buttonColor[3]
                    buttonConfig.color[3] = saved.buttonColor[4] or config.buttonColor[4]
                end
                
                if saved.buttonHoverColor then
                    buttonConfig.hoverColor[0] = saved.buttonHoverColor[1] or config.buttonHoverColor[1]
                    buttonConfig.hoverColor[1] = saved.buttonHoverColor[2] or config.buttonHoverColor[2]
                    buttonConfig.hoverColor[2] = saved.buttonHoverColor[3] or config.buttonHoverColor[3]
                    buttonConfig.hoverColor[3] = saved.buttonHoverColor[4] or config.buttonHoverColor[4]
                end
                
                if saved.buttonActiveColor then
                    buttonConfig.activeColor[0] = saved.buttonActiveColor[1] or config.buttonActiveColor[1]
                    buttonConfig.activeColor[1] = saved.buttonActiveColor[2] or config.buttonActiveColor[2]
                    buttonConfig.activeColor[2] = saved.buttonActiveColor[3] or config.buttonActiveColor[3]
                    buttonConfig.activeColor[3] = saved.buttonActiveColor[4] or config.buttonActiveColor[4]
                end
                
                for key, value in pairs(saved) do
                    if key ~= "borderEnabled" and key ~= "miraEnabled" and key ~= "miraBorderEnabled" and key ~= "isWeatherActive" and key ~= "isFakePcActive" and key ~= "fovEnabled" and key ~= "imageEnabled" and key ~= "buttonEnabled" then
                        config[key] = value
                    end
                end
            end
        end
    end
end

local function saveConfig()
    config.vidaX = vidaX[0]
    config.vidaY = vidaY[0]
    config.vidaWidth = vidaWidth[0]
    config.vidaHeight = vidaHeight[0]
    config.coleteX = coleteX[0]
    config.coleteY = coleteY[0]
    config.coleteWidth = coleteWidth[0]
    config.coleteHeight = coleteHeight[0]
    config.moneyX = moneyX[0]
    config.moneyY = moneyY[0]
    config.moneySize = moneySize[0]
    config.moneyBorderSize = moneyBorderSize[0]
    config.borderRadius = borderRadius[0]
    config.hpFontSize = hpFontSize[0]
    config.apFontSize = apFontSize[0]
    config.moneyFontSize = moneyFontSize[0]
    
    config.borderEnabled = borderEnabled[0]
    config.borderSize = borderSize[0]
    config.borderColor = {borderColor[0], borderColor[1], borderColor[2], borderColor[3]}
    
    config.miraEnabled = miraEnabled[0]
    config.miraX = miraX[0]
    config.miraY = miraY[0]
    config.miraSize = miraSize[0]
    config.miraWidth = miraWidth[0]
    config.miraColor = {miraColor[0], miraColor[1], miraColor[2], miraColor[3]}
    config.miraBorderEnabled = miraBorderEnabled[0]
    config.miraBorderSize = miraBorderSize[0]
    config.miraBorderColor = {miraBorderColor[0], miraBorderColor[1], miraBorderColor[2], miraBorderColor[3]}
    config.miraTipo = miraTipo[0]
    
    config.vidaColor = {vidaColor[0], vidaColor[1], vidaColor[2], vidaColor[3]}
    config.coleteColor = {coleteColor[0], coleteColor[1], coleteColor[2], coleteColor[3]}
    config.moneyColor = {moneyColor[0], moneyColor[1], moneyColor[2], moneyColor[3]}
    config.moneyBorderColor = {moneyBorderColor[0], moneyBorderColor[1], moneyBorderColor[2], moneyBorderColor[3]}
    config.fontColor = {fontColor[0], fontColor[1], fontColor[2], fontColor[3]}

    config.timeId = timeId[0]
    config.weatherId = weatherId[0]
    config.isWeatherActive = isWeatherActive[0]
    config.isFakePcActive = isFakePcActive[0]
    config.fovEnabled = fovEnabled[0]
    config.fovValue = fovValue[0]
    
    config.imageEnabled = imageConfig.enabled[0]
    config.imageX = imageConfig.x[0]
    config.imageY = imageConfig.y[0]
    config.imageWidth = imageConfig.width[0]
    config.imageHeight = imageConfig.height[0]
    
    config.buttonEnabled = buttonConfig.enabled[0]
    config.buttonX = buttonConfig.x[0]
    config.buttonY = buttonConfig.y[0]
    config.buttonSize = buttonConfig.size[0]
    config.buttonColor = {buttonConfig.color[0], buttonConfig.color[1], buttonConfig.color[2], buttonConfig.color[3]}
    config.buttonHoverColor = {buttonConfig.hoverColor[0], buttonConfig.hoverColor[1], buttonConfig.hoverColor[2], buttonConfig.hoverColor[3]}
    config.buttonActiveColor = {buttonConfig.activeColor[0], buttonConfig.activeColor[1], buttonConfig.activeColor[2], buttonConfig.activeColor[3]}
    
    local filePath = "/storage/emulated/0/.hudeditshellderwsw.json"
    local file = io.open(filePath, "w")
    if file then
        file:write(json.encode(config))
        file:close()
    end
end

imgui.OnInitialize(function()
    local conf = imgui.ImFontConfig()
    conf.MergeMode = true
    conf.PixelSnapH = true
    local iconRanges = imgui.new.ImWchar[3](faicons.min_range, faicons.max_range, 0)
    imgui.GetIO().Fonts:AddFontFromMemoryCompressedBase85TTF(
        faicons.get_font_data_base85("Solid"), 22, conf, iconRanges
    )
    
    moneyFont = imgui.GetIO().Fonts:AddFontDefault()
    hpFont = imgui.GetIO().Fonts:AddFontDefault()
    apFont = imgui.GetIO().Fonts:AddFontDefault()
    imgui.GetIO().Fonts:Build()
    
    loadImageTexture()
end)

local lastClickTime = 0
local clickCooldown = 300

imgui.OnFrame(function() return true end, function()
    local sw, sh = getScreenResolution()
    imgui.SetNextWindowPos(imgui.ImVec2(0, 0))
    imgui.SetNextWindowSize(imgui.ImVec2(sw, sh))
    imgui.Begin("HUD_ORIGINAL", nil,
        imgui.WindowFlags.NoDecoration +
        imgui.WindowFlags.NoMove +
        imgui.WindowFlags.NoBackground +
        imgui.WindowFlags.NoInputs +
        imgui.WindowFlags.NoBringToFrontOnFocus
    )

    local draw = imgui.GetWindowDrawList()
    local pos = imgui.GetWindowPos()
    local mousePos = imgui.GetMousePos()
    local io = imgui.GetIO()

    if imageConfig.enabled[0] and imageConfig.currentTexture then
        local imagePos = imgui.ImVec2(pos.x + imageConfig.x[0], pos.y + imageConfig.y[0])
        local imageSize = imgui.ImVec2(imageConfig.width[0], imageConfig.height[0])
        
        draw:AddImage(imageConfig.currentTexture, imagePos, 
                     imgui.ImVec2(imagePos.x + imageSize.x, imagePos.y + imageSize.y))
    end

    if buttonConfig.enabled[0] then
        local buttonPos = imgui.ImVec2(pos.x + buttonConfig.x[0], pos.y + buttonConfig.y[0])
        local buttonRadius = buttonConfig.size[0] / 2
        local buttonCenter = imgui.ImVec2(buttonPos.x + buttonRadius, buttonPos.y + buttonRadius)
        
        local mouseDistance = math.sqrt((mousePos.x - buttonCenter.x)^2 + (mousePos.y - buttonCenter.y)^2)
        local isHovered = mouseDistance <= buttonRadius
        
        local currentTime = os.clock() * 1000
        local canClick = (currentTime - lastClickTime) > clickCooldown
        
        local buttonColor = buttonConfig.color
        if isHovered then
            buttonColor = buttonConfig.hoverColor
            if io.MouseClicked[0] and canClick then
                buttonColor = buttonConfig.activeColor
                toggleVoip()
                lastClickTime = currentTime
            end
        end
        
        draw:AddCircleFilled(buttonCenter, buttonRadius, rgba(buttonColor[0], buttonColor[1], buttonColor[2], buttonColor[3]))
    end

    if miraEnabled[0] then
        local centerX = pos.x + sw / 2 + miraX[0]
        local centerY = pos.y + sh / 2 + miraY[0]
        local halfSize = miraSize[0] / 2
        
        if miraBorderEnabled[0] and miraBorderSize[0] > 0 then
            local borderHalfSize = halfSize + miraBorderSize[0]
            
            if miraTipo[0] == 0 then
                draw:AddLine(
                    imgui.ImVec2(centerX - borderHalfSize, centerY),
                    imgui.ImVec2(centerX + borderHalfSize, centerY),
                    rgba(miraBorderColor[0], miraBorderColor[1], miraBorderColor[2], miraBorderColor[3]),
                    miraWidth[0] + (miraBorderSize[0] * 2)
                )
                draw:AddLine(
                    imgui.ImVec2(centerX, centerY - borderHalfSize),
                    imgui.ImVec2(centerX, centerY + borderHalfSize),
                    rgba(miraBorderColor[0], miraBorderColor[1], miraBorderColor[2], miraBorderColor[3]),
                    miraWidth[0] + (miraBorderSize[0] * 2)
                )
            elseif miraTipo[0] == 1 then
                draw:AddLine(
                    imgui.ImVec2(centerX - borderHalfSize, centerY),
                    imgui.ImVec2(centerX + borderHalfSize, centerY),
                    rgba(miraBorderColor[0], miraBorderColor[1], miraBorderColor[2], miraBorderColor[3]),
                    miraWidth[0] + (miraBorderSize[0] * 2)
                )
                draw:AddLine(
                    imgui.ImVec2(centerX, centerY - borderHalfSize),
                    imgui.ImVec2(centerX, centerY + borderHalfSize),
                    rgba(miraBorderColor[0], miraBorderColor[1], miraBorderColor[2], miraBorderColor[3]),
                    miraWidth[0] + (miraBorderSize[0] * 2)
                )
            elseif miraTipo[0] == 2 then
                draw:AddCircle(
                    imgui.ImVec2(centerX, centerY),
                    halfSize + miraBorderSize[0],
                    rgba(miraBorderColor[0], miraBorderColor[1], miraBorderColor[2], miraBorderColor[3]),
                    0,
                    miraWidth[0] + (miraBorderSize[0] * 2)
                )
            elseif miraTipo[0] == 3 then
                draw:AddRect(
                    imgui.ImVec2(centerX - halfSize - miraBorderSize[0], centerY - halfSize - miraBorderSize[0]),
                    imgui.ImVec2(centerX + halfSize + miraBorderSize[0], centerY + halfSize + miraBorderSize[0]),
                    rgba(miraBorderColor[0], miraBorderColor[1], miraBorderColor[2], miraBorderColor[3]),
                    0,
                    0,
                    miraWidth[0] + (miraBorderSize[0] * 2)
                )
            elseif miraTipo[0] == 4 then
                draw:AddLine(
                    imgui.ImVec2(centerX - borderHalfSize, centerY - borderHalfSize),
                    imgui.ImVec2(centerX + borderHalfSize, centerY + borderHalfSize),
                    rgba(miraBorderColor[0], miraBorderColor[1], miraBorderColor[2], miraBorderColor[3]),
                    miraWidth[0] + (miraBorderSize[0] * 2)
                )
                draw:AddLine(
                    imgui.ImVec2(centerX + borderHalfSize, centerY - borderHalfSize),
                    imgui.ImVec2(centerX - borderHalfSize, centerY + borderHalfSize),
                    rgba(miraBorderColor[0], miraBorderColor[1], miraBorderColor[2], miraBorderColor[3]),
                    miraWidth[0] + (miraBorderSize[0] * 2)
                )
            elseif miraTipo[0] == 5 then
                draw:AddCircleFilled(
                    imgui.ImVec2(centerX, centerY),
                    halfSize + miraBorderSize[0],
                    rgba(miraBorderColor[0], miraBorderColor[1], miraBorderColor[2], miraBorderColor[3])
                )
            elseif miraTipo[0] == 6 then
                draw:AddCircle(
                    imgui.ImVec2(centerX, centerY),
                    halfSize + miraBorderSize[0],
                    rgba(miraBorderColor[0], miraBorderColor[1], miraBorderColor[2], miraBorderColor[3]),
                    0,
                    miraWidth[0] + (miraBorderSize[0] * 2)
                )
                draw:AddCircle(
                    imgui.ImVec2(centerX, centerY),
                    halfSize/2 + miraBorderSize[0],
                    rgba(miraBorderColor[0], miraBorderColor[1], miraBorderColor[2], miraBorderColor[3]),
                    0,
                    miraWidth[0] + (miraBorderSize[0] * 2)
                )
            elseif miraTipo[0] == 7 then
                local diamondSize = halfSize + miraBorderSize[0]
                draw:AddQuad(
                    imgui.ImVec2(centerX, centerY - diamondSize),
                    imgui.ImVec2(centerX + diamondSize, centerY),
                    imgui.ImVec2(centerX, centerY + diamondSize),
                    imgui.ImVec2(centerX - diamondSize, centerY),
                    rgba(miraBorderColor[0], miraBorderColor[1], miraBorderColor[2], miraBorderColor[3]),
                    miraWidth[0] + (miraBorderSize[0] * 2)
                )
            elseif miraTipo[0] == 8 then
                local starSize = halfSize + miraBorderSize[0]
                for i = 0, 4 do
                    local angle1 = i * 4 * math.pi / 5
                    local angle2 = (i + 0.5) * 4 * math.pi / 5
                    draw:AddLine(
                        imgui.ImVec2(centerX + starSize * math.sin(angle1), centerY - starSize * math.cos(angle1)),
                        imgui.ImVec2(centerX + starSize/2 * math.sin(angle2), centerY - starSize/2 * math.cos(angle2)),
                        rgba(miraBorderColor[0], miraBorderColor[1], miraBorderColor[2], miraBorderColor[3]),
                        miraWidth[0] + (miraBorderSize[0] * 2)
                    )
                end
            elseif miraTipo[0] == 9 then
                local triangleSize = halfSize + miraBorderSize[0]
                draw:AddTriangle(
                    imgui.ImVec2(centerX, centerY - triangleSize),
                    imgui.ImVec2(centerX + triangleSize, centerY + triangleSize),
                    imgui.ImVec2(centerX - triangleSize, centerY + triangleSize),
                    rgba(miraBorderColor[0], miraBorderColor[1], miraBorderColor[2], miraBorderColor[3]),
                    miraWidth[0] + (miraBorderSize[0] * 2)
                )
            end
        end
        
        if miraTipo[0] == 0 then
            draw:AddLine(
                imgui.ImVec2(centerX - halfSize, centerY),
                imgui.ImVec2(centerX + halfSize, centerY),
                rgba(miraColor[0], miraColor[1], miraColor[2], miraColor[3]),
                miraWidth[0]
            )
            draw:AddLine(
                imgui.ImVec2(centerX, centerY - halfSize),
                imgui.ImVec2(centerX, centerY + halfSize),
                rgba(miraColor[0], miraColor[1], miraColor[2], miraColor[3]),
                miraWidth[0]
            )
        elseif miraTipo[0] == 1 then
            draw:AddLine(
                imgui.ImVec2(centerX - halfSize, centerY),
                imgui.ImVec2(centerX + halfSize, centerY),
                rgba(miraColor[0], miraColor[1], miraColor[2], miraColor[3]),
                miraWidth[0]
            )
            draw:AddLine(
                imgui.ImVec2(centerX, centerY - halfSize),
                imgui.ImVec2(centerX, centerY + halfSize),
                rgba(miraColor[0], miraColor[1], miraColor[2], miraColor[3]),
                miraWidth[0]
            )
            draw:AddLine(
                imgui.ImVec2(centerX - halfSize/2, centerY - halfSize/2),
                imgui.ImVec2(centerX + halfSize/2, centerY + halfSize/2),
                rgba(miraColor[0], miraColor[1], miraColor[2], miraColor[3]),
                miraWidth[0]/2
            )
            draw:AddLine(
                imgui.ImVec2(centerX + halfSize/2, centerY - halfSize/2),
                imgui.ImVec2(centerX - halfSize/2, centerY + halfSize/2),
                rgba(miraColor[0], miraColor[1], miraColor[2], miraColor[3]),
                miraWidth[0]/2
            )
        elseif miraTipo[0] == 2 then
            draw:AddCircle(
                imgui.ImVec2(centerX, centerY),
                halfSize,
                rgba(miraColor[0], miraColor[1], miraColor[2], miraColor[3]),
                0,
                miraWidth[0]
            )
        elseif miraTipo[0] == 3 then
            draw:AddRect(
                imgui.ImVec2(centerX - halfSize, centerY - halfSize),
                imgui.ImVec2(centerX + halfSize, centerY + halfSize),
                rgba(miraColor[0], miraColor[1], miraColor[2], miraColor[3]),
                0,
                0,
                miraWidth[0]
            )
        elseif miraTipo[0] == 4 then
            draw:AddLine(
                imgui.ImVec2(centerX - halfSize, centerY - halfSize),
                imgui.ImVec2(centerX + halfSize, centerY + halfSize),
                rgba(miraColor[0], miraColor[1], miraColor[2], miraColor[3]),
                miraWidth[0]
            )
            draw:AddLine(
                imgui.ImVec2(centerX + halfSize, centerY - halfSize),
                imgui.ImVec2(centerX - halfSize, centerY + halfSize),
                rgba(miraColor[0], miraColor[1], miraColor[2], miraColor[3]),
                miraWidth[0]
            )
        elseif miraTipo[0] == 5 then
            draw:AddCircleFilled(
                imgui.ImVec2(centerX, centerY),
                halfSize,
                rgba(miraColor[0], miraColor[1], miraColor[2], miraColor[3])
            )
        elseif miraTipo[0] == 6 then
            draw:AddCircle(
                imgui.ImVec2(centerX, centerY),
                halfSize,
                rgba(miraColor[0], miraColor[1], miraColor[2], miraColor[3]),
                0,
                miraWidth[0]
            )
            draw:AddCircle(
                imgui.ImVec2(centerX, centerY),
                halfSize/2,
                rgba(miraColor[0], miraColor[1], miraColor[2], miraColor[3]),
                0,
                miraWidth[0]
            )
        elseif miraTipo[0] == 7 then
            local diamondSize = halfSize
            draw:AddQuad(
                imgui.ImVec2(centerX, centerY - diamondSize),
                imgui.ImVec2(centerX + diamondSize, centerY),
                imgui.ImVec2(centerX, centerY + diamondSize),
                imgui.ImVec2(centerX - diamondSize, centerY),
                rgba(miraColor[0], miraColor[1], miraColor[2], miraColor[3]),
                miraWidth[0]
            )
        elseif miraTipo[0] == 8 then
            local starSize = halfSize
            for i = 0, 4 do
                local angle1 = i * 4 * math.pi / 5
                local angle2 = (i + 0.5) * 4 * math.pi / 5
                draw:AddLine(
                    imgui.ImVec2(centerX + starSize * math.sin(angle1), centerY - starSize * math.cos(angle1)),
                    imgui.ImVec2(centerX + starSize/2 * math.sin(angle2), centerY - starSize/2 * math.cos(angle2)),
                    rgba(miraColor[0], miraColor[1], miraColor[2], miraColor[3]),
                    miraWidth[0]
                )
            end
        elseif miraTipo[0] == 9 then
            local triangleSize = halfSize
            draw:AddTriangle(
                imgui.ImVec2(centerX, centerY - triangleSize),
                imgui.ImVec2(centerX + triangleSize, centerY + triangleSize),
                imgui.ImVec2(centerX - triangleSize, centerY + triangleSize),
                rgba(miraColor[0], miraColor[1], miraColor[2], miraColor[3]),
                miraWidth[0]
            )
        end
    end

    shellderTextOpacity[0] = shellderTextOpacity[0] + (shellderFadeDirection * 0.01)
    if shellderTextOpacity[0] >= 1.0 then
        shellderTextOpacity[0] = 1.0
        shellderFadeDirection = -1
        shellderColorIndex = shellderColorIndex % #shellderColorList + 1
    elseif shellderTextOpacity[0] <= 0.0 then
        shellderTextOpacity[0] = 0.0
        shellderFadeDirection = 1
    end

    local shellderColor = shellderColorList[shellderColorIndex]
    
    local shellderY = pos.y + sh - 30
    local shellderX = pos.x + 50

    draw:AddText(imgui.ImVec2(shellderX, shellderY), 
                 rgba(shellderColor[1], shellderColor[2], shellderColor[3], shellderTextOpacity[0]), 
                 "by shellder")

    if editMode[0] then
        config.vidaX = vidaX[0]
        config.vidaY = vidaY[0]
        config.vidaWidth = vidaWidth[0]
        config.vidaHeight = vidaHeight[0]
        config.coleteX = coleteX[0]
        config.coleteY = coleteY[0]
        config.coleteWidth = coleteWidth[0]
        config.coleteHeight = coleteHeight[0]
        config.moneyX = moneyX[0]
        config.moneyY = moneyY[0]
        config.moneySize = moneySize[0]
        config.moneyBorderSize = moneyBorderSize[0]
        config.borderRadius = borderRadius[0]
        config.hpFontSize = hpFontSize[0]
        config.apFontSize = apFontSize[0]
        config.moneyFontSize = moneyFontSize[0]
        
        config.borderEnabled = borderEnabled[0]
        config.borderSize = borderSize[0]
        config.borderColor = {borderColor[0], borderColor[1], borderColor[2], borderColor[3]}
        
        config.miraEnabled = miraEnabled[0]
        config.miraX = miraX[0]
        config.miraY = miraY[0]
        config.miraSize = miraSize[0]
        config.miraWidth = miraWidth[0]
        config.miraColor = {miraColor[0], miraColor[1], miraColor[2], miraColor[3]}
        config.miraBorderEnabled = miraBorderEnabled[0]
        config.miraBorderSize = miraBorderSize[0]
        config.miraBorderColor = {miraBorderColor[0], miraBorderColor[1], miraBorderColor[2], miraBorderColor[3]}
        config.miraTipo = miraTipo[0]
        
        config.buttonEnabled = buttonConfig.enabled[0]
        config.buttonX = buttonConfig.x[0]
        config.buttonY = buttonConfig.y[0]
        config.buttonSize = buttonConfig.size[0]
        config.buttonColor = {buttonConfig.color[0], buttonConfig.color[1], buttonConfig.color[2], buttonConfig.color[3]}
        config.buttonHoverColor = {buttonConfig.hoverColor[0], buttonConfig.hoverColor[1], buttonConfig.hoverColor[2], buttonConfig.hoverColor[3]}
        config.buttonActiveColor = {buttonConfig.activeColor[0], buttonConfig.activeColor[1], buttonConfig.activeColor[2], buttonConfig.activeColor[3]}
    end

    local vidaMax = 100
    local coleteMax = 100
    
    local vidaPercent = math.min(vida / vidaMax, 1.0)
    local coletePercent = math.min(colete / coleteMax, 1.0)
    
    local vidaBarWidth = vidaPercent * config.vidaWidth
    local coleteBarWidth = coletePercent * config.coleteWidth

    local vidaBG = imgui.ImVec2(pos.x + config.vidaX, pos.y + config.vidaY)
    local vidaEnd = imgui.ImVec2(pos.x + config.vidaX + config.vidaWidth, pos.y + config.vidaY + config.vidaHeight)
    local vidaFG = imgui.ImVec2(pos.x + config.vidaX + vidaBarWidth, pos.y + config.vidaY + config.vidaHeight)
    
    draw:AddRectFilled(vidaBG, vidaEnd, rgba(0.1, 0.1, 0.1, 0.8), config.borderRadius)
    if vidaBarWidth > 0 then
        draw:AddRectFilled(vidaBG, vidaFG, rgba(config.vidaColor[1], config.vidaColor[2], config.vidaColor[3], config.vidaColor[4]), config.borderRadius)
    end
    
    if borderEnabled[0] and borderSize[0] > 0 then
        draw:AddRect(vidaBG, vidaEnd, 
                     rgba(borderColor[0], borderColor[1], borderColor[2], borderColor[3]), 
                     config.borderRadius, 0, borderSize[0])
    end
    
    imgui.SetWindowFontScale(config.hpFontSize / 13.0)
    draw:AddText(imgui.ImVec2(pos.x + config.vidaX + 10, pos.y + config.vidaY + 7), rgba(config.fontColor[1], config.fontColor[2], config.fontColor[3], config.fontColor[4]), string.format("%d HP", math.floor(vida)))
    imgui.SetWindowFontScale(1.0)
    
    draw:AddText(imgui.ImVec2(pos.x + config.vidaX + config.iconOffsetX, pos.y + config.vidaY + 5), rgba(config.fontColor[1], config.fontColor[2], config.fontColor[3], config.fontColor[4]), faicons.HEART_PULSE)

    local coleteBG = imgui.ImVec2(pos.x + config.coleteX, pos.y + config.coleteY)
    local coleteEnd = imgui.ImVec2(pos.x + config.coleteX + config.coleteWidth, pos.y + config.coleteY + config.coleteHeight)
    local coleteFG = imgui.ImVec2(pos.x + config.coleteX + coleteBarWidth, pos.y + config.coleteY + config.coleteHeight)
    
    draw:AddRectFilled(coleteBG, coleteEnd, rgba(0.1, 0.1, 0.1, 0.8), config.borderRadius)
    if coleteBarWidth > 0 then
        draw:AddRectFilled(coleteBG, coleteFG, rgba(config.coleteColor[1], config.coleteColor[2], config.coleteColor[3], config.coleteColor[4]), config.borderRadius)
    end
    
    if borderEnabled[0] and borderSize[0] > 0 then
        draw:AddRect(coleteBG, coleteEnd, 
                     rgba(borderColor[0], borderColor[1], borderColor[2], borderColor[3]), 
                     config.borderRadius, 0, borderSize[0])
    end
    
    imgui.SetWindowFontScale(config.apFontSize / 13.0)
    draw:AddText(imgui.ImVec2(pos.x + config.coleteX + 10, pos.y + config.coleteY + 7), rgba(config.fontColor[1], config.fontColor[2], config.fontColor[3], config.fontColor[4]), string.format("%d AP", math.floor(colete)))
    imgui.SetWindowFontScale(1.0)
    
    draw:AddText(imgui.ImVec2(pos.x + config.coleteX + config.iconOffsetX, pos.y + config.coleteY + 5), rgba(config.fontColor[1], config.fontColor[2], config.fontColor[3], config.fontColor[4]), faicons.VEST)

    imgui.SetWindowFontScale(config.moneyFontSize / 13.0)
    
    local moneyText = "$" .. formatMoney(dinheiro)
    local textPos = imgui.ImVec2(pos.x + config.moneyX, pos.y + config.moneyY)
    
    for x = -config.moneyBorderSize, config.moneyBorderSize, config.moneyBorderSize do
        for y = -config.moneyBorderSize, config.moneyBorderSize, config.moneyBorderSize do
            if x ~= 0 or y ~= 0 then
                draw:AddText(imgui.ImVec2(textPos.x + x, textPos.y + y),
                    rgba(config.moneyBorderColor[1], config.moneyBorderColor[2], config.moneyBorderColor[3], config.moneyBorderColor[4]),
                    moneyText)
            end
        end
    end
    draw:AddText(textPos, rgba(config.moneyColor[1], config.moneyColor[2], config.moneyColor[3], config.moneyColor[4]), moneyText)
    
    imgui.SetWindowFontScale(1.0)
    
    imgui.End()
end)

local function CenterText(text)
    imgui.SetCursorPosX(imgui.GetWindowWidth() / 2 - imgui.CalcTextSize(text).x / 2)
    imgui.Text(text)
end

imgui.OnFrame(function() return editMode[0] end, function()
    imgui.SetNextWindowSize(imgui.ImVec2(600, 700), imgui.Cond.FirstUseEver)
    
    imgui.SetNextWindowSizeConstraints(imgui.ImVec2(600, 700), imgui.ImVec2(600, 700))
    
    imgui.PushStyleColor(imgui.Col.WindowBg, imgui.ImVec4(0.0, 0.0, 0.0, 0.8))
    imgui.PushStyleColor(imgui.Col.TitleBg, imgui.ImVec4(0.1, 0.1, 0.1, 0.9))
    imgui.PushStyleColor(imgui.Col.TitleBgActive, imgui.ImVec4(0.2, 0.2, 0.2, 0.9))
    imgui.PushStyleColor(imgui.Col.Button, imgui.ImVec4(0.3, 0.3, 0.3, 0.8))
    imgui.PushStyleColor(imgui.Col.ButtonHovered, imgui.ImVec4(0.4, 0.4, 0.4, 0.9))
    imgui.PushStyleColor(imgui.Col.ButtonActive, imgui.ImVec4(0.5, 0.5, 0.5, 1.0))
    imgui.PushStyleColor(imgui.Col.Header, imgui.ImVec4(0.2, 0.2, 0.2, 0.8))
    imgui.PushStyleColor(imgui.Col.HeaderHovered, imgui.ImVec4(0.3, 0.3, 0.3, 0.9))
    imgui.PushStyleColor(imgui.Col.HeaderActive, imgui.ImVec4(0.4, 0.4, 0.4, 1.0))
    imgui.PushStyleColor(imgui.Col.FrameBg, imgui.ImVec4(0.15, 0.15, 0.15, 0.8))
    imgui.PushStyleColor(imgui.Col.FrameBgHovered, imgui.ImVec4(0.25, 0.25, 0.25, 0.9))
    imgui.PushStyleColor(imgui.Col.FrameBgActive, imgui.ImVec4(0.35, 0.35, 0.35, 1.0))
    imgui.PushStyleColor(imgui.Col.SliderGrab, imgui.ImVec4(0.4, 0.4, 0.4, 1.0))
    imgui.PushStyleColor(imgui.Col.SliderGrabActive, imgui.ImVec4(0.5, 0.5, 0.5, 1.0))
    imgui.PushStyleColor(imgui.Col.Text, imgui.ImVec4(1.0, 1.0, 1.0, 1.0))
    
    imgui.PushStyleColor(imgui.Col.Tab, imgui.ImVec4(0.5, 0.0, 0.0, 0.8))
    imgui.PushStyleColor(imgui.Col.TabHovered, imgui.ImVec4(0.8, 0.0, 0.0, 0.9))
    imgui.PushStyleColor(imgui.Col.TabActive, imgui.ImVec4(1.0, 0.0, 0.0, 1.0))
    
    local windowOpen = imgui.Begin("HUD ESTILO PC - by Shellder", editMode, imgui.WindowFlags.NoResize + imgui.WindowFlags.NoCollapse)

    local draw = imgui.GetWindowDrawList()
    local windowPos = imgui.GetWindowPos()
    local windowSize = imgui.GetWindowSize()

    shellderTextOpacity[0] = shellderTextOpacity[0] + (shellderFadeDirection * 0.01)
    if shellderTextOpacity[0] >= 1.0 then
        shellderTextOpacity[0] = 1.0
        shellderFadeDirection = -1
        shellderColorIndex = shellderColorIndex % #shellderColorList + 1
    elseif shellderTextOpacity[0] <= 0.0 then
        shellderTextOpacity[0] = 0.0
        shellderFadeDirection = 1
    end

    local shellderColor = shellderColorList[shellderColorIndex]
    
    local shellderY = windowPos.y + 40
    local shellderX = windowSize.x / 2 - imgui.CalcTextSize("by Shellder").x / 2

    draw:AddText(imgui.ImVec2(shellderX, shellderY), 
                 rgba(shellderColor[1], shellderColor[2], shellderColor[3], shellderTextOpacity[0]), 
                 "by Shellder")

    imgui.Spacing()
    imgui.Spacing()
    
    if imgui.BeginTabBar("##HUDTabs") then
        if imgui.BeginTabItem("AJUSTES DE POSICAO") then
            if imgui.CollapsingHeader("AJUSTAR VIDA/COLETE", imgui.TreeNodeFlags.DefaultOpen) then
                imgui.Checkbox("MOVER VIDA/COLETE JUNTOS", moveTogether)
                
                if moveTogether[0] then
                    imgui.Text("POSICAO VIDA/COLETE")
                    if imgui.SliderInt("VIDA/COLETE X", vidaX, 0, 3000) then
                        coleteX[0] = vidaX[0]
                    end
                    if imgui.SliderInt("VIDA/COLETE Y", vidaY, 0, 3000) then
                        coleteY[0] = vidaY[0] + 42
                    end
                else
                    imgui.Text("POSICAO VIDA (X, Y):")
                    imgui.SliderInt("VIDA X", vidaX, 0, 3000)
                    imgui.SliderInt("VIDA Y", vidaY, 0, 3000)
                    
                    imgui.Text("POSICAO COLETE (X, Y):")
                    imgui.SliderInt("COLETE X", coleteX, 0, 3000)
                    imgui.SliderInt("COLETE Y", coleteY, 0, 3000)
                end
                
                imgui.Text("AJUSTAR VIDA")
                imgui.SliderInt("LARGURA VIDA", vidaWidth, 30, 300)
                imgui.SliderInt("ALTURA VIDA", vidaHeight, 10, 80)
                
                imgui.Text("AJUSTAR COLETE")
                imgui.SliderInt("LARGURA COLETE", coleteWidth, 30, 300)
                imgui.SliderInt("ALTURA COLETE", coleteHeight, 10, 80)
                
                imgui.Text("TAMANHO FONTE VIDA/COLETE")
                imgui.Checkbox("AJUSTAR FONTES", moveFontsTogether)
                
                if moveFontsTogether[0] then
                    if imgui.SliderFloat("TAMANHO HP/AP", hpFontSize, 8.0, 30.0) then
                        apFontSize[0] = hpFontSize[0]
                    end
                else
                    imgui.SliderFloat("TAMANHO HP", hpFontSize, 8.0, 30.0)
                    imgui.SliderFloat("TAMANHO AP", apFontSize, 8.0, 30.0)
                end
            end

            imgui.Spacing()

            if imgui.CollapsingHeader("AJUSTAR DINHEIRO", imgui.TreeNodeFlags.DefaultOpen) then
                imgui.Text("AJUSTAR DINHEIRO")
                imgui.SliderInt("MONEY X", moneyX, 0, 3000)
                imgui.SliderInt("MONEY Y", moneyY, 0, 3000)
                
                imgui.Text("TAMANHO FONTE DO DINHEIRO")
                imgui.SliderFloat("TAMANHO FONTE", moneyFontSize, 8.0, 30.0)
            end
            
            imgui.EndTabItem()
        end
        
        if imgui.BeginTabItem("CORES HUD") then
            if imgui.CollapsingHeader("CORES DO HUD", imgui.TreeNodeFlags.DefaultOpen) then
                imgui.Text("COR DA BARRA DE VIDA")
                imgui.ColorEdit4(" VIDA", vidaColor)
                imgui.Separator()
                imgui.Text("COR DA BARRA DE COLETE")
                imgui.ColorEdit4(" COLETE", coleteColor)
                imgui.Separator()
                imgui.Text("COR DO DINHEIRO")
                imgui.ColorEdit4(" DINHEIRO", moneyColor)
                imgui.Separator()
                imgui.Text("COR DAS FONTES")
                imgui.ColorEdit4(" FONTES", fontColor)
            end
            
            imgui.Spacing()
            
            if imgui.CollapsingHeader("BORDA DO HUD (VIDA/COLETE)", imgui.TreeNodeFlags.DefaultOpen) then
                imgui.Spacing()
                imgui.Separator()
                imgui.Spacing()
                
                imgui.Text("ESTILO DA BORDA (ARREDONDAMENTO)")
                imgui.Text(string.format("BORDA ATUAL: %.1f", borderRadius[0]))
                
                imgui.BeginGroup()
                if imgui.Button("-", imgui.ImVec2(40, 30)) then
                    if borderRadius[0] > 0.0 then
                        borderRadius[0] = borderRadius[0] - 0.5
                    end
                end
                
                imgui.SameLine()
                
                if imgui.Button("+", imgui.ImVec2(40, 30)) then
                    if borderRadius[0] < 20.0 then
                        borderRadius[0] = borderRadius[0] + 0.5
                    end
                end
                imgui.EndGroup()
                
                imgui.Spacing()
                imgui.Separator()
                imgui.Spacing()
                
                imgui.Text("BORDA EXTERNA VIDA/COLETE")
                imgui.Checkbox("ATIVAR BORDA EXTERNA", borderEnabled)
                
                if borderEnabled[0] then
                    imgui.Text("TAMANHO DA BORDA")
                    imgui.SliderFloat("LARGURA BORDA", borderSize, 0.5, 10.0)
                    
                    imgui.Text("COR DA BORDA")
                    imgui.ColorEdit4("COR BORDA", borderColor)
                end
                
                imgui.Spacing()
                imgui.Separator()
                imgui.Spacing()
                
                imgui.Text("BORDA DA FONTE DO DINHEIRO")
                imgui.SliderFloat("LARGURA BORDA DO DINHEIRO", moneyBorderSize, 0.5, 5.0)
                
                imgui.Text("COR DA BORDA DO DINHEIRO")
                imgui.ColorEdit4("COR BORDA DINHEIRO", moneyBorderColor)
            end
            
            imgui.EndTabItem()
        end

        if imgui.BeginTabItem("MIRA EXTERNA") then
            if imgui.CollapsingHeader("Mira Externa", imgui.TreeNodeFlags.DefaultOpen) then
                imgui.Checkbox("ATIVAR MIRA", miraEnabled)
                
                if miraEnabled[0] then
                    imgui.Text("TIPO DE MIRA")
                    local currentMiraType = miraTipo[0]
                    if imgui.BeginCombo("ESCOLHA O TIPO", miraTipos[currentMiraType + 1]) then
                        for i = 0, 9 do
                            if imgui.Selectable(miraTipos[i + 1], currentMiraType == i) then
                                miraTipo[0] = i
                            end
                        end
                        imgui.EndCombo()
                    end
                    
                    imgui.Text("PREVIEW: " .. miraTipos[miraTipo[0] + 1])
                    
                    imgui.Text("POSICAO DA MIRA")
                    imgui.SliderInt("MIRA X", miraX, -100, 100)
                    imgui.SliderInt("MIRA Y", miraY, -100, 100)
                    
                    imgui.Text("TAMANHO DA MIRA")
                    imgui.SliderFloat("TAMANHO", miraSize, 2.0, 50.0)
                    imgui.SliderFloat("LARGURA", miraWidth, 1.0, 10.0)
                    
                    imgui.Text("COR DA MIRA")
                    imgui.ColorEdit4("COR MIRA", miraColor)
                    
                    imgui.Spacing()
                    imgui.Separator()
                    imgui.Spacing()
                    
                    imgui.Text("BORDA DA MIRA")
                    imgui.Checkbox("ATIVAR BORDA", miraBorderEnabled)
                    
                    if miraBorderEnabled[0] then
                        imgui.Text("TAMANHO DA BORDA")
                        imgui.SliderFloat("LARGURA BORDA", miraBorderSize, 0.5, 5.0)
                        
                        imgui.Text("COR DA BORDA")
                        imgui.ColorEdit4("COR BORDA", miraBorderColor)
                    end
                end
            end
            imgui.EndTabItem()
        end

        if imgui.BeginTabItem("MODS EXTRAS") then
            if imgui.CollapsingHeader("MODIFICADOR DE CLIMA/HORA", imgui.TreeNodeFlags.DefaultOpen) then
                CenterText("Hora")
                imgui.PushItemWidth(230)
                imgui.SliderInt("##time", timeId, 0, 23)
                imgui.Spacing()
                CenterText("Clima")
                imgui.SliderInt("##weather", weatherId, 0, 45)
                imgui.PopItemWidth()
                imgui.Spacing()
                imgui.Separator()
                imgui.Spacing()

                if isWeatherActive[0] then
                    if imgui.Button("DESATIVAR CLIMA/HORA", imgui.ImVec2(190, 35)) then
                        isWeatherActive[0] = false
                    end
                else
                    if imgui.Button("ATIVAR CLIMA/HORA", imgui.ImVec2(190, 35)) then
                        isWeatherActive[0] = true
                    end
                end
            end

            imgui.Spacing()
            imgui.Separator()
            imgui.Spacing()

            if imgui.CollapsingHeader("FAKE PC - by Shellder", imgui.TreeNodeFlags.DefaultOpen) then
                CenterText("FAKE PC - by Shellder")
                imgui.Spacing()
                if isFakePcActive[0] then
                    if imgui.Button("DESATIVAR FAKE PC", imgui.ImVec2(190, 35)) then
                        isFakePcActive[0] = false
                    end
                else
                    if imgui.Button("ATIVAR FAKE PC", imgui.ImVec2(190, 35)) then
                        isFakePcActive[0] = true
                    end
                end
            end

            imgui.Spacing()
            imgui.Separator()
            imgui.Spacing()

            if imgui.CollapsingHeader("FOV CHANGER - by beavers_best", imgui.TreeNodeFlags.DefaultOpen) then
                CenterText("FOV CHANGER")
                imgui.Spacing()
                
                imgui.Checkbox("ATIVAR FOV CHANGER", fovEnabled)
                
                if fovEnabled[0] then
                    imgui.Text("VALOR DO FOV")
                    imgui.SliderInt("FOV", fovValue, 10, 135)
                    
                    if imgui.Button("RESETAR FOV", imgui.ImVec2(190, 35)) then
                        fovValue[0] = 60
                    end
                else
                    if imgui.Button("ATIVAR FOV CHANGER", imgui.ImVec2(190, 35)) then
                        fovEnabled[0] = true
                    end
                end
            end

            imgui.Spacing()
            imgui.Separator()
            imgui.Spacing()

            if imgui.CollapsingHeader("SISTEMA DE VOIP - by Shellder", imgui.TreeNodeFlags.DefaultOpen) then
                CenterText("CONTROLE DE VOIP")
                imgui.Spacing()
                
                if imgui.Checkbox("ATIVAR FACK VOIP", imageConfig.enabled) then
                    if imageConfig.enabled[0] and not imageConfig.texture then
                        loadImageTexture()
                    end
                end
                
                imgui.Spacing()
                imgui.Separator()
                imgui.Spacing()
                
                imgui.Text("POSICAO DO VOIP")
                imgui.SliderInt("POSICAO X", imageConfig.x, 0, 2000)
                imgui.SliderInt("POSICAO Y", imageConfig.y, 0, 2000)
                
                imgui.Spacing()
                imgui.Separator()
                imgui.Spacing()
                
                imgui.Text("TAMANHO DO VOIP")
                imgui.SliderInt("LARGURA", imageConfig.width, 50, 1000)
                imgui.SliderInt("ALTURA", imageConfig.height, 50, 1000)
                
                imgui.Spacing()
                imgui.Separator()
                imgui.Spacing()
                
                if imgui.Button("RECARREGAR VOIP", imgui.ImVec2(190, 35)) then
                    loadImageTexture()
                end
                
                imgui.SameLine()
                
                if imgui.Button("RESETAR CONFIG", imgui.ImVec2(190, 35)) then
                    imageConfig.x[0] = 100
                    imageConfig.y[0] = 100
                    imageConfig.width[0] = 200
                    imageConfig.height[0] = 200
                end
                
                imgui.Spacing()
                imgui.Separator()
                imgui.Spacing()
                
                imgui.Text("STATUS DO VOIP: " .. (imageConfig.voipActive and "ATIVO" or "INATIVO"))
                imgui.Text("IMAGEM ATUAL: " .. (imageConfig.voipActive and "shellder2.png" or "shellder.png"))
            end

            imgui.Spacing()
            imgui.Separator()
            imgui.Spacing()

            if imgui.CollapsingHeader("BOTÃO VOIP - by Shellder", imgui.TreeNodeFlags.DefaultOpen) then
                CenterText("CONFIGURAÇÃO DO BOTÃO VOIP")
                imgui.Spacing()
                
                imgui.Checkbox("ATIVAR BOTÃO VOIP", buttonConfig.enabled)
                
                imgui.Spacing()
                imgui.Separator()
                imgui.Spacing()
                
                imgui.Text("POSIÇÃO DO BOTÃO")
                imgui.SliderInt("BOTÃO X", buttonConfig.x, 0, 2000)
                imgui.SliderInt("BOTÃO Y", buttonConfig.y, 0, 2000)
                
                imgui.Spacing()
                imgui.Separator()
                imgui.Spacing()
                
                imgui.Text("TAMANHO DO BOTÃO")
                imgui.SliderInt("TAMANHO DO BOTÃO", buttonConfig.size, 20, 200)
                
                imgui.Spacing()
                imgui.Separator()
                imgui.Spacing()
                
                imgui.Text("CORES DO BOTÃO (OPACIDADE)")
                imgui.ColorEdit4("COR NORMAL", buttonConfig.color)
                imgui.ColorEdit4("COR HOVER", buttonConfig.hoverColor)
                imgui.ColorEdit4("COR ATIVO", buttonConfig.activeColor)
                
                imgui.Spacing()
                imgui.Separator()
                imgui.Spacing()
                
                if imgui.Button("TESTAR BOTÃO", imgui.ImVec2(190, 35)) then
                    toggleVoip()
                end
                
                imgui.SameLine()
                
                if imgui.Button("RESETAR BOTÃO", imgui.ImVec2(190, 35)) then
                    buttonConfig.x[0] = 50
                    buttonConfig.y[0] = 50
                    buttonConfig.size[0] = 60
                    buttonConfig.color[0] = 0.0
                    buttonConfig.color[1] = 0.0
                    buttonConfig.color[2] = 0.0
                    buttonConfig.color[3] = 0.5
                    buttonConfig.hoverColor[0] = 0.0
                    buttonConfig.hoverColor[1] = 0.0
                    buttonConfig.hoverColor[2] = 0.0
                    buttonConfig.hoverColor[3] = 0.7
                    buttonConfig.activeColor[0] = 0.0
                    buttonConfig.activeColor[1] = 0.0
                    buttonConfig.activeColor[2] = 0.0
                    buttonConfig.activeColor[3] = 0.9
                end
            end

            imgui.SameLine()

            if imgui.Button("D", imgui.ImVec2(35, 35)) then
                openLink("https://discord.gg/bU8SEBDP22")
            end
            
            imgui.EndTabItem()
        end
        
        imgui.EndTabBar()
    end

    imgui.Spacing()
    imgui.Spacing()
    imgui.Separator()
    imgui.Spacing()
    
    if imgui.Button("SALVAR", imgui.ImVec2(120, 30)) then
        saveConfig()
    end
    
    imgui.SameLine()
    
    if imgui.Button("FECHAR", imgui.ImVec2(120, 30)) then
        editMode[0] = false
    end

    imgui.End()
    
    imgui.PopStyleColor(18) 
end)

local fakePcVersion = "0.3.7"
local lastUnoccupiedSyncTime = 0

function a.onSendClientJoin(c, d, e, f, g, h, i)
    if isFakePcActive[0] then
        h = fakePcVersion
        g = '3917818323CD3E248B7722EB5CBCED04CE5B4DBF67E'
        local j = 1
        return { c, j, e, f, g, h, i }
    end
    return nil
end

function a.onSendPlayerSync(k)
    if isFakePcActive[0] then
        if k.weapon == 44 or k.weapon == 45 or k.weapon == 46 then
            k.weapon = 0
        end

        if k.leftRightKeys ~= 0 and k.leftRightKeys ~= 128 and k.leftRightKeys ~= 65408 then
            if k.leftRightKeys > 0 then
                k.leftRightKeys = 128
            else
                k.leftRightKeys = 65408
            end
        end

        if k.upDownKeys ~= 0 and k.upDownKeys ~= 128 and k.upDownKeys ~= 65408 then
            if k.upDownKeys > 0 then
                k.upDownKeys = 128
            else
                k.upDownKeys = 65408
            end
        end

        if k.upDownKeys == 65408 or k.keysData == 4 then
            k.animationId = 0
        end

        if k.specialAction ~= nil and k.specialAction ~= 0 and k.specialAction ~= 1 then
            k.specialAction = 0
        end
    end
    return k
end

function a.onSendVehicleSync(k)
    if isFakePcActive[0] then
        if k.leftRightKeys ~= 0 and k.leftRightKeys ~= 128 and k.leftRightKeys ~= 65408 then
            if k.leftRightKeys > 0 then
                k.leftRightKeys = 128
            else
                k.leftRightKeys = 65408
            end
        end
    end
    return k
end

function a.onSendPassengerSync(k)
    if isFakePcActive[0] then
        if k.leftRightKeys ~= 0 and k.leftRightKeys ~= 128 and k.leftRightKeys ~= 65408 then
            if k.leftRightKeys > 0 then
                k.leftRightKeys = 128
            else
                k.leftRightKeys = 65408
            end
        end
    end
    return k
end

function simulateUnoccupiedSync()
    local m = os.clock()
    if m - lastUnoccupiedSyncTime > 5 then
        lastUnoccupiedSyncTime = m
    end
end

function main()
    loadConfig()
    
    while not isSampAvailable() do 
        wait(0) 
    end

    sampRegisterChatCommand("shell", function()
        editMode[0] = not editMode[0]
    end)
    
    while true do
        wait(0)
        if isSampAvailable() then
            local hp = getCharHealth(PLAYER_PED)
            local ar = getCharArmour(PLAYER_PED)
            if hp then vida = math.min(100, hp) end
            if ar then colete = math.min(100, ar) end
            dinheiro = getPlayerMoney(PLAYER_HANDLE) or 0

            if isWeatherActive[0] then
                setTimeOfDay(timeId[0], 0)
                forceWeatherNow(weatherId[0])
            end

            if isFakePcActive[0] then
                simulateUnoccupiedSync()
            end

            if fovEnabled[0] then
                cameraSetLerpFov(fovValue[0], 101.0, 1000, true)
            end
        end
    end
end
