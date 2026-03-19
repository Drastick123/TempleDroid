# TempleDroid
Recentemente descobri uma forma de usar o TempleOS no termux em uma VM, então eu quis compartilhar isso aqui, que inclusive meu amigo, [Ex3cutor76-V1](https://github.com/Ex3cutor76-V1) me ajudou com essa ideia.

# O que é o TempleOS

resumindo: TempleOS é um sistema operacional criado do zero por Terry A. Davis que de acordo com ele seria um "Templo de Deus", mais informações no site do meu amigo:
https://ex3cutor76-v1.github.io/Math-info/Sistemas%20operacionais/TempleOS/index.html

# Requisitos

``pkg install wget``

``pkg update && pkg upgrade -y``

``pkg install root-repo``

``pkg install netcat-openbsd``

``pkg install x11-repo``

``pkg install qemu-system-x86_64 ``




# Instalação 

primeiro execute  o comando:

``nano $PREFIX/bin/temple``

após insto cole o seguinte comando dentro dele:
```
#!/data/data/com.termux/files/usr/bin/bash

# ===== CORES =====
AZUL='\033[1;34m'
AMARELO='\033[1;33m'
VERDE='\033[1;32m'
VERMELHO='\033[1;31m'
RESET='\033[0m'

RAM_DEFAULT=512
ISO="$HOME/TempleOS.ISO"
CONFIG_FILE="$HOME/.temple_config"

# ===== SOM =====
play_sound() {
  termux-media-player play /system/media/audio/ui/Effect_Tick.ogg 2>/dev/null
}

# ===== LOADING BAR =====
loading_bar() {
  echo -ne "${AZUL}Carregando: ${RESET}"
  for i in {1..20}; do
    echo -ne "${VERDE}#${RESET}"
    sleep 0.05
  done
  echo ""
}

# ===== LOGO BONITA =====
logo() {
  clear
  echo -e "${AMARELO}=========================================${RESET}"
  echo -e "${AZUL}"
  sleep 0.03
  echo "████████╗███████╗███╗   ███╗██████╗ ██╗     ███████╗"
  sleep 0.03
  echo "╚══██╔══╝██╔════╝████╗ ████║██╔══██╗██║     ██╔════╝"
  sleep 0.03
  echo "   ██║   █████╗  ██╔████╔██║██████╔╝██║     █████╗  "
  sleep 0.03
  echo "   ██║   ██╔══╝  ██║╚██╔╝██║██╔═══╝ ██║     ██╔══╝  "
  sleep 0.03
  echo "   ██║   ███████╗██║ ╚═╝ ██║██║     ███████╗███████╗"
  sleep 0.03
  echo "   ╚═╝   ╚══════╝╚═╝     ╚═╝╚═╝     ╚══════╝╚══════╝"
  echo ""
  echo -e "${AMARELO}"
  sleep 0.05
  echo "            ██████╗ ███████╗"
  sleep 0.05
  echo "           ██╔═══██╗██╔════╝"
  sleep 0.05
  echo "           ██║   ██║███████╗"
  sleep 0.05
  echo "           ██║   ██║╚════██║"
  sleep 0.05
  echo "           ╚██████╔╝███████║"
  sleep 0.05
  echo "            ╚═════╝ ╚══════╝"
  echo ""
  echo -e "${AZUL}TempleOS Virtual Machine Launcher Created by Drastick${RESET}"
  echo -e "${AMARELO}=========================================${RESET}"
  echo ""
}

# ===== PRIMEIRA VERIFICAÇÃO RVNC =====
first_run_check() {
  if [ ! -f "$CONFIG_FILE" ]; then
    clear
    echo -e "${AMARELO}RVNC Viewer está instalado?${RESET}"
    echo "1 - Sim"
    echo "2 - Não"
    read -p "Escolha: " resp

    echo ""
    read -p "Tem certeza :/? (s/n): " confirm

    if [[ "$confirm" == "s" || "$confirm" == "S" ]]; then
      echo "ok" > "$CONFIG_FILE"
      echo -e "${VERDE}Configuração salva!${RESET}"
      sleep 1
    else
      echo -e "${VERMELHO}Cancelado, escolha novamente na próxima execução${RESET}"
      sleep 1
    fi
  fi
}

# ===== START VM =====
start_vm() {
  RAM="$1"
  [[ -z "$RAM" ]] && RAM="$RAM_DEFAULT"

  if ! [[ "$RAM" =~ ^[0-9]+$ ]]; then
    echo -e "${VERMELHO}RAM inválida${RESET}"
    return
  fi

  logo
  play_sound
  loading_bar

  echo -e "${AZUL}Verificando ISO...${RESET}"
  if [[ ! -f "$ISO" ]]; then
    echo -e "${AMARELO}Baixando...${RESET}"
    wget -O "$ISO" https://templeos.org/Downloads/TempleOS.ISO || return
  fi

  pkill -f qemu-system-x86_64 2>/dev/null

  PORT=5900
  DISPLAY=0

  while nc -z 127.0.0.1 $PORT >/dev/null 2>&1; do
    PORT=$((PORT+1))
    DISPLAY=$((DISPLAY+1))
  done

  echo -e "${VERDE}Porta $PORT | RAM ${RAM}MB${RESET}"

  qemu-system-x86_64 \
  -m "${RAM}M" \
  -cpu qemu64 \
  -cdrom "$ISO" \
  -boot d \
  -vga std \
  -vnc 127.0.0.1:$DISPLAY >/dev/null 2>&1 &

  sleep 2

  if ! pgrep -f qemu-system-x86_64 >/dev/null; then
    echo -e "${VERMELHO}Erro ao iniciar VM${RESET}"
    return
  fi

  echo -e "${AZUL}Aguardando VNC...${RESET}"
  for i in {1..30}; do
    nc -z 127.0.0.1 $PORT && break
    sleep 1
  done

  echo -e "${VERDE}Abrindo VNC...${RESET}"
  am start -a android.intent.action.VIEW -d vnc://127.0.0.1:$PORT
}

# ===== FUNÇÃO PARA ABRIR PLAY STORE MANUALMENTE =====
open_playstore() {
  echo -e "${AMARELO}Abrindo Play Store para instalar o RVNC Viewer...${RESET}"
  am start -a android.intent.action.VIEW -d https://play.google.com/store/apps/details?id=com.realvnc.viewer.android
  echo -e "${VERDE}Quando instalar, volte ao menu para iniciar a VM.${RESET}"
}

stop_vm() {
  pkill -f qemu-system-x86_64 2>/dev/null && echo -e "${VERDE}VM parada${RESET}" || echo -e "${VERMELHO}Nenhuma VM${RESET}"
}

status_vm() {
  pgrep -f qemu-system-x86_64 >/dev/null && echo -e "${VERDE}Rodando${RESET}" || echo -e "${VERMELHO}Parada${RESET}"
}

custom_ram() {
  read -p "RAM em MB: " RAM
  start_vm "$RAM"
}

# ===== EXECUÇÃO =====
first_run_check

while true; do
  logo
  echo -e "${AMARELO}1${RESET} Iniciar VM"
  echo -e "${AMARELO}2${RESET} RAM personalizada"
  echo -e "${AMARELO}3${RESET} Parar VM"
  echo -e "${AMARELO}4${RESET} Status da VM"
  echo -e "${AMARELO}5${RESET} Instalar RVNC Viewer (Play Store)"
  echo -e "${AMARELO}6${RESET} Sair"
  echo ""
  read -p "Escolha: " opt

  case $opt in
    1) start_vm ;;
    2) custom_ram ;;
    3) stop_vm ;;
    4) status_vm ;;
    5) open_playstore ;;
    6) exit ;;
    *) echo -e "${VERMELHO}Inválido${RESET}" ;;
  esac

  echo ""
  read -p "ENTER para continuar..."
done
```
após ter copiado e colado o comando, salve e saia dele 

# Uso

Faça a ativação do arquivo com o comando:

``chmod +x $PREFIX/bin/temple``

Após a instalação e configuração abra o servidor com o: 

``temple``

obs:o ``temple`` será ultilizado para executar o arquivo .sh que foi criado, então você só irá precisar por ele para rodar o script para abrir o servidor :)


# Resumo:

O comando instala A ISO do TempleOS e também leva automaticamente para a Playstore para fazer o download do RVNC VIEWER, além de criar um servidor com uma porta de ip aleatório, após isto ele leva automaticamente para o RVNC VIEWER 

# Atenção:

Este projeto ainda está em desenvolvimento, então poderá ter erros e problemas que poderão ser resolvidos em atualizações futuras. 

Espero que você consiga aproveitar para testar esse sistema maravilhoso chamado TempleOS ;)

# Créditos:
[Drastick123](https://github.com/Drastick123)    

[Ex3cutor76-V1](https://github.com/Ex3cutor76-V1)
