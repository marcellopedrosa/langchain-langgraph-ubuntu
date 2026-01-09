
```markdown
# 🚀 Guia de Instalação: LangChain no Ubuntu 24.04.3 LTS

Este guia foi criado para resolver os problemas de pacotes gerenciados externamente (PEP 668), conflitos de versões de bibliotecas de IA e incompatibilidades de pacotes exclusivos de Windows em ambientes Linux.

## 1. Requisitos do Sistema
Antes de começar, instale as dependências nativas do Ubuntu necessárias para o processamento de documentos (PDF, imagens, etc):

```bash
sudo apt update
sudo apt install python3-venv libmagic-dev poppler-utils tesseract-ocr -y

```

## 2. Criando o Ambiente Isolado

Nunca utilize o `sudo pip`. No Ubuntu, sempre use um Ambiente Virtual (`venv`):

```bash
# Navegue até a pasta do projeto
cd ~/Documents/workspace/curso-langchain

# Crie e ative o ambiente
python3 -m venv .venv
source .venv/bin/activate

```

## 3. Sequência de Instalação (Sem Erros)

Instale por blocos para garantir que o gerenciador de pacotes (`pip`) não entre em conflito:

### Passo A: Base de Dados e Ferramentas de Build

```bash
pip install setuptools "pydantic>=2.9.2,<2.10.0" "langsmith>=0.1.125,<0.2.0" "pypdf>=4.0"

```

### Passo B: Core do LangChain e ChromaDB

```bash
pip install "langchain>=0.3.0,<0.4.0" \
            "langchain-community>=0.3.0,<0.4.0" \
            "langchain-core>=0.3.0,<0.4.0" \
            "langchain-chroma>=0.1.0,<0.3.0" \
            "chromadb>=0.5.13,<0.6.0" \
            "tokenizers>=0.19.0,<0.21.0" \
            "transformers>=4.40.0,<4.47.0"

```

### Passo C: Motores de Cálculo (Versão CPU)

Para evitar erros de drivers NVIDIA/CUDA no Ubuntu, instalamos a versão otimizada para processadores:

```bash
pip install scikit-learn torch --extra-index-url https://download.pytorch.org/whl/cpu

```

### Passo D: Provedores e Utilitários de Dados

```bash
pip install langchain-openai langchain-google-genai langchain-groq \
            langchain-huggingface langchain-ollama langchain-qdrant \
            openai groq google-ai-generativelanguage ollama \
            python-dotenv fastapi uvicorn httpx requests python-magic \
            PyMuPDF pandas numpy sqlalchemy "unstructured[all-docs]"

```

## 4. Troubleshooting: Como lidar com avisos

Durante a instalação, você poderá ver mensagens de erro ou avisos. Veja como interpretá-los:

| Erro Comum | Causa | Ação |
| --- | --- | --- |
| `nvidia-xxx-cu12 not installed` | O PyTorch está procurando uma placa de vídeo NVIDIA. | **Ignore.** O sistema usará a CPU para os cálculos. |
| `No matching distribution for pywin32` | O pacote é exclusivo para Windows. | **Ignore.** Ele não é necessário no Ubuntu. |
| `pip's dependency resolver conflict` | Versões de bibliotecas secundárias com pequenos desvios. | **Ignore.** Se o LangChain carregar, o projeto funcionará. |

## 5. Teste de Sanidade

Crie um arquivo `check_env.py` com o código abaixo:

```python
import langchain
import torch
print(f"LangChain: {langchain.__version__}")
print(f"Torch Device: {'CPU' if not torch.cuda.is_available() else 'GPU'}")
print("Ambiente pronto!")

```

Execute com: `python3 check_env.py`

---

*Nota: Este guia foi otimizado para Python 3.12 no Ubuntu 24.04.*

```

---
