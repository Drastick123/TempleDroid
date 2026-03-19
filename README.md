# TempleDroid
Recentemente descobri uma forma de usar o TempleOS no termux em uma VM, então eu quis compartilhar isso aqui, que inclusive meu amigo, Ex3cutor76-V1 me ajudou com essa ideia.

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

echo " Verificando TempleOS ISO..."
if [[ ! -f "$HOME/TempleOS.ISO" ]]; then
  echo " TempleOS não encontrado. Baixando..."
  wget -O $HOME/TempleOS.ISO https://templeos.org/Downloads/TempleOS.ISO
else
  echo " TempleOS já está baixado"
fi

echo " Verificando VNC Viewer..."
if pm list packages | grep -q "com.realvnc.viewer.android"; then
  echo " VNC Viewer instalado"
else
  echo " VNC Viewer não instalado"
  echo " Abrindo Play Store..."
  am start -a android.intent.action.VIEW -d https://play.google.com/store/apps/details?id=com.realvnc.viewer.android
  echo "Instale o app e volte aqui..."
  read -p "Pressione ENTER quando terminar..."
fi

# Mata QEMU antigo
pkill qemu 2>/dev/null

echo " Procurando porta VNC livre..."

PORT=5900
DISPLAY=0

while nc -z 127.0.0.1 $PORT 2>/dev/null; do
  PORT=$((PORT+1))
  DISPLAY=$((DISPLAY+1))
done

echo " Porta livre: $PORT (display :$DISPLAY)"

echo " Iniciando TempleOS..."
qemu-system-x86_64 \
-m 512M \
-cpu max \
-cdrom $HOME/TempleOS.ISO \
-boot d \
-vga std \
-vnc 127.0.0.1:$DISPLAY &

echo " Aguardando VNC..."

while ! nc -z 127.0.0.1 $PORT 2>/dev/null; do
  sleep 1
done

sleep 2

echo " Conectando..."

am start -a android.intent.action.VIEW -d vnc://127.0.0.1:$PORT

echo "TempleOS rodando :)"
```
após ter copiado e colado o comando, salve e saia dele 

# Uso

Faça a ativação do arquivo com o comando:

``chmod +x $PREFIX/bin/temple``



# Resumo:

O comando instala A ISO do TempleOS e também leva automaticamente para a Playstore para fazer o download do RVNC VIEWER, além de criar um servidor com uma porta de ip aleatório, após isto ele leva automaticamente para o RVNC VIEWER 

# Atenção:


Após a instalação e configuração abra o servidor com o: 

``temple``

Espero que você aproveite para testar esse sistema maravilhoso chamado TempleOS ;)

# Créditos:


