# Gemini Health Insight - Assistente de Saúde Preventiva Personalizado com Foco em Medicina de Precisão

## Descrição

O Gemini Health Insight é um assistente de saúde preventiva que utiliza inteligência artificial para analisar dados de pacientes e identificar fatores de risco para doenças cardiovasculares. O sistema considera uma variedade de dados, incluindo informações genômicas, histórico médico, estilo de vida e fatores ambientais, para fornecer uma análise de risco personalizada.

## Funcionalidades

* **Análise de Risco Cardiovascular:** Avalia o risco de doenças cardiovasculares com base em dados abrangentes do paciente.
* **Geração de Pacientes Simulados:** (Opcional) Permite a criação de dados de pacientes simulados para testes e desenvolvimento.
* **Recomendações Personalizadas:** (Em desenvolvimento) Fornece recomendações de saúde personalizadas com base na análise de risco.

## Instalação

1.  **Instalar a biblioteca do Google Gemini:**

    ```bash
    !pip install -q google-generativeai
    ```

2.  **Importar as bibliotecas necessárias:**

    ```python
    import google.generativeai as genai
    import json  # Para formatar os dados para o prompt
    import random  # Para a função de gerar múltiplos pacientes (futuro)
    ```

3.  **Configurar a API Key do Gemini:**

    ```python
    import os
    from google.colab import userdata
    
    try:
        GOOGLE_API_KEY = userdata.get('GOOGLE_API_KEY')
        genai.configure(api_key=GOOGLE_API_KEY)
        print("API Key configurada com sucesso!")
    except userdata.SecretNotFoundError:
        print("Erro: Secret 'GEMINI_API_KEY' não encontrado. Por favor, configure-o no Colab.")
    except Exception as e:
        print(f"Ocorreu um erro ao configurar a API Key: {e}")
    ```

    * **Explicação:** Este código tenta obter a API Key do Gemini do Colab (se estiver rodando no Colab). Se não encontrar, imprime um erro.

4.  **Carregar o modelo Gemini:**

    ```python
    model = genai.GenerativeModel('gemini-2.0-flash')
    print("Modelo Gemini carregado com sucesso!")
    ```

    * **Explicação:** Carrega o modelo Gemini para uso nas análises.

## Estrutura de Dados do Paciente

O sistema utiliza um dicionário Python para representar os dados de um paciente. A estrutura é a seguinte:

```python
paciente_exemplo_1 = {
    "id_paciente": "P001",
    "dados_pessoais": {
        "idade": 52,
        "sexo_biologico": "Masculino"
    },
    "dados_genomicos": {
        "Variante_Genetica_Colesterol": "Presente",
        "Polimorfismo_APOE": "e3/e4"
    },
    "historico_medico": {
        "Historico_Familiar_DCV": "Sim",
        "Parentesco_DCV": "Pai",
        "Idade_Diagnostico_Familiar_DCV": 55,
        "Diagnostico_Hipertensao": "Sim",
        "Pressao_Arterial_Sistolica_mmHg": 145,
        "Pressao_Arterial_Diastolica_mmHg": 92,
        "Diagnostico_Diabetes": "Não",
        "Nivel_Colesterol_Ultimo_Exame": {
            "LDL_Colesterol_mg_dL": 150,
            "HDL_Colesterol_mg_dL": 45,
            "Triglicerideos_mg_dL": 180
        },
        "Medicacoes_Uso": ["Lisinopril 10mg"]
    },
    "estilo_de_vida": {
        "Alimentacao_Tipo": "Ocidental_Processada",
        "Frequencia_Alimentacao_Processada": "Alta",
        "Atividade_Fisica_Semanal_Minutos": 60,
        "Atividade_Fisica_Tipo": "Caminhada Leve",
        "Tabagismo_Status": "Ex-Fumante",
        "Anos_Desde_Cessacao_Tabagismo": 5,
        "Consumo_Alcool_Frequencia": "Semanal",
        "Consumo_Alcool_Doses_Por_Vez": 2,
        "Nivel_Estresse_Percebido": "Alto",
        "Horas_Sono_Media_Noite": 6.0
    },
    "dados_ambientais": {
        "Cidade_Residencia": "Rio de Janeiro",
        "Bairro_Residencia_Simulado": "Centro",
        "Qualidade_Ar_Regiao_RJ": "Moderada",
        "Exposicao_Poluicao_Sonora": "Alta"
    }
}
````

  * **Explicação:** Esta estrutura organiza os dados do paciente em categorias como dados pessoais, genômicos, histórico médico, estilo de vida e dados ambientais. Isso facilita a análise e a geração de prompts.

## Geração de Pacientes Simulados (Opcional)

A função `gerar_paciente_simulado` cria dados de pacientes aleatórios para testes.

```python
def gerar_paciente_simulado(paciente_id):
    # ... (código da função)
    return paciente

