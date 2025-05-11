import React, { useState } from 'react';
import { Button } from '@/components/ui/button';
import { Switch } from '@/components/ui/switch';
import { cn } from '@/lib/utils';
import { Eye, CheckCircle, User, Users, XCircle } from 'lucide-react';

interface EspConfig {
    enabled: boolean;
    showBox: boolean;
    showName: boolean;
    showHealth: boolean;
    teamCheck: boolean;
}

const LuaEspInterface: React.FC = () => {
    const [espConfig, setEspConfig] = useState<EspConfig>({
        enabled: false,
        showBox: true,
        showName: true,
        showHealth: true,
        teamCheck: true,
    });
    const [isMenuOpen, setIsMenuOpen] = useState(true); // Controla a visibilidade do menu

    const toggleMenu = () => {
        setIsMenuOpen(!isMenuOpen);
    };

    const handleEspToggle = (key: keyof EspConfig) => {
        setEspConfig({
            ...espConfig,
            [key]: !espConfig[key],
        });
    };

    // Função para gerar o código Lua (simulado)
    const generateLuaCode = () => {
        let luaCode = `-- ESP Configuração\n`;
        luaCode += `-- Gerado em ${new Date().toLocaleString()}\n\n`;
        luaCode += `local esp = {}\n`;
        luaCode += `esp.enabled = ${espConfig.enabled ? 'true' : 'false'}\n`;
        luaCode += `esp.showBox = ${espConfig.showBox ? 'true' : 'false'}\n`;
        luaCode += `esp.showName = ${espConfig.showName ? 'true' : 'false'}\n`;
        luaCode += `esp.showHealth = ${espConfig.showHealth ? 'true' : 'false'}\n`;
        luaCode += `esp.teamCheck = ${espConfig.teamCheck ? 'true' : 'false'}\n\n`;
        luaCode += `-- Funções de desenho ESP (simuladas)\n`;
        luaCode += `function esp.draw()\n`;
        luaCode += `  if esp.enabled then\n`;
        luaCode += `    -- Lógica para iterar sobre os jogadores (simulada)\n`;
        luaCode += `    for i, player in ipairs(game.GetPlayers()) do\n`; //Função game.GetPlayers() não existe no react, é apenas para simular o LUA
        luaCode += `      local playerName = player:GetName()\n`; // player:GetName() não existe no react, é apenas para simular o LUA
        luaCode += `      local playerHealth = player:GetHealth()\n`;  // player:GetHealth() não existe no react, é apenas para simular o LUA
        luaCode += `      local playerTeam = player:GetTeam()\n`;    // player:GetTeam() não existe no react, é apenas para simular o LUA
        luaCode += `      local localTeam = game.GetLocalPlayer():GetTeam()\n`; // game.GetLocalPlayer():GetTeam() não existe no react, é apenas para simular o LUA
        luaCode += `      local x, y, z = player:GetPosition()\n`; // player:GetPosition() não existe no react, é apenas para simular o LUA
        luaCode += `      local isVisible = player:IsVisible()\n`;  // player:IsVisible() não existe no react, é apenas para simular o LUA


        luaCode += `      if not esp.teamCheck or playerTeam ~= localTeam then\n`;
        luaCode += `        -- Desenha informações do jogador\n`;
        luaCode += `        if esp.showBox then\n`;
        luaCode += `          if isVisible then\n`
        luaCode += `             draw.Color(0, 255, 0, 255)\n`; // draw.Color não existe no react, é apenas para simular o LUA
        luaCode += `          else\n`
        luaCode += `             draw.Color(255, 0, 0, 255)\n`; // draw.Color não existe no react, é apenas para simular o LUA
        luaCode += `          end\n`
        luaCode += `          draw.WireframeBox(x, y, z, 20, 40, 20)\n`; // draw.WireframeBox() não existe no react, é apenas para simular o LUA, e os valores 20, 40, 20 são exemplos de tamanho
        luaCode += `        end\n`;
        luaCode += `        if esp.showName then\n`;
        luaCode += `          draw.Text(playerName, x, y - 10, color_white)\n`; // draw.Text() não existe no react, é apenas para simular o LUA
        luaCode += `        end\n`;
        luaCode += `        if esp.showHealth then\n`;
        luaCode += `          draw.Text("HP: " .. playerHealth, x, y + 50, color_green)\n`; // draw.Text() não existe no react, é apenas para simular o LUA
        luaCode += `        end\n`;
        luaCode += `      end\n`;
        luaCode += `    end\n`;
        luaCode += `  end\n`;
        luaCode += `end\n\n`;
        luaCode += `-- Chamada da função de desenho (simulada)\n`;
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
                    <h1 className="text-xl font-bold text-blue-500 flex items-center gap-2">
                        <Eye className="w-5 h-5" />
                        Configuração ESP
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

                {/* Controles ESP */}
                <div className="space-y-4">
                    <div className="flex items-center justify-between">
                        <span className="text-gray-200 flex items-center gap-2">
                            <Eye className="w-4 h-4" />
                            Ativar ESP
                        </span>
                        <Switch
                            checked={espConfig.enabled}
                            onCheckedChange={() => handleEspToggle('enabled')}
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
                            onCheckedChange={() => handleEspToggle('showBox')}
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
                            onCheckedChange={() => handleEspToggle('showName')}
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
                            onCheckedChange={() => handleEspToggle('showHealth')}
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
                            onCheckedChange={() => handleEspToggle('teamCheck')}
                            className={cn(
                                espConfig.teamCheck ? "bg-blue-500" : "bg-gray-700",
                            )}
                        />
                    </div>
                </div>

                {/* Botão Gerar Código */}
                <div className="mt-6">
                    <Button
                        onClick={handleCopyToClipboard}
                        className="w-full bg-blue-500 hover:bg-blue-600 text-white"
                    >
                        Gerar Código Lua
                    </Button>
                </div>
            </div>
            {!isMenuOpen && (
                <Button
                    onClick={toggleMenu}
                    className="mt-4 bg-blue-500/90 text-white hover:bg-blue-500 backdrop-blur-md border border-blue-600 shadow-lg"
                >
                    Abrir Menu
                </Button>
            )}
        </div>
    );
};

export default LuaEspInterface;
