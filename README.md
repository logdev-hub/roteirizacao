# 🚚 Simulador de Roteirização Logística

**Aplicação web educacional para comparar rotas de distribuição, calcular custos de transporte e explorar métodos de otimização combinatória.**

O simulador recebe uma **matriz de distâncias** entre um Centro de Distribuição (CD) e seus clientes, calcula três estratégias de visita e apresenta as respectivas distâncias, despesas e economias. Foi desenvolvido com **HTML, CSS e JavaScript**, com interface responsiva baseada no **Bootstrap 5** e visual inspirado na paleta institucional do Senac SP.

> **Objetivo didático:** demonstrar que a escolha do menor trecho a cada parada não garante o menor percurso total. A ferramenta é adequada a atividades de Logística, Transportes, Pesquisa Operacional e Gestão de Custos.

## 📋 Sumário

- [Demonstração e execução](#-demonstração-e-execução)
- [Funcionalidades](#-funcionalidades)
- [Métodos de roteirização](#-métodos-de-roteirização)
- [Exemplo: CD e oito clientes](#-exemplo-cd-e-oito-clientes)
- [Fórmulas utilizadas](#-fórmulas-utilizadas)
- [Como importar uma matriz](#-como-importar-uma-matriz)
- [Tecnologias](#-tecnologias)
- [Estrutura do projeto](#-estrutura-do-projeto)
- [Limitações e premissas](#-limitações-e-premissas)
- [Sugestões de aplicação em sala](#-sugestões-de-aplicação-em-sala)
- [Contribuições e licença](#-contribuições-e-licença)

## 🌐 Demonstração e execução

### Executar localmente

1. Baixe ou clone este repositório.
2. Abra o arquivo `simulador_rotas_logisticas.html` no **Chrome**, **Edge** ou outro navegador moderno.
3. A matriz inicial já estará carregada. Clique em **Calcular rotas**, ou modifique distâncias e custos para testar novos cenários.
4. Analise os três métodos, o comparativo financeiro, as decisões passo a passo e a memória de cálculo.

**Não é necessário instalar Node.js, Python, banco de dados ou servidor web.** O cálculo é feito no navegador. A folha de estilos do Bootstrap 5 é carregada por CDN e, portanto, requer conexão com a internet para sua aparência completa.

### Publicar no GitHub Pages

Para disponibilizar o simulador em uma URL pública:

1. Envie o arquivo HTML para a raiz do repositório.
2. Crie uma cópia chamada `index.html` (mantendo o arquivo original, se desejar).
3. Nas configurações do repositório, acesse **Settings → Pages**.
4. Em **Build and deployment**, selecione **Deploy from a branch**, escolha a branch principal e a pasta **/(root)** e salve.
5. Após a publicação, abra a URL indicada pelo GitHub Pages.

> Se preferir não criar `index.html`, o arquivo também pode ser acessado por seu caminho explícito na URL do Pages, após a publicação.

## ✨ Funcionalidades

- **Matriz de distâncias editável** com verificação de valores, rótulos e diagonal.
- **Tratamento de distâncias direcionais:** o percurso de A até B pode diferir do percurso de B até A.
- **Três estratégias de roteirização:** sequencial, vizinho mais próximo e solução global ótima.
- **Otimização exata** por programação dinâmica de **Held–Karp** para até **15 clientes**.
- **Custos configuráveis** por quilômetro e por viagem.
- **Comparação econômica** das rotas, com diferença absoluta e percentual em relação à rota sequencial.
- **Explicação passo a passo** das decisões do vizinho mais próximo.
- **Memória de cálculo** com todos os trechos e parcelas de custo.
- **Gráfico de barras** comparando distâncias e **diagrama esquemático** das sequências de visita.
- **Importação** de matrizes via texto ou arquivo CSV e **exportação** da matriz e dos resultados em CSV.
- **Impressão ou salvamento em PDF** por meio do navegador.
- **Avisos de inviabilidade** quando não é possível formar um circuito completo.

## 🧠 Métodos de roteirização

### A — Rota sequencial

Percorre os clientes na ordem numérica e retorna ao CD:

`CD → C1 → C2 → ... → Cn → CD`

É a referência utilizada para calcular a economia das demais alternativas.

### B — Vizinho mais próximo

A cada etapa, escolhe o **cliente ainda não visitado com a menor distância de saída** do ponto atual. Se houver empate, dá prioridade ao cliente de menor número. Ao final, retorna ao CD.

É uma **heurística gulosa**: suas decisões são locais e **não garantem** a menor rota global. Se os arcos disponíveis não permitirem completar o circuito, a estratégia é sinalizada como inviável.

### C — Rota ótima global (Held–Karp)

Utiliza **programação dinâmica** para encontrar o circuito Hamiltoniano de menor distância que parte do CD, visita cada cliente exatamente uma vez e retorna ao CD.

Para `n` clientes, há `n!` sequências possíveis de visita. Em vez de enumerar cada rota individualmente, o algoritmo armazena a melhor distância para cada combinação de **subconjunto visitado + último cliente**, reduzindo a repetição de cálculos. A complexidade assintótica típica é **O(n² · 2ⁿ)** em tempo e **O(n · 2ⁿ)** em memória.

O simulador executa o método exato com até **15 clientes**. Acima desse limite, **não apresenta uma solução aproximada como se fosse ótima**.

## 📊 Exemplo: CD e oito clientes

A matriz carregada inicialmente contém **1 CD + 8 clientes**, distâncias em quilômetros e custos padrão de **R$ 2,80/km + R$ 60,00 por viagem**.

| Método | Distância | Custo variável | Custo fixo | Custo total | Economia versus sequencial |
|---|---:|---:|---:|---:|---:|
| Sequencial | 104 km | R$ 291,20 | R$ 60,00 | **R$ 351,20** | — |
| Vizinho mais próximo | 110 km | R$ 308,00 | R$ 60,00 | **R$ 368,00** | −R$ 16,80 |
| **Ótima global** | **79 km** | **R$ 221,20** | R$ 60,00 | **R$ 281,20** | **R$ 70,00 (19,93%)** |

### Sequências calculadas

**Sequencial**

`CD → C1 → C2 → C3 → C4 → C5 → C6 → C7 → C8 → CD`

**Vizinho mais próximo**

`CD → C3 → C5 → C8 → C6 → C7 → C2 → C1 → C4 → CD`

**Ótima global**

`CD → C1 → C5 → C8 → C2 → C4 → C6 → C7 → C3 → CD`

A rota ótima percorre **25 km a menos** que a sequencial, o que representa **24,04% de redução na distância** e **19,93% de redução no custo total**, dadas as premissas. Comparada ao vizinho mais próximo, economiza **31 km e R$ 86,80**.

> **Atenção aos dados de entrada:** a matriz originalmente fornecida continha `C7 → C7 = 36` e `C8 → C8 = 1`, apesar de distâncias de um ponto para ele mesmo deverem ser zero. A aplicação inicia com esses dois elementos da diagonal normalizados para `0`, preserva os demais valores e oferece o botão **Restaurar matriz original** para examinar as inconsistências. A matriz é **assimétrica**, e o sentido dos arcos é respeitado.

## 🧮 Fórmulas utilizadas

Para uma sequência de visitas `CD → p1 → p2 → ... → pn → CD`, seja `D[i][j]` a distância do ponto `i` até o ponto `j`:

**Distância total**

\[
L = D[CD][p_1] + \sum_{k=1}^{n-1} D[p_k][p_{k+1}] + D[p_n][CD]
\]

**Custo variável:** `CV = L × custo_por_km`

**Custo total:** `CT = CV + custo_fixo`

**Economia absoluta:** `E = CT_sequencial − CT_alternativa`

**Economia percentual:** `E% = (E ÷ CT_sequencial) × 100`, quando `CT_sequencial > 0`.

O custo fixo é contabilizado **uma vez por viagem**. Com o mesmo custo por quilômetro e custo fixo entre alternativas, minimizar a distância também minimiza o custo total.

## 📥 Como importar uma matriz

Acesse **Importar ou colar matriz CSV / tabela** e cole os dados, ou selecione um arquivo `.csv`/`.txt`.

Formato de exemplo (separador `;`):

```csv
Origem;CD;C1;C2;C3
CD;0;12;18;9
C1;12;0;8;14
C2;18;8;0;16
C3;9;14;16;0
```

**Regras:**

- A matriz deve ser **quadrada**, com cabeçalhos e linhas na ordem `CD, C1, C2, ...`.
- A diagonal deve conter **zeros**.
- Distâncias devem ser **não negativas**; use `∞` para indicar um deslocamento indisponível.
- A matriz **não precisa ser simétrica**.
- O importador aceita delimitadores `;`, vírgula ou tabulação. Recomenda-se `;` para evitar conflito com decimais.
- Para valores decimais importados, prefira **ponto** como separador decimal (`12.5`).

Os resultados são exportados em CSV com **separador `;` e codificação UTF-8 com BOM**, facilitando a abertura no Excel configurado para pt-BR.

## 🛠️ Tecnologias

| Tecnologia | Uso |
|---|---|
| HTML5 | Estrutura semântica da aplicação |
| CSS3 | Estilização e regras de impressão |
| JavaScript (vanilla) | Validação, algoritmos, cálculos, gráficos e exportação |
| Bootstrap 5.3.3 (CDN) | Layout responsivo e componentes visuais |
| SVG | Gráfico e diagrama esquemático |

Não há dependência de backend, APIs externas para roteirização nem serviços pagos.

## 📁 Estrutura do projeto

```text
simulador-roteirizacao-logistica/
├── README.md
├── simulador_rotas_logisticas.html
└── index.html                     # opcional: cópia para GitHub Pages
```

## ⚠️ Limitações e premissas

- Considera **um veículo, uma viagem**, saída e retorno ao CD e visita a cada cliente exatamente uma vez.
- **Não** considera automaticamente capacidade do veículo, demanda, janelas de entrega, trânsito, pedágios, tempo de serviço, restrições de circulação ou múltiplos veículos.
- O circuito desenhado é uma **representação esquemática**, **não um mapa geográfico real**; uma matriz de distâncias não fornece coordenadas ou ruas.
- O método exato possui **limite de 15 clientes** no navegador; o importador pode aceitar matrizes maiores, mas não executará otimização exata acima desse limite.
- Em instâncias com arcos marcados como indisponíveis, uma heurística pode falhar mesmo quando existe outra rota viável. O método exato avalia a viabilidade global dentro do limite suportado.
- Os resultados dependem integralmente da qualidade da matriz e das hipóteses financeiras informadas.

## 🎓 Sugestões de aplicação em sala

1. **Comparação de estratégias:** peça aos estudantes que calculem a rota sequencial manualmente e expliquem por que o vizinho mais próximo pode ser pior.
2. **Análise econômica:** altere o custo por quilômetro e discuta a relação entre quilometragem, custo variável, custo fixo e economia percentual.
3. **Matrizes assimétricas:** compare `D[A][B]` com `D[B][A]` e discuta vias de sentido único, condições de tráfego e rotas de retorno.
4. **Análise de sensibilidade:** modifique alguns trechos, recalcule e identifique quais decisões de visita se alteraram.
5. **Tomada de decisão:** confronte a melhor rota matemática com restrições operacionais reais não incluídas no modelo.

## 🤝 Contribuições e licença

Sugestões de melhorias, relatos de problemas e contribuições podem ser enviados por meio de **Issues** e **Pull Requests** no GitHub.

**Licença:** nenhuma licença de código aberto está definida neste README. Se o projeto for disponibilizado para reutilização por terceiros, adicione um arquivo `LICENSE` com os termos escolhidos.

---

**Projeto educacional de roteirização logística** · Desenvolvido para apoiar o ensino de Logística e Pesquisa Operacional.

*Identidade visual inspirada na paleta Senac SP. Este repositório não representa, por si só, um produto oficial ou endossado pela instituição.*
