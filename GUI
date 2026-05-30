import sys
import subprocess
from datetime import datetime

from PySide6.QtWidgets import (
    QApplication,
    QWidget,
    QPushButton,
    QTextEdit,
    QVBoxLayout,
    QHBoxLayout,
    QGridLayout,
    QLabel,
    QLineEdit,
    QGroupBox,
)
from PySide6.QtCore import Qt


class PentestDashboard(QWidget):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("Drey Pentest Linux Dashboard")
        self.resize(1000, 700)

        self.target_input = QLineEdit()
        self.target_input.setPlaceholderText("Target IP / Domain, z.B. 10.10.10.10")

        self.output = QTextEdit()
        self.output.setReadOnly(True)

        main_layout = QVBoxLayout()

        title = QLabel("Pentest Linux Dashboard")
        title.setAlignment(Qt.AlignCenter)
        title.setStyleSheet("font-size: 26px; font-weight: bold; padding: 12px;")

        main_layout.addWidget(title)
        main_layout.addWidget(self.target_input)

        button_layout = QHBoxLayout()

        button_layout.addWidget(self.create_network_box())
        button_layout.addWidget(self.create_tools_box())
        button_layout.addWidget(self.create_htb_box())
        button_layout.addWidget(self.create_system_box())

        main_layout.addLayout(button_layout)
        main_layout.addWidget(self.output)

        self.setLayout(main_layout)

        self.setStyleSheet("""
            QWidget {
                background-color: #111827;
                color: #e5e7eb;
                font-family: Arial;
                font-size: 14px;
            }

            QGroupBox {
                border: 1px solid #374151;
                border-radius: 10px;
                margin-top: 12px;
                padding: 10px;
                font-weight: bold;
            }

            QGroupBox::title {
                subcontrol-origin: margin;
                left: 10px;
                padding: 0 5px;
            }

            QPushButton {
                background-color: #1f2937;
                border: 1px solid #4b5563;
                border-radius: 8px;
                padding: 10px;
                color: #f9fafb;
            }

            QPushButton:hover {
                background-color: #374151;
            }

            QPushButton:pressed {
                background-color: #4b5563;
            }

            QLineEdit {
                background-color: #1f2937;
                border: 1px solid #4b5563;
                border-radius: 8px;
                padding: 10px;
                color: #f9fafb;
            }

            QTextEdit {
                background-color: #030712;
                border: 1px solid #374151;
                border-radius: 8px;
                padding: 10px;
                color: #d1d5db;
                font-family: monospace;
            }
        """)

    def log(self, text):
        timestamp = datetime.now().strftime("%H:%M:%S")
        self.output.append(f"[{timestamp}] {text}")

    def get_target(self):
        return self.target_input.text().strip()

    def run_command(self, command):
        self.log(f"$ {command}")

        try:
            result = subprocess.run(
                command,
                shell=True,
                capture_output=True,
                text=True
            )

            if result.stdout:
                self.output.append(result.stdout)

            if result.stderr:
                self.output.append(result.stderr)

        except Exception as e:
            self.log(f"ERROR: {e}")

    def launch_program(self, command):
        self.log(f"Starting: {command}")

        try:
            subprocess.Popen(
                command,
                shell=True,
                stdout=subprocess.DEVNULL,
                stderr=subprocess.DEVNULL,
                start_new_session=True
            )
            self.log("Started successfully.")
        except Exception as e:
            self.log(f"ERROR: {e}")

    def run_with_target(self, command_template):
        target = self.get_target()

        if not target:
            self.log("Please enter a target first.")
            return

        command = command_template.replace("{target}", target)
        self.run_command(command)

    def create_network_box(self):
        box = QGroupBox("Network")
        layout = QGridLayout()

        buttons = [
            ("Show IP", lambda: self.run_command("ip a")),
            ("Show Routes", lambda: self.run_command("ip route")),
            ("Check tun0", lambda: self.run_command("ip a show tun0")),
            ("DNS Lookup", lambda: self.run_with_target("dig {target}")),
            ("Ping Target", lambda: self.run_with_target("ping -c 4 {target}")),
            ("Nmap Basic", lambda: self.run_with_target("nmap -sV -sC {target}")),
        ]

        self.add_buttons(layout, buttons)
        box.setLayout(layout)
        return box

    def create_tools_box(self):
        box = QGroupBox("Tools")
        layout = QGridLayout()

        buttons = [
            ("Start GQRX", lambda: self.launch_program("gqrx")),
            ("Start BloodHound", lambda: self.launch_program("bloodhound")),
            ("Start Burp", lambda: self.launch_program("burpsuite")),
            ("Start Wireshark", lambda: self.launch_program("wireshark")),
            ("Open Terminal", lambda: self.launch_program("xterm")),
            ("Open Firefox", lambda: self.launch_program("firefox")),
        ]

        self.add_buttons(layout, buttons)
        box.setLayout(layout)
        return box

    def create_htb_box(self):
        box = QGroupBox("HTB")
        layout = QGridLayout()

        buttons = [
            ("Check VPN", lambda: self.run_command("ip a | grep -A 5 tun0")),
            ("My IP", lambda: self.run_command("curl -s ifconfig.me")),
            ("Open Notes", lambda: self.launch_program("xdg-open ~/HTB")),
            ("Open Downloads", lambda: self.launch_program("xdg-open ~/Downloads")),
            ("Start Neo4j", lambda: self.launch_program("xterm -e 'sudo neo4j console'")),
            ("Start Python Server", lambda: self.launch_program("xterm -e 'python3 -m http.server 8000'")),
        ]

        self.add_buttons(layout, buttons)
        box.setLayout(layout)
        return box

    def create_system_box(self):
        box = QGroupBox("System")
        layout = QGridLayout()

        buttons = [
            ("Update APT", lambda: self.launch_program("xterm -e 'sudo apt update; bash'")),
            ("Show Disks", lambda: self.run_command("lsblk -f")),
            ("Disk Usage", lambda: self.run_command("df -h")),
            ("Processes", lambda: self.run_command("ps aux --sort=-%mem | head")),
            ("Reboot", lambda: self.launch_program("xterm -e 'sudo reboot'")),
            ("Shutdown", lambda: self.launch_program("xterm -e 'sudo shutdown now'")),
        ]

        self.add_buttons(layout, buttons)
        box.setLayout(layout)
        return box

    def add_buttons(self, layout, buttons):
        row = 0
        col = 0

        for text, action in buttons:
            button = QPushButton(text)
            button.clicked.connect(action)
            layout.addWidget(button, row, col)

            col += 1
            if col >= 2:
                col = 0
                row += 1


if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = PentestDashboard()
    window.show()
    sys.exit(app.exec())
