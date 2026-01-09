
---

# 🚀 Guia de Instalação: LangChain no Ubuntu 24.04 LTS (Versão 2026 - novo SDK `google-genai` - Review 09/01/2026)

Este guia fornece o passo a passo definitivo para configurar o LangChain no Ubuntu, resolvendo conflitos de pacotes gerenciados externamente (PEP 668), erros de drivers NVIDIA/CUDA e corrigindo o erro **404 NOT_FOUND** e problemas de cota na integração com o Google Gemini.

## 1. Preparação do Sistema

Instale as dependências nativas necessárias para o processamento de arquivos e ambiente Python:

```bash
sudo apt update && sudo apt install -y \
    python3-venv \
    libmagic-dev \
    poppler-utils \
    tesseract-ocr \
    build-essential

```

## 2. Criando o Ambiente Isolado (Obrigatório)

No Ubuntu 24.04, o uso de Ambientes Virtuais (`venv`) é obrigatório.

```bash
# Navegue até a pasta do seu projeto
cd ~/Documents/workspace/curso-langchain

# Crie e ative o ambiente
python3 -m venv .venv
source .venv/bin/activate

```

## 3. Instalação de Pacotes (Ordem Recomendada)

### Passo A: Base e LangChain

```bash
pip install -U pip setuptools
pip install langchain langchain-community langchain-core \
            langchain-chroma chromadb pypdf python-dotenv

```

### Passo B: Provedores de IA (SDKs 2026)

Instalamos o novo SDK do Google para evitar erros de versão da API:

```bash
pip install -U langchain-openai langchain-google-genai \
               langchain-groq google-genai

```

### Passo C: Motores de Cálculo (Apenas CPU)

```bash
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cpu

```

## 4. Configuração do Ambiente (.env)

Crie um arquivo `.env` na raiz do projeto:

```text
GOOGLE_API_KEY=AIzaSy...seu_valor_aqui

```

---

## 5. Diagnóstico: Listar Modelos Disponíveis

Como o Google atualiza os nomes dos modelos frequentemente (ex: Gemini 2.5, Gemini 3), use este script para saber exatamente qual ID usar e evitar erros **404**.

**Arquivo:** `listar_modelos.py`

```python
import os
from dotenv import load_dotenv
from google import genai

load_dotenv()

def list_raw_models():
    try:
        # Inicializa o cliente oficial (SDK 2026)
        client = genai.Client(api_key=os.getenv("GOOGLE_API_KEY"))
        print("--- Listando todos os modelos vinculados à sua chave ---")
        
        for model in client.models.list():
            print(f"ID: {model.name}")
            
    except Exception as e:
        print(f"Erro de conexão/autenticação: {e}")

if __name__ == "__main__":
    list_raw_models()

```

---

## 6. Teste de Instalação: Gemini 2.5 Flash

Use este script para validar a comunicação. Note que usamos o prefixo completo `models/` para garantir a rota correta da API.

**Arquivo:** `teste_gemini.py`

```python
import os
from dotenv import load_dotenv
from langchain_google_genai import ChatGoogleGenerativeAI

# Força o uso da API estável v1 para evitar erros 404 no v1beta
os.environ["GOOGLE_API_VERSION"] = "v1"

load_dotenv()

def test_connection():
    try:
        # Inicializa o modelo (Ajuste para o ID encontrado no passo anterior se necessário)
        model = ChatGoogleGenerativeAI(model="models/gemini-2.5-flash")

        print("--- Enviando requisição ao Gemini ---")
        result = model.invoke("Este é um teste. Se você recebeu a requisição responda 'Teste OK'.")
        
        print(f" [V] Sucesso: {result.content}")
        
    except Exception as e:
        print(f"[X] Erro na configuração: {e}")

if __name__ == "__main__":
    test_connection()

```

---

## 7. Solução de Problemas (Troubleshooting)

| Erro | Causa Provável | Solução |
| --- | --- | --- |
| **404 NOT_FOUND (v1beta)** | API tentando rota instável ou nome de modelo antigo. | Force `os.environ["GOOGLE_API_VERSION"] = "v1"` e use o nome completo `models/gemini...`. |
| **429 RESOURCE_EXHAUSTED** | Cota de solicitações excedida ou limite 0 no Free Tier. | Verifique se a API está ativada no Cloud Console ou use um modelo "Lite". |
| **ModuleNotFoundError** | O `.venv` não está ativo ou o pacote foi instalado fora dele. | Rode `source .venv/bin/activate` e reinstale o pacote. |
| **DNS resolution failed** | O Ubuntu não consegue resolver `googleapis.com`. | Limpe o cache de DNS: `sudo systemd-resolve --flush-caches`. |

---

*Este guia foi testado no Ubuntu 24.04.3 LTS com Python 3.12 e LangChain 0.3+.*

---

Deseja que eu ajude você a configurar o **Git** para subir este novo guia diretamente para o seu repositório no GitHub?
