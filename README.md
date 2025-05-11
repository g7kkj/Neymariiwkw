import React, { useState } from 'react';
import { Button } from '@/components/ui/button';
import { Switch } from '@/components/ui/switch';
import { Slider } from '@/components/ui/slider';
import { cn } from '@/lib/utils';
import { Target, CheckCircle, Settings, XCircle, ChevronDown, Eye } from 'lucide-react';

interface EspConfig {
    enabled: boolean;
    showBox: boolean;
    showName: boolean;
    showHealth: boolean;
    teamCheck: boolean;
    showDistance: boolean;
}

interface AimbotConfig {
    enabled: boolean;
    autoAim: boolean;
    targetSelection: 'closest' | 'healthiest' | 'lowestHealth';
    teamCheck: boolean;
    fov: number;
    smooth: number;
}

const LuaCombinedInterface: React.FC = () => {
    const [espConfig, setEspConfig] = useState<EspConfig>({
        enabled: false,
        showBox: true,
        showName: true,
        showHealth: true,
        teamCheck: true,
        showDistance: true,
    });
    const [aimbotConfig, setAimbotConfig] = useState<AimbotConfig>({
        enabled: false,
        autoAim: true,
        targetSelection: 'closest',
        teamCheck: true,
        fov: 100,
        smooth: 0,
    });
    const [isMenuOpen, setIsMenuOpen] = useState(true); // Controla a visibilidade do menu
    const [activeTab, setActiveTab] = useState<'aimbot' | 'esp'>('aimbot');

    const targetOptions = [
        { label: 'Mais Próximo', value: 'closest' },
        { label: 'Mais Vida', value: 'healthiest' },
        { label: 'Menor Vida', value: 'lowestHealth' },
    ];

    const toggleMenu = () => {
        setIsMenuOpen(!isMenuOpen);
    };

    const handleEspToggle = (key: keyof EspConfig) => {
        setEspConfig({
            ...espConfig,
            [key]: !espConfig[key],
        });
    };

    const handleAimbotToggle = (key: keyof AimbotConfig) => {
        setAimbotConfig({
            ...aimbotConfig,
            [key]: !aimbotConfig[key],
        });
    };

    const handleConfigChange = (config: 'aimbot' | 'esp', key: keyof AimbotConfig | keyof EspConfig, value: any) => {
        if (config === 'aimbot') {
            setAimbotConfig({
                ...aimbotConfig,
                [key as keyof AimbotConfig]: value,
            });
        } else {
            setEspConfig({
                ...espConfig,
                [key as keyof EspConfig]: value,
            });
        }
    };

    const generateLuaCode = () => {
        let luaCode = `-- Configuração Combinada de Aimbot e ESP\n`;
        luaCode += `-- Gerado em ${new Date().toLocaleString()}\n\n`;

        // Aimbot Lua
        luaCode += `local aimbot = {}\n`;
        luaCode += `aimbot.enabled = ${aimbotConfig.enabled ? 'true' : 'false'}\n`;
        luaCode += `aimbot.autoAim = ${aimbotConfig.autoAim ? 'true' : 'false'}\n`;
        luaCode += `aimbot.targetSelection = "${aimbotConfig.targetSelection}"\n`;
        luaCode += `aimbot.teamCheck = ${aimbotConfig.teamCheck ? 'true' : 'false'}\n`;
        luaCode += `aimbot.fov = ${aimbotConfig.fov}\n`;
        luaCode += `aimbot.smooth = ${aimbotConfig.smooth}\n\n`;

        // ESP Lua
        luaCode += `local esp = {}\n`;
        luaCode += `esp.enabled = ${espConfig.enabled ? 'true' : 'false'}\n`;
        luaCode += `esp.showBox = ${espConfig.showBox ? 'true' : 'false'}\n`;
        luaCode += `esp.showName = ${espConfig.showName ? 'true' : 'false'}\n`;
        luaCode += `esp.showHealth = ${espConfig.showHealth ? 'true' : 'false'}\n`;
        luaCode += `esp.teamCheck = ${espConfig.teamCheck ? 'true' : 'false'}\n`;
        luaCode += `esp.showDistance = ${espConfig.showDistance ? 'true' : 'false'}\n\n`;

        luaCode += `-- Funções Aimbot (simuladas)\n`;
        luaCode += `function aimbot.findTarget()\n`;
        luaCode += `  if not aimbot.enabled then return nil end\n`;
        luaCode += `  local bestTarget = nil\n`;
        luaCode += `  local localTeam = game.GetLocalPlayer():GetTeam()\n`; // game.GetLocalPlayer():GetTeam() não existe no react, é apenas para simular o LUA
        luaCode += `  local closestDistance = 99999\n`;
        luaCode += `  local highestHealth = -1\n`;
        luaCode += `  local lowestHealth = 99999\n`;

        luaCode += `  for i, player in ipairs(game.GetPlayers()) do\n`;  // game.GetPlayers() não existe no react, é apenas para simular o LUA
        luaCode += `    if player ~= game.GetLocalPlayer() then\n`;   // game.GetLocalPlayer() não existe no react, é apenas para simular o LUA
        luaCode += `      local playerTeam = player:GetTeam()\n`;     // player:GetTeam() não existe no react, é apenas para simular o LUA
        luaCode += `      local playerHealth = player:GetHealth()\n`;   //  player:GetHealth() não existe no react, é apenas para simular o LUA
        luaCode += `      local distance = game.GetLocalPlayer():GetDistance(player)\n`; // game.GetLocalPlayer():GetDistance(player) não existe no react, é apenas para simular o LUA


        luaCode += `      if not aimbot.teamCheck or playerTeam ~= localTeam then\n`;
        luaCode += `        if aimbot.fov > 0 and distance > aimbot.fov then\n`;
        luaCode += `          goto continue\n`;
        luaCode += `        end\n`;
        luaCode += `        if aimbot.targetSelection == "closest" then\n`;
        luaCode += `          if distance < closestDistance then\n`;
        luaCode += `            closestDistance = distance\n`;
        luaCode += `            bestTarget = player\n`;
        luaCode += `          end\n`;
        luaCode += `        elseif aimbot.targetSelection == "healthiest" then\n`;
        luaCode += `          if playerHealth > highestHealth then\n`;
        luaCode += `            highestHealth = playerHealth\n`;
        luaCode += `            bestTarget = player\n`;
        luaCode += `          end\n`;
        luaCode += `        elseif aimbot.targetSelection == "lowestHealth" then\n`;
        luaCode += `          if playerHealth < lowestHealth then\n`;
        luaCode += `            lowestHealth = playerHealth\n`;
        luaCode += `            bestTarget = player\n`;
        luaCode += `          end\n`;
        luaCode += `        end\n`;
        luaCode += `      end\n`;
        luaCode += `    end\n`;
        luaCode += `    ::continue::\n`;
        luaCode += `  end\n`;
        luaCode += `  return bestTarget\n`;
        luaCode += `end\n\n`;

        luaCode += `function aimbot.aim()\n`;
        luaCode += `  if not aimbot.enabled then return end\n`;
        luaCode += `  local target = aimbot.findTarget()\n`;
        luaCode += `  if target then\n`;
        luaCode += `    -- Lógica para mover a mira para o alvo (simulada)\n`;
        luaCode += `    local x, y, z = target:GetPosition()\n`;  // target:GetPosition() não existe no react, é apenas para simular o LUA
        luaCode += `     local localPlayer = game.GetLocalPlayer()\n`; // game.GetLocalPlayer() não existe no react, é apenas para simular o LUA
        luaCode += `    local currentX, currentY, currentZ = localPlayer:GetPosition()\n`;  // localPlayer:GetPosition() não existe no react, é apenas para simular o LUA

        luaCode += `    local aimX = currentX + (x - currentX) / (aimbot.smooth + 1)\n`;
        luaCode += `    local aimY = currentY + (y - currentY) / (aimbot.smooth + 1)\n`;
        luaCode += `    local aimZ = currentZ + (z - currentZ) / (aimbot.smooth + 1)\n`;

        luaCode += `    game.SetViewAngles(aimX, aimY, aimZ)\n`; // game.SetViewAngles() não existe no react, é apenas para simular o LUA
        luaCode += `  end\n`;
        luaCode += `end\n\n`;

        // ESP Lua
        luaCode += `function esp.draw()\n`;
        luaCode += `  if esp.enabled then\n`;
        luaCode += `    -- Lógica para iterar sobre os jogadores (simulada)\n`;
        luaCode += `    for i, player in ipairs(game.GetPlayers()) do\n`;  // game.GetPlayers() não existe no react, é apenas para simular o LUA
        luaCode += `      local playerName = player:GetName()\n`;    //  player:GetName() não existe no react, é apenas para simular o LUA
        luaCode += `      local playerHealth = player:GetHealth()\n`;  //  player:GetHealth() não existe no react, é apenas para simular o LUA
        luaCode += `      local playerTeam = player:GetTeam()\n`;    //  player:GetTeam() não existe no react, é apenas para simular o LUA
        luaCode += `      local localTeam = game.GetLocalPlayer():GetTeam()\n`;  // game.GetLocalPlayer():GetTeam() não existe no react, é apenas para simular o LUA
        luaCode += `      local x, y, z = player:GetPosition()\n`;    //  player:GetPosition() não existe no react, é apenas para simular o LUA
        luaCode += `      local isVisible = player:IsVisible()\n`;    //  player:IsVisible() não existe no react, é apenas para simular o LUA
        luaCode += `      local distance = game.GetLocalPlayer():GetDistance(player)\n`; // game.GetLocalPlayer():GetDistance(player) não existe no react, é apenas para simular o LUA


        luaCode += `      if not esp.teamCheck or playerTeam ~= localTeam then\n`;
        luaCode += `        -- Desenha informações do jogador\n`;
        luaCode += `        if esp.showBox then\n`;
        luaCode += `          if isVisible then\n`
        luaCode += `             draw.Color(0, 255, 0, 255)\n`;  // draw.Color não existe no react, é apenas para simular o LUA
        luaCode += `          else\n`
        luaCode += `             draw.Color(255, 0, 0, 255)\n`;   // draw.Color não existe no react, é apenas para simular o LUA
        luaCode += `          end\n`
        luaCode += `          draw.WireframeBox(x, y, z, 20, 40, 20)\n`;  // draw.WireframeBox() não existe no react, é apenas para simular o LUA
        luaCode += `        end\n`;
        luaCode += `        if esp.showName then\n`;
        luaCode += `          draw.Text(playerName, x, y - 10, color_white)\n`;  // draw.Text() não existe no react, é apenas para simular o LUA
        luaCode += `        end\n`;
        luaCode += `        if esp.showHealth then\n`;
        luaCode += `          draw.Text("HP: " .. playerHealth, x, y + 50, color_green)\n`;  // draw.Text() não existe no react, é apenas para simular o LUA
        luaCode += `        end\n`;
        luaCode += `       if esp.showDistance then\n`;
        luaCode += `          draw.Text("Dist: " .. distance, x, y + 60, color_yellow)\n`; // draw.Text() não existe no react, é apenas para simular o LUA
        luaCode += `        end\n`;
        luaCode += `      end\n`;
        luaCode += `    end\n`;
        luaCode += `  end\n`;
        luaCode += `end\n\n`;

        luaCode += `-- Chamadas das funções (simuladas)\n`;
        luaCode += `if aimbot.autoAim then\n`;
        luaCode += `  aimbot.aim()\n`;
        luaCode += `end\n`;
        luaCode += `esp.draw()\n`;

        return luaCode;
    };

    const handleCopyToClipboard = () => {
        const code = generateLuaCode();
        navigator.clipboard.writeText(code);
        alert('Código Lua copiado para a área de transferência!');
    };

    return (
        <div className="min-h-screen bg-gray-900 text-white flex flex-col items-center justify-start pt-12">
            <div className={cn(
                "bg-gray-800/90 backdrop-blur-md border border-gray-700 rounded-xl shadow-lg p-4 w-full max-w-md transition-all duration-300",
                isMenuOpen ? "opacity-100 translate-y-0" : "opacity-0 translate-y-[-20px] pointer-events-none"
            )}>
                {/* Cabeçalho do Menu */}
                <div className="flex justify-between items-center mb-4">
                    <h1 className="text-xl font-bold text-red-500 flex items-center gap-2">
                        <Settings className="w-5 h-5" />
                        Configuração Aimbot & ESP
                    </h1>
                    <Button
                        variant="ghost"
                        size="icon"
                        onClick={toggleMenu}
                        className="text-gray-400 hover:text-white"
                    >
                        <XCircle className="w-5 h-5" />
                    </Button>
                </div>

                {/* Navegação por Abas */}
                <div className="flex space-x-4 mb-4">
                    <Button
                        variant="ghost"
                        className={cn(
                            "text-gray-400 hover:text-white hover:bg-gray-700/50 flex items-center gap-2",
                            activeTab === 'aimbot' && 'text-red-500 border-b-2 border-red-500'
                        )}
                        onClick={() => setActiveTab('aimbot')}
                    >
                        <Target className="w-4 h-4" />
                        Aimbot
                    </Button>
                    <Button
                        variant="ghost"
                        className={cn(
                            "text-gray-400 hover:text-white hover:bg-gray-700/50 flex items-center gap-2",
                            activeTab === 'esp' && 'text-blue-500 border-b-2 border-blue-500'
                        )}
                        onClick={() => setActiveTab('esp')}
                    >
                        <Eye className="w-4 h-4" />
                        ESP
                    </Button>
                </div>

                {/* Controles Aimbot */}
                {activeTab === 'aimbot' && (
                    <div className="space-y-4">
                        <div className="flex items-center justify-between">
                            <span className="text-gray-200">Ativar Aimbot</span>
                            <Switch
                                checked={aimbotConfig.enabled}
                                onCheckedChange={() => handleConfigChange('aimbot', 'enabled', !aimbotConfig.enabled)}
                                className={cn(
                                    aimbotConfig.enabled ? "bg-red-500" : "bg-gray-700",
                                )}
                            />
                        </div>

                        <div className="flex items-center justify-between">
                            <span className="text-gray-200">Auto Aim</span>
                            <Switch
                                checked={aimbotConfig.autoAim}
                                onCheckedChange={() => handleConfigChange('aimbot', 'autoAim', !aimbotConfig.autoAim)}
                                className={cn(
                                    aimbotConfig.autoAim ? "bg-red-500" : "bg-gray-700",
                                )}
                            />
                        </div>

                        <div>
                            <span className="text-gray-200 block mb-2">Seleção de Alvo</span>
                            <div className="relative inline-block w-full">
                                <select
                                    value={aimbotConfig.targetSelection}
                                    onChange={(e) => handleConfigChange('aimbot', 'targetSelection', e.target.value)}
                                    className="w-full bg-gray-700 border border-gray-600 text-white text-sm rounded-lg focus:ring-red-500 focus:border-red-500 block py-2.5 px-4 pr-10 appearance-none"
                                >
                                    {targetOptions.map((option) => (
                                        <option key={option.value} value={option.value}>
                                            {option.label}
                                        </option>
                                    ))}
                                </select>
                                <div className="absolute inset-y-0 right-0 flex items-center pr-3 pointer-events-none">
                                    <ChevronDown className="w-4 h-4 text-gray-400" />
                                </div>
                            </div>
                        </div>

                        <div className="flex items-center justify-between">
                            <span className="text-gray-200 flex items-center gap-2">
                                <CheckCircle className="w-4 h-4" />
                                Team Check
                            </span>
                            <Switch
                                checked={aimbotConfig.teamCheck}
                                onCheckedChange={() => handleConfigChange('aimbot', 'teamCheck', !aimbotConfig.teamCheck)}
                                className={cn(
                                    aimbotConfig.teamCheck ? "bg-red-500" : "bg-gray-700",
                                )}
                            />
                        </div>

                        <div>
                            <span className="text-gray-200 block mb-2">FOV</span>
                            <Slider
                                min={0}
                                max={180}
                                step={1}
                                value={[aimbotConfig.fov]}
                                onValueChange={(value) => handleConfigChange('aimbot', 'fov', value[0])}
                                className="w-full"
                                style={{
                                    '--slider-track-color': 'linear-gradient(to right, #4b5563, #ef4444, #4b5563)', // Cor do slider
                                }}
                            />
                            <p className="text-center text-gray-300 mt-2">{aimbotConfig.fov.toFixed(0)} Graus</p>
                        </div>

                        <div>
                            <span className="text-gray-200 block mb-2">Suavização</span>
                            <Slider
                                min={0}
                                max={50}
                                step={1}
                                value={[aimbotConfig.smooth]}
                                onValueChange={(value) => handleConfigChange('aimbot', 'smooth', value[0])}
                                className="w-full"
                                style={{
                                    '--slider-track-color': 'linear-gradient(to right, #4b5563, #ef4444, #4b5563)', // Cor do slider
                                }}
                            />
                            <p className="text-center text-gray-300 mt-2">{aimbotConfig.smooth.toFixed(0)}</p>
                        </div>
                    </div>
                )}

                {/* Controles ESP */}
                {activeTab === 'esp' && (
                    <div className="space-y-4">
                        <div className="flex items-center justify-between">
                            <span className="text-gray-200 flex items-center gap-2">
                                <Eye className="w-4 h-4" />
                                Ativar ESP
                            </span>
                            <Switch
                                checked={espConfig.enabled}
                                onCheckedChange={() => handleConfigChange('esp', 'enabled', !espConfig.enabled)}
                                className={cn(
                                    espConfig.enabled ? "bg-blue-500" : "bg-gray-700",
                                )}
                            />
                        </div>

                        <div className="flex items-center justify-between">
                            <span className="text-gray-200">
                                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="w-4 h-4"><rect x="5" y="5" width="14" height="14" rx="2" ry="2"></rect></svg>
                                Mostrar Box
                            </span>
                            <Switch
                                checked={espConfig.showBox}
                                onCheckedChange={() => handleConfigChange('esp', 'showBox', !espConfig.showBox)}
                                className={cn(
                                    espConfig.showBox ? "bg-blue-500" : "bg-gray-700",
                                )}
                            />
                        </div>

                        <div className="flex items-center justify-between">
                            <span className="text-gray-200 flex items-center gap-2">
                                <User className="w-4 h-4" />
                                Mostrar Nome
                            </span>
                            <Switch
                                checked={espConfig.showName}
                                onCheckedChange={() => handleConfigChange('esp', 'showName', !espConfig.showName)}
                                className={cn(
                                    espConfig.showName ? "bg-blue-500" : "bg-gray-700",
                                )}
                            />
                        </div>

                        <div className="flex items-center justify-between">
                            <span className="text-gray-200">
                                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="w-4 h-4"><path d="M19 10v-2a7 7 0 0 0-14 0v2"></path><path d="M12 15v-3"></path><path d="M17 5H9.5a3.5 3.5 0 0 0 0 7h5a3.5 3.5 0 0 1 0 7H6"></path></svg>
                                Mostrar Vida
                            </span>
                            <Switch
                                checked={espConfig.showHealth}
                                onCheckedChange={() => handleConfigChange('esp', 'showHealth', !espConfig.showHealth)}
                                className={cn(
                                    espConfig.showHealth ? "bg-blue-500" : "bg-gray-700",
                                )}
                            />
                        </div>

                        <div className="flex items-center justify-between">
                            <span className="text-gray-200 flex items-center gap-2">
                                <CheckCircle className="w-4 h-4" />
                                Team Check
                            </span>
                            <Switch
                                checked={espConfig.teamCheck}
                                onCheckedChange={() => handleConfigChange('esp', 'teamCheck', !espConfig.teamCheck)}
                                className={cn(
                                    espConfig.teamCheck ? "bg-blue-500" : "bg-gray-700",
                                )}
                            />
                        </div>
                         <div className="flex items-center justify-between">
                            <span className="text-gray-200 flex items-center gap-2">
                                <Target className="w-4 h-4" />
                                Mostrar Distância
                            </span>
                            <Switch
                                checked={espConfig.showDistance}
                                onCheckedChange={() => handleConfigChange('esp', 'showDistance', !espConfig.showDistance)}
                                className={cn(
                                    espConfig.showDistance ? "bg-blue-500" : "bg-gray-700",
                                )}
                            />
                        </div>
                    </div>
                )}

                {/* Botão Gerar Código */}
                <div className="mt-6">
                    <Button
                        onClick={handleCopyToClipboard}
                        className="w-full bg-purple-500 hover:bg-purple-600 text-white"
                    >
                        Gerar Código Lua
                    </Button>
                </div>
            </div>
            {!isMenuOpen && (
                <Button
                    onClick={toggleMenu}
                    className="mt-4 bg-purple-500/90text-white hover:bg-purple-500 backdrop-blur-md border border-purple-600 shadow-lg"
                >
                    Abrir Menu
                </Button>
            )}
        </div>
    );
};

export default LuaCombinedInterface;
