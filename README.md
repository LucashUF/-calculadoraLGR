# LGR — Calculadora do Lugar Geométrico das Raízes

[![Streamlit App](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://share.streamlit.io)

**Aplicativo web interativo para apoio didático em exercícios e avaliações de Sistemas de Controle**, com foco na análise completa do Lugar Geométrico das Raízes (LGR).

Este projeto implementa a construção gráfica e analítica do LGR, ferramenta fundamental no projeto de sistemas de controle em malha fechada, combinando rigor matemático com interface intuitiva.

---

## 🎯 Características Principais

- ✅ **Cálculo automático de polos e zeros** de malha aberta
- ✅ **Análise do eixo real** com identificação de segmentos válidos
- ✅ **Determinação de assíntotas** (centroide e ângulos)
- ✅ **Detecção de pontos de breakaway/break-in** via dK/ds = 0
- ✅ **Tabela de Routh-Hurwitz simbólica** com parâmetro K
- ✅ **Cruzamento com eixo imaginário** (margens de estabilidade)
- ✅ **Ângulos de partida e chegada** para polos/zeros complexos
- ✅ **Critério do ângulo e do módulo** para validação de pontos
- ✅ **7 visualizações individuais + gráfico consolidado** do LGR
- ✅ **Testes de regressão** (124 testes) garantindo precisão contra implementação original
- ✅ **Deploy automático** no Streamlit Community Cloud

---

## 📋 Pré-requisitos

- **Python 3.8+**
- **pip** (gerenciador de pacotes)
- Git (para clonar o repositório)

---


## 📦 Estrutura do Projeto

```
lgr_rosa/
├── app.py                          # Interface Streamlit (camada de apresentação)
│
├── core/
│   ├── __init__.py
│   └── calculos.py                 # Núcleo matemático — toda a lógica do LGR
│                                    # (independente de Streamlit, 100% testável)
│
├── reference/
│   └── original_calculos.py        # Implementação original (inalterada)
│                                    # Usada apenas como referência nos testes
│
├── tests/
│   ├── test_regression.py          # Validação de equivalência entre as duas
│   │                                # implementações (124 casos)
│   └── test_app_smoke.py           # Testes de execução sem erros (6 casos)
│
├── requirements.txt                # Dependências de produção
├── requirements-dev.txt            # Dependências de desenvolvimento
├── .gitignore
└── README.md                       # Este arquivo

```

### Filosofia de Arquitetura

A separação entre `core/calculos.py` e `reference/original_calculos.py` é deliberada:

- **`core/calculos.py`**: Implementação refatorada com documentação clara, type hints e organização melhorada. É o código que evolui e melhora.
- **`reference/original_calculos.py`**: Código original preservado intacto. Serve como "gabarito" para validação contínua durante a evolução do projeto.

Essa abordagem garante que qualquer melhoria ou refatoração **não comprometa a precisão dos cálculos**.

---

## 🌐 Deploy no Streamlit Community Cloud

### Pré-requisitos
- Repositório no GitHub (público ou privado)
- Conta no Streamlit Community Cloud

### Passos

1. **Envie o código para o GitHub**
   ```bash
   git push origin main
   ```

2. **Acesse [share.streamlit.io](https://share.streamlit.io/)**

3. **Clique em "New app"** e preencha:
   - Repository: `seu-usuario/lgr_rosa`
   - Branch: `main` (ou a branch que desejar)
   - Main file path: `app.py`

4. **Clique em "Deploy"**
   - O Streamlit Cloud instalará automaticamente as dependências de `requirements.txt`
   - Em poucos segundos, o app estará online

---

## 📐 Funcionalidades Detalhadas

### Análise da Função de Transferência

O aplicativo recebe as funções de transferência de malha aberta:
- **G(s)**: Função de transferência da planta/processo
- **H(s)**: Função de transferência do sensor/retroalimentação

A partir dessas, monta a **equação característica**:
$$D(s) + K \cdot N(s) = 0$$

onde os coeficientes são extraídos de:
$$G(s) = K \cdot \frac{N_G(s)}{D_G(s)}, \quad H(s) = \frac{N_H(s)}{D_H(s)}$$

### Polos e Zeros de Malha Aberta

Calcula as raízes de D_G(s), D_H(s), N_G(s) e N_H(s) usando `numpy.roots`, exibindo:
- Coordenadas exatas (real, imaginária)
- Multiplicidade
- Representação fatorada

### Segmentos do Eixo Real

Identifica intervalos no eixo real onde o LGR existe, aplicando o critério: o número de polos + zeros à direita de cada ponto deve ser ímpar.

### Assíntotas

Para o LGR tendendo ao infinito:
- **Número de assíntotas**: |n - m| (n = polos, m = zeros)
- **Centroide**: σₐ = (Σ polos - Σ zeros) / (n - m)
- **Ângulos**: θₖ = (2k + 1)π / (n - m), k = 0, 1, ..., n - m - 1

### Pontos de Breakaway/Break-in

Encontrados resolvendo dK/ds = 0 (onde K é função de s na equação característica). Cada ponto é validado:
- ✓ Se pertence ao LGR (verificação de ângulo)
- ✓ Se é ponto duplo (múltiplas raízes coincidem)

### Tabela de Routh-Hurwitz

Construída simbolicamente com K como parâmetro, determinando:
- Condições para **estabilidade assintótica**
- Valores críticos de K onde o sistema transita de estável para instável
- Número de raízes no semiplano direito

### Cruzamento com Eixo Imaginário

Substitui s = jω na equação característica:
$$D(jω) + K \cdot N(jω) = 0$$

Resolve para K e ω, indicando onde o LGR cruza o eixo imaginário (limite de estabilidade).

### Ângulos de Partida e Chegada

- **Partida** (de polos complexos): Usa critério do ângulo para determinar a direção inicial
- **Chegada** (em zeros complexos): Similar, mas para o ponto final

### Critério do Ângulo

Para um ponto de teste s₀, verifica se:
$$\sum(\text{ângulos de s₀ aos zeros}) - \sum(\text{ângulos de s₀ aos polos}) = (2k+1) \times 180°$$

com tolerância de **5°** (conforme implementação original).

### Critério do Módulo

Calcula o ganho K necessário para que s₀ seja um ponto válido no LGR:
$$K = \frac{\prod |s₀ - p_i|}{\prod |s₀ - z_j|}$$

---

## 💡 Como Usar a Interface

### Entrada de Dados

1. **Insira a função de transferência G(s)**
   - Exemplo: `(s+2) / (s*(s+1)*(s+3))`

2. **Insira a função H(s)** (se houver retroalimentação unitária, deixe como `1`)
   - Exemplo: `1` ou `(s+5)/(s+10)`

3. **Especifique os limites dos gráficos** (opcional)
   - Real: [-10, 5]
   - Imaginário: [-8, 8]

### Saídas Disponíveis

Explore as abas/seções:

- **Análise Simbólica**: Polos, zeros, equação característica
- **Eixo Real**: Segmentos válidos
- **Assíntotas**: Centroide e ângulos
- **Pontos Críticos**: Breakaway/break-in
- **Estabilidade**: Tabela de Routh-Hurwitz
- **Cruzamento**: Eixo imaginário
- **Ângulos**: Partida e chegada
- **Teste de Ponto**: Critério do ângulo e módulo
- **Gráficos**: Visualizações individuais e consolidadas

---

## 🔧 Estrutura do Código

### `core/calculos.py`

Módulo principal contendo funções puras:

```python
# Exemplo de função disponível
def calcular_polos_zeros(num_g, den_g, num_h, den_h):
    """Retorna polos e zeros de malha aberta."""
    ...

def segmentos_eixo_real(polos, zeros):
    """Identifica intervalos válidos no eixo real."""
    ...

def assintotas(num_polos, num_zeros, polos, zeros):
    """Calcula assíntotas (número, centroide, ângulos)."""
    ...

# ... e muitas outras
```

**Vantagem**: Totalmente testável e independente da UI.

### `app.py`

Interface Streamlit que:
- Recebe entrada do usuário
- Chama funções de `core/calculos.py`
- Renderiza resultados e gráficos

**Responsabilidade única**: Apresentação, não cálculo.

---

## 🧪 Exemplo de Teste

```python
# tests/test_regression.py

def test_polos_zeros_exemplo_simples():
    """Valida cálculo de polos e zeros contra implementação original."""
    # G(s) = 1 / (s*(s+1)*(s+2))
    resultado_novo = core.calculos.calcular_polos_zeros(
        [1], [1, 3, 2, 0]
    )
    resultado_original = reference.original_calculos.calcular_polos_zeros(
        [1], [1, 3, 2, 0]
    )
    assert np.allclose(resultado_novo, resultado_original)
```

---

## 📚 Referências e Teoria

O projeto implementa conceitos clássicos de Controle Automático:

- **Nichols, N. B., Marill, T., & Blakelock, J. H.** (1947). "Root-locus method"
- **Evans, W. R.** (1948). "Graphical analysis of control systems"
- **Franklin, G. F., Powell, J. D., & Emami-Naeini, A.** (2015). *Feedback Control of Dynamic Systems*, 7ª edição

---

## 🤝 Contribuindo

Contribuições são bem-vindas! Para colaborar:

1. Faça um fork do repositório
2. Crie uma branch para sua feature (`git checkout -b feature/sua-feature`)
3. Commit suas mudanças (`git commit -am 'Adiciona nova feature'`)
4. Push para a branch (`git push origin feature/sua-feature`)
5. Abra um Pull Request

**Importante**: Certifique-se de que todos os testes passam:
```bash
pytest tests/ -v
```

---

## 📄 Licença

Este projeto está licenciado sob a **MIT License** — veja o arquivo `LICENSE` para detalhes.

---

## 📧 Suporte

Encontrou um bug ou tem uma sugestão? Abra uma [issue](https://github.com/seu-usuario/lgr_rosa/issues) no GitHub.

---

## 🎓 Contexto Acadêmico

Este projeto foi desenvolvido como ferramenta de apoio para a disciplina **DCA-3705: Linguagens Formais** (ou similar em Sistemas de Controle) na **UFRN — Universidade Federal do Rio Grande do Norte**.

Destina-se a auxiliar estudantes e professores na compreensão visual e analítica do Lugar Geométrico das Raízes, acelerando o tempo de cálculo e permitindo exploração interativa de diferentes cenários de projeto.

---

**Última atualização**: Setembro de 2026

---
