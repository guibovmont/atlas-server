# 🏛️ Atlas Server

Repositório de documentação e configurações do servidor principal (OptiPlex 3070) para automação, Docker e Klipper.

## 🛠️ Especificações Técnicas

### Hardware
- **Máquina:** Dell OptiPlex 3070 Micro
- **CPU:** Intel i5-9500T (6 Cores / 6 Threads)
- **RAM:** 16GB DDR4
- **Storage Interno:** M.2 240GB (OS) + HDD 500GB (Data)
- **IP Estático:** `192.168.1.110`

### Software & Localização
- **OS:** Ubuntu 24.04.4 LTS
- **Hostname:** `Atlas`
- **Timezone:** `America/Sao_Paulo` (UTC-3)

## 🗂️ Estrutura do Repositório
atlas-server/
├── configs/
│   ├── klipper/          # Configurações da impressora 3D
│   │   ├── printer.cfg
│   │   ├── moonraker.conf
│   │   ├── KlipperScreen.conf
│   │   ├── crowsnest.conf
│   │   ├── timelapse.cfg
│   │   └── acelerometros.cfg
│   └── orcaslicer/       # Perfis de impressão
│       ├── hero-me-7/    # Hardware anterior (histórico)
│       │   ├── machine/
│       │   ├── process/
│       │   └── filament/
│       └── stealthburner/ # Hardware atual
│           ├── machine/
│           ├── process/
│           └── filament/

## 🖨️ Configurações OrcaSlicer

### Organização por Hardware

Os perfis estão separados por **hardware** (hotend), que é o componente menos mutável.

### Tipos de Perfil

- **machine/**: Configurações físicas da impressora (dimensões, limites)
- **process/**: Parâmetros de impressão (layer height, infill, velocidade)
- **filament/**: Configurações de material (temperatura, retração)

### Hero Me 7 (Histórico)

Hardware anterior. Mantido para **análise comparativa** futura.

- **Materiais suportados:** PLA, PETG, ABS, TPU 95A
- **Perfis:** Acabamento (0.12mm) e Function (0.24mm)

### StealthBurner (Atual)

Hardware em uso desde 10/04/2026.

- **Material principal:** PETG
- **Perfis:** Acabamento (0.12mm) e Function (0.24mm)

## 🤖 Projeto OpenClaw

Este repositório serve como base de configurações para o **OpenClaw**, um agente IA que controlará ativamente a impressão 3D.

O OpenClaw usará esses arquivos para:
- Auditar evolução de configurações ao longo do tempo
- Comparar desempenho entre diferentes hardwares
- Ajustar perfis de impressão baseado em resultados

## 📊 Histórico de Decisões

Consulte os **commits** do Git para entender:
- Por que cada mudança foi feita
- Quando cada hardware foi instalado
- Evolução dos parâmetros de impressão

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

**Última atualização:** Abril 2026  
**Fase do projeto:** PROMETHEUS - Fase 0
---

## 💾 Storage Externo (Orico)

### Hardware

- **Dock:** Orico Dual-Bay USB-C
- **HDs:** 2x WD Purple 2TB (surveillance-grade)
- **Interface:** USB 3.0/USB-C
- **Capacidade total:** ~3.6 TiB (4TB decimal)

### Configuração LVM

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

### Por que LVM?

- ✅ **Expansível:** Adicionar novos HDs sem reformatar
- ✅ **Snapshots:** Backup incremental
- ✅ **Flexível:** Redimensionar volumes dinamicamente

### Estratégia de Backup (3 camadas)

**Camada 1 - Dados ativos:**
- Orico 4TB → Nextcloud, Immich, projetos

**Camada 2 - Backup local:**
- HDD 500GB interno → Snapshots incrementais (7 dias)

**Camada 3 - Backup offsite:**
- Backblaze B2 → Backup seletivo de dados críticos

### Expansão Futura
Quando necessário (~3.5TB ocupados), adicionar **2 HDs extras** e migrar para RAID:

```bash
# Adicionar novos PVs ao VG existente
sudo pvcreate /dev/sde1 /dev/sdf1
sudo vgextend storage-vg /dev/sde1 /dev/sdf1
sudo lvextend -l +100%FREE /dev/storage-vg/storage-lv
sudo resize2fs /dev/storage-vg/storage-lv
```
