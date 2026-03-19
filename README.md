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
```
#!/data/data/com.termux/files/usr/bin/bash
echo "Verificando TempleOS ISO..."
if [[ ! -f "$HOME/TempleOS.ISO" ]]; then
echo "TempleOS não encontrado. Baixando..."
wget -O $HOME/TempleOS.ISO https://templeos.org/Downloads/TempleOS.ISO
else
echo "TempleOS já está baixado"
fi

echo "Verificando VNC Viewer..."
if pm list packages | grep -q "com.realvnc.viewer.android"; then
echo "VNC Viewer instalado"
else
echo "VNC Viewer não instalado"
echo "Abrindo Play Store para instalar..."
am start -a android.intent.action.VIEW -d https://play.google.com/store/apps/details?id=com.realvnc.viewer.android
echo "Instale o app e depois volte aqui..."
read -p "Pressione ENTER quando terminar..."
fi
echo " Iniciando TempleOS..."
qemu-system-x86_64 \
-m 512M \
-cpu max \
-cdrom $HOME/TempleOS.ISO \
-boot d \
-vga std \
-device usb-tablet \
-vnc 127.0.0.1:0 &

echo "Aguardando VNC..."

while ! nc 127.0.0.1 5900 >/dev/null 2>&1; do
sleep 1
done

sleep 2

echo " Conectando automaticamente..."
am start -a android.intent.action.VIEW -d vnc://127.0.0.1:5900
echo " Tudo pronto! Hello wolrd! :)"

Faça a ativação do arquivo com o comando:

``chmod +x temple mv temple $PREFIX/bin/``

# Uso
```
Ai após isso só abrir o servidor com o: ``temple``


Créditos:


