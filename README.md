# 🖥️ Homeserver Ansible Setup

Dieses Ansible-Projekt automatisiert die Installation, Konfiguration und Verwaltung eines privaten Homeservers auf **Debian 13**.  
Es bietet ein schlankes, sicheres Setup für Docker-Container und Bare-Metal-Dienste, inklusive Monitoring, SNMP-Integration und automatischen Updates.

---

## 🎯 Ziel

- Reduzierung unnötiger Dienste und Komplexität  
- Einheitliches Deployment auf neuen Servern  
- Sicheres Management von Docker-Containern, Bare-Metal-Services und Monitoring  
- Sicherheitsbewusstes Setup: minimale Root-Nutzung, abgesicherte SSH-Zugänge
- Einfaches Re-Deployment auf neuer Hardware oder nach Neuinstallation  

---

## 🔮 Zukünftige Ziele

Geplante Erweiterungen und Verbesserungen für Sicherheit, Effizienz und Wartung des Servers:

🔒 Sicherheit & Hardening

 - SSH-Hardening (PermitRootLogin no, PasswordAuthentication no, nur SSH-Keys)
 - Fail2Ban zum Schutz vor Brute-Force-Angriffen
 - Automatische Sicherheitsupdates (unattended-upgrades)
 - Firewall-Regeln via UFW oder nftables

🌐 Netzwerk & Firewall

 - Standardregel: „deny all incoming, allow outgoing“
 - Freigabe nur definierter Ports (SSH, 80, 443 etc.)
 - VPN-Zugang (WireGuard) für Remote-Management
 - Automatischer Portscan mit nmap nach Deployments

⚡ Energieeffizienz

 - CPU-Power-Management (cpufrequtils, Governor-Anpassung)
 - HDD-Spin-Down über hdparm
 - Automatisches Herunterfahren über NUT-Server bei Stromausfall

📊 Monitoring & Transparenz

 - Erweiterte Checkmk-Checks (SMART, Temp, Load)
 - SNMP-Integration für Netzwerkgeräte und USV
 - Discord-Benachrichtigungen bei System- oder Containerfehlern
 - Docker-Health-Checks & Watchtower-Reporting

🧩 Struktur & Skalierbarkeit

 - Weitere Modularisierung der Rollen (z. B. Firewall, Backup, Security)

---

## 🧩 Server-Hardware & Umgebung

Der Homeserver wurde auf moderne, effiziente Hardware umgestellt, um eine gute Balance aus **Leistung, Energieeffizienz und Zukunftssicherheit** zu erreichen.

### ⚙️ Hardware-Spezifikationen

| Komponente | Beschreibung |
|-------------|--------------|
| **CPU** | AMD Ryzen 5 5600G – APU mit integrierter Grafik und sehr gutem Energieverbrauch |
| **RAM** | 128 GB DDR4 @ 3200 MT/s – ursprünglich für viele VMs unter Proxmox, bietet jetzt viel Puffer für Container und Caching |
| **Systemlaufwerk** | 2 TB NVMe SSD – hohe I/O-Performance für System, Docker-Container und Datenbanken |
| **Datenspeicher** | 2 × 4 TB HDD – massig Platz für Medien, Backups und persistenten Speicher |
| **Netzwerkkarte** | Dual-Port 10 Gbit SFP+ – ideal für hohen Datendurchsatz im Heimnetz oder zwischen NAS und Backup-System |
| **Mainboard** | Gigabyte B550M AORUS ELITE AX – stabil, gut ausgestattet und zukunftssicher |
| **Kühler** | NZXT Low-Profile CPU Cooler – leise und platzsparend |
| **Gehäuse** | Jonsbo N4 NAS-Gehäuse – kompaktes Design mit Platz für mehrere 3,5"-HDDs |

### 💡 Systemumgebung

- **Betriebssystem:** Debian 13 (Trixie)  
- **Verwaltung:** vollständig über Ansible automatisiert  
- **Dienste:** Mischung aus Docker-Containern und Bare-Metal-Anwendungen  
- **Monitoring:** Checkmk mit SNMP-Integration  
- **Netzwerk:** statische IP, 10 Gbit, Zugriff via SSH-Schlüssel  

---

## 🔄 Von Proxmox zu Debian

Der Server lief ursprünglich unter **Proxmox VE** mit mehreren virtuellen Maschinen.  
Der Wechsel zu **Debian 13** erfolgte, um das Setup zu vereinfachen und den Ressourcenverbrauch zu senken.

**Gründe für den Wechsel:**
- Reduktion unnötiger Dienste und Virtualisierungs-Overhead  
- Direkter Zugriff auf Hardware ohne VM-Schicht  
- Weniger Komplexität bei Updates und Backups  
- Potenziell geringerer Stromverbrauch  
- Bessere Integration mit Ansible  

Das neue Setup läuft nativ auf Debian und nutzt Docker-Container für alle modularen Dienste – leichtgewichtig, konsistent und einfach wartbar.

---

## 🧱 Unterstützte Dienste

### 🐳 Docker-Container

| Dienst | Beschreibung |
|--------|---------------|
| **Omada Controller** | Verwaltung von Netzwerkgeräten (Access Points, Switches, Router) |
| **Peanut** | Frontend zur Visualisierung von USV-Daten (über NUT-Server) und Medienstatus |
| **Nextcloud** | Private Cloud für Dateien, Kalender, Kontakte und Backups |
| **Homepage** | Übersichtsseite für lokale Services mit Icons und Links |
| **Nginx Proxy Manager** | Reverse Proxy mit SSL-Zertifikatsverwaltung |
| **Home Assistant** | Smart-Home-Steuerung und Automatisierungen |
| **Watchtower** | Automatische Updates für alle Container |
| **Pi-hole** | DNS-Filter und Werbeblocker für das gesamte Netzwerk |
| **Checkmk** | Monitoring-Plattform für Hosts, Container und Dienste (inkl. Agent-Installation) |

### 🧰 Bare-Metal

| Dienst | Beschreibung |
|--------|---------------|
| **Plex Media Server** | Lokaler Medienserver für Filme, Serien und Musik |
| **NUT-Server** | Kommunikation mit der USV, Bereitstellung von Statusdaten über SNMP/Peanut |

---

## ⚙️ Installation

### 🧾 Voraussetzungen

- Debian 13 mit SSH-Zugang  
- Benutzer mit `sudo`-Rechten  
- Ansible auf dem Steuerrechner installiert  
- SSH-Schlüssel-Authentifizierung eingerichtet  

---

### 🚀 Schritt 1: Repository klonen

```bash
git clone <REPO_URL>
cd <REPO_NAME>
ansible-playbook -i inventory.ini playbooks/server-install.yml --ask-vault-pass
