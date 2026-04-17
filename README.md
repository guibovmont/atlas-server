# 🏛️ Atlas Server

Repositório de documentação e configurações do servidor principal (OptiPlex 3070) para automação, Docker e Klipper.

---

## 🔥 PHOENIX - Impressora 3D

**PHOENIX** é uma impressora em constante evolução, renascendo das cinzas a cada upgrade.

### ⚙️ Configuração Hero Me Gen 7 (anterior)

**Base:** Creality Ender 3 V2 Neo  
**Toolhead:** Hero Me Gen 7  
**Extrusora:** Sprite SE Direct Drive  
**Hotend:** MK8 original  
**Cooling:**
- Fan peça: 5015
- Fan hotend: 4020

### ⚙️ Configuração Stealthburner (atual - 16/ABR/2026)

**Toolhead:** Stealthburner  
**Hotend:** MK8 original  
**Cooling:**
- Fan frontal: 4010 (original Ender 3 V2 Neo)
- Fan lateral: 5015 (part cooling)

**Sensores:**
- Z-Probe: BTT Eddy (indutivo, sem contato)
- Filamento: BTT SFS (switch PA4 + motion PB0)
- Acelerômetros: Dual RP2040 LIS2DW (X toolhead, Y mesa)

**Mecânica:**
- Dual Z, fuso lubrificado
- Área: 220×220×145mm (altura reduzida por cable chain)
- Cama: manta isolante + molas reforçadas

**Stack:**
- Firmware: Klipper v0.13
- Servidor: ATLAS (OptiPlex 3070 Micro)
- Interface: Mainsail + KlipperScreen (BTT Pad 7)

---

## 🖨️ Configurações Klipper

### Printer.cfg

**Última atualização:** 16/04/2026

**Mudanças recentes:**
- 🔥 Sensor de filamento BTT SFS instalado (switch + motion)
- ⚙️ Ajuste altura stepper Z devido à cable chain na toolhead
- 🔧 Atualização input shaper (X: 40.6Hz, Y: 38.6Hz)

**Hardware atual:**
- **Toolhead:** Stealthburner
- **Probe:** BTT Eddy
- **Acelerômetros:** RP2040 (2 unidades)
- **Extrusora:** Orbiter 2.0
- **Sensor filamento:** BTT SFS

### Webcam (Crowsnest)

**Status:** ✅ Configurado e funcionando (15/04/2026)

**Configuração:**
- **Device:** `/dev/video0` (Jieli USB Composite)
- **Resolução:** 1280x720 (HD nativo)
- **FPS:** 30
- **Encoder:** Hardware (GPU Intel)
- **Porta:** 8080
- **URL Stream:** `http://192.168.1.110:8080/?action=stream`

**Notas:**
- Device corrigido de `/dev/video2` para `/dev/video0` durante migração servidor
- Hardware encoding (`--encoder=HW`) ativado para melhor performance
- Flag `--host 0.0.0.0` adicionada para permitir acesso via rede

### 🎨 Configurações OrcaSlicer

#### Organização por Hardware

Os perfis estão separados por **hardware** (hotend), que é o componente menos mutável.

#### Tipos de Perfil

