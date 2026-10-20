# Projeto Filtro Digital (DSP)

[![Licença](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![Python](https://img.shields.io/badge/python-3.8+-brightgreen.svg)](https://www.python.org/)

Este repositório contém um projeto prático de **Processamento Digital de Sinais (PDS / DSP)** desenvolvido em Python. O código realiza a análise temporal e espectral de sinais de áudio, injeção de ruído em bandas de frequência específicas, aplicação de filtros digitais IIR (Butterworth) passa-baixas e passa-altas de fase zero, e técnicas de janelamento temporal (Hann e Bartlett).

---

## 📋 Sumário

- [Visão Geral](#-visão-geral)
- [Funcionalidades](#-funcionalidades)
- [Estrutura do Repositório](#-estrutura-do-repositório)
- [Pré-requisitos e Dependências](#-pré-requisitos-e-dependências)
- [Instalação e Execução](#-instalação-e-execução)
- [Pipeline de Processamento](#-pipeline-de-processamento)
  - [1. Análise do Sinal Original](#1-análise-do-sinal-original)
  - [2. Injeção de Ruído Espectral Seletivo](#2-injeção-de-ruído-espectral-seletivo)
  - [3. Filtros Digitais Butterworth](#3-filtros-digitais-butterworth)
  - [4. Janelamento Temporal (Hann e Bartlett)](#4-janelamento-temporal-hann-e-bartlett)
  - [5. Exportação dos Sinais de Áudio](#5-exportação-dos-sinais-de-áudio)
- [Parâmetros Configuráveis](#-parâmetros-configuráveis)
- [Licença](#-licença)

---

## 🎯 Visão Geral

O objetivo principal deste projeto é demonstrar computacionalmente os conceitos fundamentais de filtragem e análise no domínio da frequência:
- Representação temporal (waveform) e espectrogramas (STFT - Short-Time Fourier Transform).
- Manipulação da magnitude espectral e reconstrução de sinais via iSTFT.
- Projeto e aplicação de filtros digitais IIR (*Infinite Impulse Response*) do tipo Butterworth.
- Atenuação e filtragem de fase zero com `scipy.signal.filtfilt`.
- Efeitos de funções de janelamento (Hann e Bartlett) na magnitude e fase do sinal.

---

## ✨ Funcionalidades

- **Carregamento de Áudio Flexível:** Carrega áudios de demonstração (`librosa.ex('trumpet')`) ou arquivos personalizados (`.wav`).
- **Visualização Espectral e Temporal:** Geração de gráficos de formas de onda, espectrogramas em decibéis (dB) e gráficos de densidade espectral/DFT.
- **Injeção de Ruído Seletivo:** Inserção de ruído gaussiano restrito a uma faixa arbitrária de frequências ($5000\text{ Hz} - 7000\text{ Hz}$).
- **Filtros Digitais Butterworth:**
  - **Passa-Baixas (Low-Pass):** Atenuação de altas frequências e remoção do ruído inserido.
  - **Passa-Altas (High-Pass):** Isolamento de componentes de alta frequência.
  - Filtragem bidirecional de fase zero sem introduzir atraso de grupo.
- **Janelamento:** Aplicação e comparação das janelas de Hann e Bartlett, exibindo tempo, magnitude e fase da DFT.
- **Exportação de Áudio:** Gravação dos sinais filtrados em formato `.wav` para reprodução e comparação auditiva.

---

## 📂 Estrutura do Repositório

```text
ProjetoFiltroDigital/
├── Filtro_DSP.py        # Script principal com todo o fluxo de DSP
├── requirements.txt     # Dependências do projeto
├── LICENSE              # Licença Apache 2.0
└── README.md            # Documentação do projeto
```

---

## 🛠️ Pré-requisitos e Dependências

O projeto requer **Python 3.8+** e as seguintes bibliotecas principais:

- [`numpy`](https://numpy.org/) — Operações matriciais e manipulação numérica.
- [`scipy`](https://scipy.org/) — Projeto de filtros digitais (`butter`, `filtfilt`) e I/O de áudio (`wavfile`).
- [`librosa`](https://librosa.org/) — Processamento, extração de STFT, iSTFT e visualizações de áudio.
- [`matplotlib`](https://matplotlib.org/) — Renderização de gráficos e espectrogramas.
- [`soundfile`](https://python-soundfile.readthedocs.io/) — Backend para leitura/escrita de áudio.
- [`ipython`](https://ipython.org/) — Utilitários de reprodução interativa de áudio (`IPython.display.Audio`).

---

## 🚀 Instalação e Execução

### 1. Clonar o repositório

```bash
git clone https://github.com/lucasA51/ProjetoFiltroDigital.git
cd ProjetoFiltroDigital
```

### 2. Criar e ativar um ambiente virtual (recomendado)

```bash
# Linux / macOS:
python3 -m venv venv
source venv/bin/activate

# Windows:
python -m venv venv
venv\Scripts\activate
```

### 3. Instalar as dependências

```bash
pip install -r requirements.txt
```

### 4. Executar o script

```bash
python Filtro_DSP.py
```

> **Dica:** O script exibe gráficos interativos via `matplotlib.pyplot.show()` durante a execução. Feche cada janela de figura para que o fluxo avance até a próxima etapa.
> Caso prefira uma execução célula a célula interativa, o script também pode ser executado em ambientes como **Jupyter Notebook** ou **Google Colab**.

---

## 🔬 Pipeline de Processamento

### 1. Análise do Sinal Original
- O sinal é carregado com sua taxa de amostragem (`sr`).
- É plotada a forma de onda temporal juntamente com o espectrograma linear em escala de decibéis.
- É calculada a Transformada Discreta de Fourier (DFT) média do sinal para identificar as componentes de frequência naturais.

### 2. Injeção de Ruído Espectral Seletivo
- O sinal é decomposto em magnitude e fase via STFT:
  $$X[k] = |X[k]| e^{j \theta[k]}$$
- É adicionado ruído gaussiano apenas aos *bins* de frequência correspondentes à faixa entre $5000\text{ Hz}$ e $7000\text{ Hz}$.
- O sinal contaminado é reconstruído no domínio do tempo via iSTFT (*Inverse Short-Time Fourier Transform*).

### 3. Filtros Digitais Butterworth
- **Normalização de Frequência:** As frequências de corte são normalizadas em relação à frequência de Nyquist ($f_N = f_s / 2$).
- **Filtro Passa-Baixas:** Ordem 6, frequência de corte $f_c = 5000\text{ Hz}$ (`btype='low'`).
- **Filtro Passa-Altas:** Ordem 6, frequência de corte $f_c = 5000\text{ Hz}$ (`btype='high'`).
- **Filtro de Fase Zero (`filtfilt`):** Aplica a filtragem nos sentidos direto e reverso para eliminar distorções de fase.

### 4. Janelamento Temporal (Hann e Bartlett)
- Aplicação de janelas de corte temporal em um segmento do sinal:
  - **Janela de Hann:** atenua suavemente as bordas, reduzindo o vazamento espectral (*spectral leakage*).
  - **Janela de Bartlett:** janela triangular para análise comparativa de transição e espectro de fase.
- Visualização dos sinais janelados, magnitude e fase da DFT.

### 5. Exportação dos Sinais de Áudio
Ao término do processamento, dois arquivos de áudio são gerados no diretório de execução:
- `sinal_filtrado1.wav`: Áudio resultante do filtro passa-baixas.
- `sinal_filtrado2.wav`: Áudio resultante do filtro passa-altas.

---

## ⚙️ Parâmetros Configuráveis

Você pode personalizar o comportamento abrindo [`Filtro_DSP.py`](Filtro_DSP.py) e alterando as variáveis no início de cada seção:

| Parâmetro | Linha / Seção | Descrição | Valor Padrão |
| :--- | :--- | :--- | :--- |
| `caminho_sinal` | Carregamento | Caminho do áudio de entrada (ex: `librosa.ex('trumpet')` ou arquivo local `'audio.wav'`) | `'trumpet'` |
| `freq_inferior` / `freq_superior` | Injeção de Ruído | Faixa de frequência (Hz) onde o ruído será inserido | `5000` a `7000` Hz |
| `nivel_de_ruido` | Injeção de Ruído | Fator multiplicador da amplitude do ruído gaussiano | `0.8` |
| `freq_corte` | Filtros | Frequência de corte do filtro Butterworth (Hz) | `5000` Hz |
| `ordem` | Filtros | Ordem matemática do filtro Butterworth | `6` |
| `tamanho_janela` | Janelas | Tamanho do trecho a ser janelado | $10\%$ do sinal |

---

## 📄 Licença

Este projeto é distribuído sob a licença **Apache 2.0**. Para mais detalhes, consulte o arquivo [LICENSE](LICENSE).

---

## 👤 Autor

Desenvolvido por **Lucas Andrade** ([@lucasA51](https://github.com/lucasA51)).
