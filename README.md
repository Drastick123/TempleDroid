# TempleDroid
Recentemente descobri uma forma de usar o TempleOS no termux em uma VM, então eu quis compartilhar isso aqui, que inclusive meu amigo, Ex3cutor76-V1 me ajudou com essa ideia.

# O que é o TempleOS

resumindo: TempleOS é um sistema operacional criado do zero por Terry A. Davis que de acordo com ele seria um "Templo de Deus", mais informações no site do meu amigo:
https://ex3cutor76-v1.github.io/Math-info/Sistemas%20operacionais/TempleOS/index.html

# Requisitos

``
pkg install root-repo``

``pkg install x11-repo``

``pkg install qemu-system-x86_64 ``

``pkg install netcat-openbsd``



# Instalação 

Atualize o sistema: ``pkg update && pkg upgrade -y``

Instale a iso: ``wget https://templeos.org/Downloads/TempleOS.ISO``

Rode o comando  no termux para ser levado para a página da Playstore 
``am start -a android.intent.action.VIEW -d https://play.google.com/store/apps/details?id=com.realvnc.viewer.android``

Dê permissão ao arquivo .sh: chmod +x Temple.sh

# Uso

Ai após isso só abrir o servidor com o: ``./Temple.sh``

E abrir o VNC viewer.

Créditos:


