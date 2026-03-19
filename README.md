# TempleDroid
Recentemente descobri uma forma de usar o TempleOS no termux em uma VM, então eu quis compartilhar isso aqui, que inclusive meu amigo, Ex3cutor76-V1 me ajudou com essa ideia.

# O que é o TempleOS

resumindo: TempleOS é um sistema operacional criado do zero por Terry A. Davis que de acordo com ele seria um "Templo de Deus", mais informações no site do meu amigo:
https://ex3cutor76-v1.github.io/Math-info/Sistemas%20operacionais/TempleOS/index.html

# Requisitos

``pkg install root-repo
pkg install x11-repo
pkg install qemu-system-x86_64 wget termux-api netcat-openbsd -y``


# Instalação 

Atualize o sistema: ``pkg update && pkg upgrade -y``

Instale a iso: ``wget https://templeos.org/Downloads/TempleOS.ISO``

Crie um arquivo sh: ``touch Temple.sh``

Dentro do arquivo coloque esse script:

echo "Abrindo página para instalar VNC Viewer..."
am start -a android.intent.action.VIEW -d https://play.google.com/store/apps/details?id=com.realvnc.viewer.android

echo "Aguarde instalar o app e volte aqui..."
sleep 10

echo "Baixando TempleOS..."
wget -O TempleOS.ISO https://templeos.org/Downloads/TempleOS.ISO

echo "Criando comando automático..."

cat > temple << 'EOF'
#!/data/data/com.termux/files/usr/bin/bash

echo "Iniciando TempleOS (64 bits)..."

qemu-system-x86_64 \
-m 512M \
-cpu qemu64 \
-cdrom $HOME/TempleOS.ISO \
-boot d \
-vga std \
-usb -device usb-kbd \
-vnc 127.0.0.1:0 &

echo "Aguardando servidor VNC iniciar..."

# Espera até a porta 5900 abrir
while ! nc -z 127.0.0.1 5900; do
    sleep 1
done

echo "Servidor VNC ativo! Abrindo app..."

am start -a android.intent.action.VIEW -d vnc://127.0.0.1:5900

EOF

chmod +x temple
mv temple $PREFIX/bin/

echo "Tudo pronto! Use: ./Temple.sh

Após isso salve com Ctrl + O e depois Ctrl + X

Dê permissão ao arquivo .sh: chmod +x Temple.sh

Ai após isso só abrir o servidor com o: ``./Temple.sh``

E abrir o VNC viewer 
