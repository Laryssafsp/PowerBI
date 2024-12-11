Para criar uma medida que mostre o título do gráfico de barras de acordo com os dias da semana filtrados, podemos usar a função CONCATENATEX para montar uma string com os dias da semana selecionados. Essa medida pode ser dinâmica e se ajustará conforme o filtro aplicado no gráfico.
Aqui estão os passos para criar a medida do título dinâmico com base nos dias da semana filtrados:

- Passos:
Criação da Medida para o Título Dinâmico:

A medida irá verificar os dias da semana que estão selecionados e criar uma string com esses dias. Você pode usar a função CONCATENATEX para concatenar os valores selecionados e exibi-los no título.
Aqui está um exemplo de medida que você pode criar:

```
Título Gráfico = 
VAR DiasSelecionados = 
    VALUES(Tabela[dia_da_semana])  -- Captura os dias da semana filtrados
RETURN 
    IF(
        COUNTROWS(DiasSelecionados) > 1,
        "Agendamentos para os dias: " & CONCATENATEX(DiasSelecionados, Tabela[dia_da_semana], ", "),
        "Agendamentos para o dia: " & CONCATENATEX(DiasSelecionados, Tabela[dia_da_semana], "")
    )
```

Explicação da medida:

- VALUES(Tabela[dia_da_semana]): Captura todos os dias da semana que estão sendo exibidos no gráfico, após a aplicação dos filtros. <br>
- CONCATENATEX(DiasSelecionados, Tabela[dia_da_semana], ", "): Cria uma lista de dias concatenados por vírgula. Se mais de um dia estiver selecionado, ele será exibido como "Segunda-feira, Terça-feira", por exemplo. <br>
- IF(COUNTROWS(DiasSelecionados) > 1, ...): Se mais de um dia estiver selecionado, ele mostra "Agendamentos para os dias: ...". Caso contrário, mostra "Agendamentos para o dia: ...". <br>

**Aplicar na função das cores da barra**