- **machine/**: Configurações físicas da impressora (dimensões, limites)
- **process/**: Parâmetros de impressão (layer height, infill, velocidade)
- **filament/**: Configurações de material (temperatura, retração)

#### Hero Me 7 (Histórico)

Hardware anterior. Mantido para **análise comparativa** futura.

- **Materiais suportados:** PLA, PETG, ABS, TPU 95A
- **Perfis:** Acabamento (0.12mm) e Function (0.24mm)

#### StealthBurner (Atual)

Hardware em uso desde 10/04/2026.

- **Material principal:** PETG
- **Perfis:** Acabamento (0.12mm) e Function (0.24mm)

---

## 🖥️ ATLAS - Servidor

### 🛠️ Especificações Técnicas

**Hardware:**
- **Máquina:** Dell OptiPlex 3070 Micro
- **CPU:** Intel i5-9500T (6 Cores / 6 Threads)
- **RAM:** 16GB DDR4
- **Storage Interno:** M.2 240GB (OS) + HDD 500GB (Data)
- **IP Estático:** `192.168.1.110`

**Software & Localização:**
- **OS:** Ubuntu 24.04.4 LTS
- **Hostname:** `Atlas`
- **Timezone:** `America/Sao_Paulo` (UTC-3)

---

### 💾 Storage & Backup

#### Hardware Externo (Orico)

- **Dock:** Orico Dual-Bay USB-C
- **HDs:** 2x WD Purple 2TB (surveillance-grade)
- **Interface:** USB 3.0/USB-C
- **Capacidade total:** ~3.6 TiB (4TB decimal)

#### Configuração LVM

O storage foi configurado como **JBOD (Linear)** usando LVM para facilitar expansão futura:

```bash
# 1. Criar Physical Volumes
sudo pvcreate /dev/sdc1 /dev/sdd1

# 2. Criar Volume Group
sudo vgcreate storage-vg /dev/sdc1 /dev/sdd1

# 3. Criar Logical Volume (usando 100% do espaço)
sudo lvcreate -l 100%FREE -n storage-lv storage-vg

# 4. Formatar com ext4
sudo mkfs.ext4 -L storage /dev/storage-vg/storage-lv

# 5. Montar
sudo mkdir -p /mnt/storage
sudo mount /dev/storage-vg/storage-lv /mnt/storage

# 6. Adicionar ao fstab (montagem automática)
echo '/dev/storage-vg/storage-lv /mnt/storage ext4 defaults 0 2' | sudo tee -a /etc/fstab
```

**Por que LVM?**
- ✅ **Expansível:** Adicionar novos HDs sem reformatar
- ✅ **Snapshots:** Backup incremental
- ✅ **Flexível:** Redimensionar volumes dinamicamente

**Por que JBOD em vez de RAID 1?**
- ✅ **Capacidade total:** 3.6TB utilizáveis (vs 2TB em RAID 1)
- ✅ **Backup 3-2-1:** Risco mitigado com estratégia de backup robusta
- ⚠️ **Sem redundância:** Se um disco falhar, perde tudo (dependência do backup)

#### Compartilhamento de Rede (Samba)

**Status:** ✅ Configurado e funcionando (15/04/2026)

**Compartilhamento:**
- **Nome:** `storage`
- **Path:** `/mnt/storage`
- **Protocolo:** SMB/CIFS (Samba)
- **Acesso:** `\\192.168.1.110\storage`

**Segurança (3 camadas):**
1. **Autenticação:** Usuário `guilherme` + senha obrigatória
2. **Restrição IP:** Somente `192.168.1.21` (KRONOS) permitido
3. **Permissões Linux:** `0775` (dono/grupo escrevem, outros só leem)

**Acesso do Windows (KRONOS):**
```cmd
net use S: \\192.168.1.110\storage /user:guilherme /persistent:yes
```

**Configuração Samba (`/etc/samba/smb.conf`):**
```ini
[storage]
   path = /mnt/storage
   browseable = yes
   read only = no
   valid users = guilherme
   hosts allow = 192.168.1.21
   hosts deny = 0.0.0.0/0
   create mask = 0775
   directory mask = 0775
```

**Comandos Úteis:**
```bash
# Testar configuração Samba
sudo testparm -s

# Reiniciar serviço
sudo systemctl restart smbd

# Verificar status
systemctl status smbd
```

#### Estratégia de Backup (3 camadas)

**Camada 1 - Dados ativos:**
- `/mnt/storage` (3.6TB) → Nextcloud, Immich, projetos

**Camada 2 - Lixeira local:**
- Nextcloud trash (30 dias retenção) → Recovery rápido

**Camada 3 - Backup offsite:**
- AWS Glacier Deep Archive ($4/mês) → Backup completo 4TB
- Google Drive 100GB grátis → Backup seletivo (críticos)

#### Expansão Futura

Quando necessário (~3.5TB ocupados), adicionar **2 HDs extras** ao LVM:

```bash
# Adicionar novos PVs ao VG existente
sudo pvcreate /dev/sde1 /dev/sdf1
sudo vgextend storage-vg /dev/sde1 /dev/sdf1
sudo lvextend -l +100%FREE /dev/storage-vg/storage-lv
sudo resize2fs /dev/storage-vg/storage-lv
```

---

## 🗂️ Estrutura do Repositório
atlas-server/
├── configs/
│   ├── klipper/              # Configurações da impressora 3D
│   │   ├── printer.cfg
│   │   ├── moonraker.conf
│   │   ├── KlipperScreen.conf
│   │   ├── crowsnest.conf
│   │   ├── timelapse.cfg
│   │   └── acelerometros.cfg
│   └── orcaslicer/           # Perfis de impressão
│       ├── hero-me-7/        # Hardware anterior (histórico)
│       │   ├── machine/
│       │   ├── process/
│       │   └── filament/
│       └── stealthburner/    # Hardware atual
│           ├── machine/
│           ├── process/
│           └── filament/

---

## 🤖 Projeto OpenClaw

Este repositório serve como base de configurações para o **OpenClaw**, um agente IA que controlará ativamente a impressão 3D.

O OpenClaw usará esses arquivos para:

- Auditar evolução de configurações ao longo do tempo
- Comparar desempenho entre diferentes hardwares
- Ajustar perfis de impressão baseado em resultados

---

## 📊 Histórico de Decisões

Consulte os **commits** do Git para entender:

- Por que cada mudança foi feita
- Quando cada hardware foi instalado
- Evolução dos parâmetros de impressão

---

## 🔧 Como Usar

### Restaurar configurações Klipper

```bash
# Copiar configs pro Klipper
cp configs/klipper/* ~/printer_data/config/
sudo systemctl restart klipper
```

### Importar perfis OrcaSlicer

Os arquivos em `configs/orcaslicer/stealthburner/` podem ser importados diretamente no OrcaSlicer.

---

**Última atualização:** 16/04/2026  
**Fase do projeto:** PROMETHEUS - Fase 0