# Exemplo de uso:
numero_de_pacientes_simulados = 5
lista_pacientes_simulados = [gerar_paciente_simulado(i+1) for i in range(numero_de_pacientes_simulados)]
```

  * **Explicação:** Esta função gera dados aleatórios para cada campo do paciente, permitindo testar o sistema com diferentes cenários.

## Análise de Risco Cardiovascular

O núcleo do sistema é a análise de risco cardiovascular. Ele utiliza o modelo Gemini para interpretar os dados do paciente e identificar fatores de risco.

1.  **Preparar o Prompt:**

    ```python
    paciente_para_analise = paciente_exemplo_1  # Ou: lista_pacientes_simulados[0]
    dados_paciente_json_string = json.dumps(paciente_para_analise, indent=2, ensure_ascii=False)

    prompt_analise_risco = f"""
    Você é um assistente de análise de saúde preventiva.
    Analise os seguintes dados de um indivíduo e identifique os principais fatores de risco cardiovascular.
    Considere a combinação de fatores genômicos, histórico médico, estilo de vida e dados ambientais.
    Forneça um resumo conciso dos fatores de risco identificados.
    Não forneça aconselhamento médico, apenas identifique os fatores de risco com base nos dados.

    Dados do Indivíduo:
    {dados_paciente_json_string}

    Análise de Risco Cardiovascular:
    """
    ```

      * **Explicação:** O prompt instrui o modelo Gemini a analisar os dados do paciente e identificar fatores de risco cardiovascular. Os dados do paciente são formatados como uma string JSON e inseridos no prompt.

2.  **Enviar o Prompt para o Gemini e Obter a Resposta:**

    ```python
    print(f"\nEnviando dados do paciente {paciente_para_analise['id_paciente']} para análise de risco cardiovascular...")
    try:
        response_analise = model.generate_content(prompt_analise_risco)
        print(response_analise.text)
    except Exception as e:
        print(f"Ocorreu um erro ao gerar conteúdo: {e}")
    ```

      * **Explicação:** Este código envia o prompt para o modelo Gemini e imprime a resposta, que contém a análise de risco cardiovascular.

## Passos e Melhorias Futuras

  * **Expandir os tipos de dados:** Incluir mais marcadores genéticos, histórico de vacinação, dados de wearables (sono, frequência cardíaca), resultados de exames mais detalhados.
  * **Cobrir mais condições de saúde:** Além do risco cardiovascular, poderia abordar diabetes, saúde mental, risco de câncer com base em histórico familiar, etc.
  * **Refinamento de Prompts:** Testar diferentes formas de perguntar ao Gemini para obter respostas mais precisas, estruturadas ou empáticas.
  * **Cadeia de Prompts (Chain-of-Thought):** Para análises mais complexas, você pode fazer várias perguntas sequenciais ao Gemini. A saída de um prompt alimenta o próximo.
  * **Validação de Dados de Entrada:** Adicionar checagens para garantir que os dados inseridos (especialmente na interface) são válidos.
  * **Persistência de Dados:** Salvar e carregar dados de pacientes (ex: em arquivos CSV, JSON, ou um banco de dados simples se o projeto crescer).
  * **Feedback do Usuário:** Em um sistema real, coletar feedback sobre a utilidade das recomendações para refinar o sistema.
  * **Internacionalização e Localização:** Adaptar para diferentes idiomas e contextos regionais de saúde.

## Contribuição

Contribuições são bem-vindas\! Se você tiver ideias para melhorar o projeto, sinta-se à vontade para abrir uma issue ou enviar um pull request.

## Licença

[Licença MIT](https://opensource.org/licenses/MIT).
