
- Criar uma Medida de Rank Dinâmico usando a função RANKX, levando em consideração os filtros aplicados (como o filtro de "dia da semana").
- Aplicar a Formatação Condicional para destacar as 4 maiores barras, com base na medida de rank.
- O filtro de dia da semana irá influenciar o cálculo do rank, destacando as 4 barras com maior valor para o conjunto de dias selecionados.

---

- ALLSELECTED: Permite que o cálculo de rank leve em consideração os filtros aplicados (como o filtro de "dia da semana") e só considere os dados visíveis após esses filtros.
- SUM(Tabela[qtd_agendamentos]): Soma os agendamentos para cada parâmetro (horário_30, horário_1).
 -DESC: Ordena os valores de forma decrescente, para que o maior valor tenha o rank 1. <br>

Aplicando a Formatação Condicional nas Barras

```
Rank Agendamentos = 
RANKX(
    ALLSELECTED(Tabela[horario_30], Tabela[horario_1]),  -- Seleciona os parâmetros para considerar nos cálculos
    CALCULATE(SUM(Tabela[qtd_agendamentos])),  -- A soma dos agendamentos
    , 
    DESC,  -- Ordenação em ordem decrescente, ou seja, maiores agendamentos primeiro
    Dense  -- Usa classificação densa (rank consecutivo)
)

```

Explicação:


Agora, vamos usar essa medida de rank para aplicar a formatação condicional e destacar as 4 barras com maior valor no gráfico de barras.

Selecione seu gráfico de barras no Power BI. <br>
No painel de Formatar (ícone de rolo de pintura), localize a opção Cores de Dados e clique em fx para aplicar a formatação condicional. <br>

Configuração da formatação condicional:

- Selecione "Formatar por": "Campo".
- Escolha a medida que você criou, chamada Rank Agendamentos.
- Em Baseado em valores de: escolha "Valores".
- Defina as regras de cores, por exemplo:
- Rank 1 a 4: Escolha uma cor chamativa (como verde ou azul).
- Rank > 4: Escolha uma cor mais neutra (como cinza claro).



## Destaque com "if" pois o gráfico é realizado a aprtir de parâmetros:

```
Rank Agendamentos = 
IF (
    ISFILTERED(Tabela[horario_1]),  -- Verifica se o filtro está em horario_1
    RANKX(
        ALLSELECTED(Tabela[horario_1]),  -- Aplica o rank apenas para os valores de horario_1
        CALCULATE(SUM(Tabela[qtd_agendamentos])),  -- Soma dos agendamentos
        , 
        DESC,  -- Ordenação decrescente
        Dense  -- Classificação densa
    ),
    IF (
        ISFILTERED(Tabela[horario_30]),  -- Verifica se o filtro está em horario_30
        RANKX(
            ALLSELECTED(Tabela[horario_30]),  -- Aplica o rank apenas para os valores de horario_30
            CALCULATE(SUM(Tabela[qtd_agendamentos])),  -- Soma dos agendamentos
            , 
            DESC,  -- Ordenação decrescente
            Dense  -- Classificação densa
        ),
        BLANK()  -- Se nenhum filtro for aplicado, retorna em branco (você pode ajustar isso conforme necessário)
    )
)

```
