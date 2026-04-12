# ATLAS Server

Servidor doméstico Dell OptiPlex 3070 Micro configurado para gerenciar impressora 3D (Klipper), armazenamento em nuvem (Nextcloud), e serviços de automação.

## 📋 Especificações do Hardware

- **Modelo:** Dell OptiPlex 3070 Micro
- **CPU:** Intel Core i5-9500T (6 cores)
- **RAM:** 16GB DDR4
- **Armazenamento:**
  - SSD M.2 238GB (sistema)
  - HDD 465GB (dados - futuro)
- **Rede:** Ethernet Gigabit

## 🖥️ Sistema Operacional

- **OS:** Ubuntu Server 24.04 LTS
- **Kernel:** 6.8.0-107-generic
- **Arquitetura:** x86_64

## 🌐 Configuração de Rede

- **Hostname:** atlas
- **IP Fixo:** 192.168.1.110/24
- **Gateway:** 192.168.1.1
- **DNS:** 8.8.8.8, 1.1.1.1
- **Interface:** enp1s0 (Netplan)

## 🐳 Software Instalado

### Docker
- **Versão:** Docker CE (Community Edition)
- **Instalação:** Script oficial (`get.docker.com`)
- **Usuário:** guilherme adicionado ao grupo `docker`

### Portainer
- **Versão:** Portainer CE (Community Edition) - latest
- **Porta:** 9000
- **Acesso:** http://192.168.1.110:9000
- **Restart Policy:** always

## 📂 Estrutura de Serviços Planejados
ATLAS
├── Klipper + Moonraker + Mainsail (Semana A2)
├── Nextcloud AIO (Semana A3)
├── Immich (Semana A4)
├── AdGuard Home (Semana A4)
├── Uptime Kuma (Semana A4)
└── n8n (Semana A7)

## 🔧 Comandos Executados

### Instalação do Docker
```bash
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker guilherme
```

### Instalação do Portainer
```bash
docker run -d -p 9000:9000 --name portainer --restart always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
```

## 📅 Histórico

- **2026-04-12:** Instalação Ubuntu Server + Docker + Portainer
- **Próximo:** Migração Klipper do notebook → ATLAS

## 🎯 Objetivo do Projeto

Substituir o notebook Dell Inspiron (atual host do Klipper) por um servidor dedicado mais confiável, expandindo para serviços de automação residencial e substituição de Google Drive/Photos.

---

**Projeto:** Do Zero ao Dev Sênior - Fase 0 (ATLAS)  
**Criador:** Guilherme Bovmont  
**Data de Início:** Abril 2026
