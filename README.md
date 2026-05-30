# Cyberdeck_z install

sudo apt update
sudo apt install python3 python3-pip python3-venv xterm

mkdir -p ~/pentest-dashboard
cd ~/pentest-dashboard
python3 -m venv venv
source venv/bin/activate
pip install PySide6
