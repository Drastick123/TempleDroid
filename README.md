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

Instale a ISO:

``wget https://templeos.org/Downloads/TempleOS.ISO``

Execute o comando para criar um arquivo temple

``cat > temple << 'EOF'
#!/data/data/com.termux/files/usr/bin/bash
echo "Iniciando TempleOS..."
qemu-system-x86_64 \
-m 512M \
-cpu qemu64 \
-cdrom $HOME/TempleOS.ISO \
-boot d \
-vga std \
-vnc 127.0.0.1:0 &
echo "Aguardando VNC iniciar..."
while ! nc 127.0.0.1 5900 >/dev/null 2>&1; do
    sleep 1
done
echo "Abrindo VNC automaticamente..."
am start -a android.intent.action.VIEW -d vnc://127.0.0.1:5900
EOF``

Faça a ativação do arquivo com o comando:

``chmod +x temple mv temple $PREFIX/bin/``

Intale o RNVC VIEWER:

``"echo "Verificando se o VNC Viewer está instalado..."
if pm list packages | grep -q "com.realvnc.viewer.android"; then
    echo "App encontrado! Abrindo conexão..."
 am start -a android.intent.action.VIEW -d vnc://127.0.0.1:5900
else   echo "VNC Viewer não instalado!"
    echo "Abrindo Play Store..."
    am start -a android.intent.action.VIEW -d https://play.google.com/store/apps/details?id=com.realvnc.viewer.android
  echo "Instale o app e rode o comando novamente."
fi``

# Uso

Ai após isso só abrir o servidor com o: ``./Temple.sh``

E abrir o VNC viewer.

Créditos:


