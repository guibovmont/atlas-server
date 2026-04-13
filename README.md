# ATLAS Server

Servidor de produção para impressão 3D e automação residencial.

## 📦 Hardware

- **Máquina:** Dell OptiPlex 3070 Micro
- **CPU:** Intel i5-9500T
- **RAM:** 16GB DDR4
- **Storage:** M.2 240GB (sistema) + HDD 500GB (dados)
- **OS:** Ubuntu 24.04 LTS
- **IP Estático:** 192.168.1.110

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
