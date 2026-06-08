# Phase 0 — Montage du Homelab

## Objectif
Préparer l'environnement de virtualisation pour tous les labos à venir.

## Environnement hôte
- Machine : HP ProBook, i7 12e gen, 32 Go RAM
- OS : Windows 11 Education
- Hyperviseur : VMware Workstation Pro 26.0.0

## Prérequis — Désactivation Hyper-V
Hyper-V est un hyperviseur de type 1 qui entre en conflit avec VMware.
Sans désactivation, VMware tourne dans Hyper-V ce qui dégrade les performances.

Commandes exécutées en PowerShell admin :
```powershell
bcdedit /set hypervisorlaunchtype off
Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All -NoRestart
Disable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform -NoRestart
Disable-WindowsOptionalFeature -Online -FeatureName HypervisorPlatform -NoRestart
Disable-WindowsOptionalFeature -Online -FeatureName Containers-DisposableClientVM -NoRestart
```

## VM 1 — Ubuntu Server 26.04 LTS
- RAM : 4 Go
- CPU : 2 cœurs
- Disque : 60 Go (single file)
- Réseau : NAT
- IP statique : 192.168.138.129/24
- Passerelle : 192.168.138.2
- DNS : 8.8.8.8 / 8.8.4.4
- SSH activé : oui

## VM 2 — Windows Server 2025
- RAM : 4 Go
- CPU : 2 cœurs
- Disque : 60 Go (single file)
- Réseau : NAT
- IP statique : 192.168.138.130/24
- Passerelle : 192.168.138.2
- DNS : 8.8.8.8 / 8.8.4.4
- Nom machine : WS2025-LAB
- Bureau à distance à activé
## Communication inter-VMs
- Windows hôte → Ubuntu : SSH sur 192.168.138.129
- Windows hôte → Windows Server : RDP sur 192.168.138.130
- Ubuntu <-> Windows Server : ping confirmé
## Configuration SSH Windows → VM
Fichier `C:\Users\warren djomo\.ssh\config` :