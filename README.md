
```markdown
# 🚀 Guia de Instalação: LangChain no Ubuntu 24.04 LTS

Este guia fornece o passo a passo definitivo para configurar o LangChain no Ubuntu, resolvendo conflitos de pacotes gerenciados externamente (PEP 668), erros de drivers NVIDIA/CUDA e, principalmente, corrigindo o erro **404 NOT_FOUND** na integração com o Google Gemini.

## 1. Preparação do Sistema
Instale as dependências nativas necessárias para o processamento de arquivos (PDFs, imagens e OCR):

```bash
sudo apt update && sudo apt install -y \
    python3-venv \
    libmagic-dev \
    poppler-utils \
    tesseract-ocr \
    build-essential

```

## 2. Criando o Ambiente Isolado (Obrigatório)

No Ubuntu 24.04, o uso de Ambientes Virtuais (`venv`) é obrigatório para evitar a quebra do Python do sistema.

```bash
# Navegue até a pasta do seu projeto
cd ~/Documents/workspace/curso-langchain

# Remova o ambiente antigo se houver erros persistentes
rm -rf .venv 

# Crie e ative o novo ambiente
python3 -m venv .venv
source .venv/bin/activate

```

## 3. Instalação de Pacotes (Ordem Recomendada)

Instale os pacotes em blocos para garantir que as dependências sejam resolvidas sem conflitos de versão.

### Passo A: Base e LangChain Core

```bash
pip install -U pip setuptools
pip install langchain langchain-community langchain-core \
            langchain-chroma chromadb pypdf python-dotenv

```

### Passo B: Provedores de IA (Correção para Gemini)

Para evitar o erro de modelo não encontrado (404), instalamos a versão estável do SDK do Google:

```bash
pip install -U langchain-openai langchain-google-genai \
               langchain-groq google-generativeai

```

*Nota: Evite instalar o pacote `google-genai` (experimental) junto com o `google-generativeai`, pois isso causa conflitos de versão da API.*

### Passo C: Motores de Cálculo (Versão CPU)

Garante que o código funcione sem procurar placas de vídeo NVIDIA:

```bash
pip install torch torchvision torchaudio --index-url [https://download.pytorch.org/whl/cpu](https://download.pytorch.org/whl/cpu)

```

## 4. Configuração do Ambiente (.env)

Crie um arquivo `.env` na raiz do projeto e adicione suas chaves:

```text
GOOGLE_API_KEY=sua_chave_aqui
OPENAI_API_KEY=sua_chave_aqui

```

## 5. Script de Teste: Gemini 1.5 Flash

Crie um arquivo chamado `teste_gemini.py` para validar a instalação:

```python
import os
from dotenv import load_dotenv
from langchain_google_genai import ChatGoogleGenerativeAI

load_dotenv()

def test_connection():
    try:
        # Inicializa o modelo (Use apenas o nome, a lib resolve o prefixo)
        llm = ChatGoogleGenerativeAI(
            model="gemini-1.5-flash",
            temperature=0
        )
        
        print("--- Enviando requisição ao Gemini ---")
        response = llm.invoke("Responda apenas: Sistema operacional e Gemini configurados!")
        print(f"✅ Sucesso: {response.content}")
        
    except Exception as e:
        print(f"❌ Erro na configuração: {e}")

if __name__ == "__main__":
    test_connection()

```

## 6. Solução de Problemas (Troubleshooting)

| Erro | Causa Provável | Solução |
| --- | --- | --- |
| **404 NOT_FOUND (v1beta)** | Conflito de bibliotecas ou versão da API. | Rode `pip uninstall google-genai` e mantenha apenas `google-generativeai`. |
| **externally-managed-environment** | Tentativa de usar `pip` global. | Certifique-se de que o `.venv` está ativo (`source .venv/bin/activate`). |
| **No matching distribution for pywin32** | Dependência exclusiva de Windows. | **Ignore.** O projeto funcionará normalmente no Ubuntu. |
| **Nvidia/CUDA warnings** | PyTorch procurando GPU. | **Ignore.** O sistema usará a CPU automaticamente conforme configurado no Passo 3C. |

---

*Este guia foi testado no Ubuntu 24.04.3 LTS com Python 3.12.*

``` repositório GitHub via linha de comando?

```
