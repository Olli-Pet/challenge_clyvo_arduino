# 🐾 CLYVO VET — Ollie Pet (Sprint 3: Disruptive Architectures: IoT, IoB & Generative AI)

> **Integrantes / Autores:** Ruan Luca, Mirelly Sousa, Henrique Vespasiano, Gabriely Bonfim e André Rosa  
> **Instituição:** FIAP  
> **Curso:** Análise e Desenvolvimento de Sistemas  

---

## 📌 Visão Geral do Projeto

O **Ollie Pet** é uma solução de monitoramento de saúde e segurança animal desenvolvida para a **CLYVO VET**. Na Sprint 2, foi implementado um protótipo físico utilizando a plataforma **ESP32** com sensores de telemetria biológica e de localização.

Nesta **Sprint 3**, integramos um componente de **Inteligência Artificial Generativa (LLM)** via **Tool Calling / Function Calling**, permitindo que tutores e veterinários interajam em linguagem natural com o sistema para obter diagnósticos preventivos, alertas de emergência e histórico em tempo real do animal.

---

## 🎯 Definição do Problema e Proposta de Valor

### Problema de Negócio
Métricas brutas de sensores (ex.: *130 BPM, 45.0°C, 12m de distância*) enviadas por dispositivos de IoT (Internet of Things) e IoB (Internet of Behaviors) são difíceis de interpretar por tutores no dia a dia. Sem um sistema de triagem inteligente, quadros graves de febre ou fuga podem passar despercebidos ou demorar a receber intervenção.

### Valor para o Tutor
- Tradução de métricas biológicas complexas para alertas claros e empáticos em linguagem natural.
- Identificação imediata de emergências (hipertermia, alteração cardíaca ou fuga do perímetro de segurança).
- Recomendações de primeiros socorros e direcionamento direto para agendamento na CLYVO VET.

### Valor para a Clínica (CLYVO VET)
- Triagem automatizada de casos urgentes.
- Registro contínuo de dados biométricos (IoB) vinculados ao histórico clínico do paciente.

---

## 🧠 Abordagem de IA Adoptada e Justificativa Técnica

- **Tecnologia:** IA Generativa (LLM) utilizando o modelo **Gemini 3.6 Flash** (`google-genai` SDK) com suporte a **Function Calling / Tool Calling**.
- **Justificativa Técnica:** A abordagem baseada em LLM com *Function Calling* permite que o assistente consulte dinamicamente as APIs de telemetria do chip ESP32 (MQTT/REST) no momento da pergunta do usuário. Dessa forma, a IA cruza os dados em tempo real com faixas fisiológicas normais e parâmetros cadastrais do pet, gerando respostas personalizadas e alertas contextuais em vez de respostas estáticas baseadas em regras fixas.

---

## 📊 Mapeamento de Dados

| Dado / Métrica | Origem do Dado | Tipo | Descrição e Utilização pela IA |
| :--- | :--- | :--- | :--- |
| **Temperatura (°C)** | Sensor DHT22 / ESP32 | Float | Mede a temperatura corporal. Utilizado pela IA para detectar febre/hipertermia (faixa crítica: > 39.5°C). |
| **Frequência Cardíaca (BPM)** | Telemetria SIM / ESP32 | Integer | Avalia os batimentos por minuto. A IA verifica estados de taquicardia ou arritmia. |
| **Distância da Residência (m)** | Sensor HC-SR04 / ESP32 | Integer | Avalia a distância do pet em relação ao perímetro seguro da casa (limite seguro: até 50 metros). |
| **Status de Alerta de Hardware** | Circuito ESP32 | Boolean | Sinaliza acionamento dos atuadores físicos (LED de alerta e Buzzer/som de emergência). |
| **Perfil e Histórico do Pet** | Banco de Dados / Cadastro | JSON | Contém raça, porte, idade e histórico de saúde para definir faixas fisiológicas ideais. |

---

## 🏗️ Arquitetura de Comunicação e Fluxo de Dados

[ Chip Ollie Pet ]            [ Backend / DB ]          [ Camada de IA ]            [ Interface / Tutor ]
(ESP32 + MQTT)              (CLYVO VET API)           (Gemini + Python)                (Chat App)
│                            │                          │                            │
├─── Envia Telemetria ──────>│                          │                            │
│   (BPM, Temp, Distância)   │                          │                            │
│                            │                          │<─── Pergunta do Tutor ──────┤
│                            │                          │     ("Como está o Ollie?") │
│                            │<── Consulta Telemetria ──┤                            │
│                            │    (Function Calling)    │                            │
│                            ├─── Retorna Métricas ────>│                            │
│                            │    (Ex: 45°C, Alerta=True)│                            │
│                            │                          ├─── Resposta + Alerta ─────>│
│                            │                          │    ("⚠️ EMERGÊNCIA...")   │


---

## 📁 Estrutura do Repositório

```text
├── IA_ClyvoVet_OlliePet.ipynb   # Notebook executável da IA no Google Colab (Python + Gemini SDK)
├── README.md                    # Documentação técnica do projeto
🚀 Como Executar o Projeto no Google Colab
Acesse o arquivo IA_ClyvoVet_OlliePet.ipynb diretamente no repositório e clique em Open in Colab.

No Google Colab, abra a aba Secrets (ícone de chave no menu lateral) e cadastre sua chave de API do Google AI Studio com o nome API_KEY.

Certifique-se de ativar o acesso à chave para o notebook.

Execute as células em ordem:

Célula 1: Instalação das dependências (google-genai, pydantic).

Célula 2: Inicialização do cliente Gemini utilizando o userdata do Colab.

Célula 3: Definição da função de telemetria IoT (consultar_telemetria_ollie).

Célula 4: Configuração das instruções do sistema (System Instructions), vinculação de ferramentas e execução do chat de testes.

🎬 Apresentação e Demonstração Funcional
Vídeo Pitch (YouTube): https://youtu.be/PIrszpX-uFM

Repositório GitHub: https://github.com/Olli-Pet/challenge_clyvo_arduino
