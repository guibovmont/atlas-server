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

## 🐙 Instalação do Klipper (Mini-Projeto 1 - Semana A2)

### Software Instalado via KIAUH

- **KIAUH:** v6.1.0 (Klipper Installation And Update Helper)
- **Klipper:** Instalado via KIAUH
- **Moonraker:** Instalado via KIAUH (API)
- **Mainsail:** Instalado via KIAUH (Interface Web)
- **Acesso Mainsail:** http://192.168.1.110

### Estrutura de Diretórios

\```
~/printer_data/
├── config/
│   ├── printer.cfg (copiado do notebook Dell Inspiron)
│   ├── mainsail.cfg (link simbólico)
│   ├── timelapse.cfg (copiado do notebook)
│   ├── acelerometros.cfg (copiado do notebook)
│   ├── crowsnest.conf (copiado do notebook)
│   ├── KlipperScreen.conf (copiado do notebook)
│   └── moonraker.conf (criado manualmente)
├── logs/
├── gcodes/
└── comms/
\```
### Grupos de Usuário

```bash
# Usuário guilherme adicionado aos grupos:
- tty (acesso a portas seriais)
- dialout (comunicação USB com impressora)
```

### Status Atual

- ✅ Klipper instalado e configurado
- ✅ Moonraker funcionando (porta 7125)
- ✅ Mainsail acessível via browser
- ⏳ **Aguardando:** Conexão física da impressora (Mini-Projeto 2)

### Próximos Passos (Mini-Projeto 2)

1. Desconectar impressora Ender 3 V2 do notebook
2. Conectar cabo USB no ATLAS
3. Verificar device: `ls /dev/ttyUSB*`
4. Fazer impressão de teste
5. Reconfigurar PAD7 para IP do ATLAS

---

📅 Histórico:

- **2026-04-12:** Instalação Ubuntu Server + Docker + Portainer + Repositório GitHub
- **2026-04-13:** Instalação Klipper + Moonraker + Mainsail via KIAUH + Backup de configurações

## 🎯 Objetivo do Projeto

Substituir o notebook Dell Inspiron (atual host do Klipper) por um servidor dedicado mais confiável, expandindo para serviços de automação residencial e substituição de Google Drive/Photos.

---

**Projeto:** Do Zero ao Dev Sênior - Fase 0 (ATLAS)  
**Criador:** Guilherme Bovmont  
**Data de Início:** Abril 2026
