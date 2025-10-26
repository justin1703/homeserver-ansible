# Homeserver Ansible Setup

Dieses Ansible-Projekt automatisiert die Installation, Konfiguration und Verwaltung eines privaten Homeservers auf **Debian 13**.  
Es bietet ein schlankes, sicheres Setup für Docker-Container und Bare-Metal-Dienste, inklusive Monitoring, SNMP-Integration und automatischen Updates.

---

## Ziel

- Reduzierung unnötiger Dienste und Komplexität  
- Einheitliches Deployment auf neuen Servern  
- Sicheres Management von Docker-Containern, Bare-Metal-Services und Monitoring  
- Sicherheitsbewusstes Setup: minimale Root-Nutzung, abgesicherte SSH-Zugänge, Firewall-Konfiguration

---

## Unterstützte Dienste

### Docker-Container

| Dienst | Beschreibung |
|--------|-------------|
| Omada Controller | Verwaltung von Netzwerkgeräten (APs, Switches, Router) |
| Peanut | Media-Management und Bibliotheksverwaltung |
| Nextcloud | Private Cloud für Dateien, Kalender und Kontakte |
| Homepage | Persönliche Webseite für Informationen oder Projekte |
| Nginx Proxy Manager | Reverse Proxy und SSL-Management für Webservices |
| Home Assistant | Hausautomation und Smart-Home-Steuerung |
| Watchtower | Automatische Updates für Docker-Container |
| Pi-hole | Netzwerk-Werbeblocker und DNS-Filterung |
| **Checkmk** | Monitoring-Lösung zur Überwachung von Hosts, Containern und Diensten |

### Bare-Metal

| Dienst | Beschreibung |
|--------|-------------|
| Plex | Media-Server für Filme, Serien und Musik |
| NUT-Server | USV-Monitoring und Energieverwaltung |

---

## Installation

### Voraussetzungen

- Debian 13 Server mit SSH-Zugang  
- Ansible auf dem Rechner installiert  
- Benutzer für Deployment

### Schritt 1: Repository klonen

```bash
git clone <REPO_URL>
cd <REPO_NAME>
