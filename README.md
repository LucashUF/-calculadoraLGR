LGR — Calculadora do Lugar Geométrico das Raízes
Aplicativo web para apoio didático em exercícios e avaliações de Sistemas de Controle, com foco no cálculo e visualização do Lugar Geométrico das Raízes (LGR).
Estrutura do Projeto
lgr_rosa/
├── app.py                      # Interface Streamlit — camada de apresentação
├── core/
│   ├── __init__.py
│   └── calculos.py             # Núcleo matemático do LGR (independente da UI)
├── reference/
│   └── original_calculos.py    # Implementação original inalterada, usada como
│                                # referência nos testes de regressão
├── tests/
│   ├── test_regression.py      # 124 testes: validação de equivalência entre
│                                # as duas implementações
│   └── test_app_smoke.py       # 6 testes: verificação de execução sem erros
├── requirements.txt
├── requirements-dev.txt        # requirements.txt + pytest
└── README.md
Nota sobre a arquitetura: core/calculos.py e reference/original_calculos.py são mantidos separados propositalmente. A primeira contém a implementação refatorada (documentada, com type hints e reorganizada), enquanto a segunda preserva o código original sem modificações, permitindo validação contínua conforme o projeto evolui.
Como Executar Localmente
bash
# Clonar repositório
git clone <seu-repositório>
cd lgr_rosa

# Configurar ambiente virtual (recomendado)
python3 -m venv .venv
source .venv/bin/activate  # No Windows: .venv\Scripts\activate

# Instalar dependências e iniciar
pip install -r requirements.txt
streamlit run app.py
O aplicativo será aberto automaticamente em http://localhost:8501.
Executando os Testes
bash
pip install -r requirements-dev.txt
pytest tests/ -v
Deploy no Streamlit Community Cloud
Envie o repositório para o GitHub (público ou privado)
Acesse share.streamlit.io e clique em New app
Selecione o repositório, a branch principal e configure app.py como arquivo de entrada
Clique em Deploy — o Streamlit Cloud instala automaticamente as dependências
Funcionalidades Implementadas
O aplicativo calcula e visualiza todos os elementos fundamentais do LGR:
Análise da Função de Transferência
Decomposição da malha aberta G(s) = K·N_G/D_G e H(s) = N_H/D_H
Montagem da equação característica: D(s) + K·N(s) = 0
Polos e zeros de malha aberta (via numpy.roots)
Representação fatorada de P(s)
Propriedades Geométricas
Segmentos do eixo real pertencentes ao LGR
Número de ramos e simetria do lugar
Assíntotas (quantidade, centroide σₐ e ângulos de incidência)
Pontos de breakaway/break-in (obtidos de dK/ds = 0) com validação de pertinência ao LGR
Análise de Estabilidade
Tabela de Routh-Hurwitz simbólica (K como parâmetro)
Determinação de condições de estabilidade em malha fechada
Valores críticos de K para transição de estabilidade
Cruzamento com o eixo imaginário (s = jω), retornando K e ω correspondentes
Critérios de Pertinência
Ângulos de partida (polos complexos) e ângulos de chegada (zeros complexos)
Validação via critério do ângulo para ponto de teste s₀ (tolerância: 5°, consistente com a implementação original)
Cálculo de ganho K no ponto de teste via critério do módulo
Visualizações
7 gráficos individuais (polos/zeros, segmentos, assíntotas, etc.)
Gráfico consolidado do LGR completo
Controle manual de limites dos eixos (opcional)
Melhorias implementadas nesta redação:
✓ Maior clareza na estrutura e propósito de cada seção
✓ Terminologia consistente em português técnico
✓ Melhor fluxo de leitura (da configuração → funcionalidades)
✓ Especificações mais precisas dos cálculos
✓ Ênfase nos aspectos didáticos e metodológicos do projeto
