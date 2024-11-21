## Linguagem M
```
let
    // Parâmetros de entrada
    Fonte = Consulta,  // Substitua 'Consulta' pelo nome da consulta de origem ou variável de dados.
    // DataCampo = "order_date",  // Substitua 'order_date' pelo nome do campo de data na consulta original.

    // Cálculos dinâmicos
    MaxData = List.Max(Fonte[DataCampo]),
    MinData = List.Min(Fonte[DataCampo]),
    MinAno = Date.Year(MinData),
    DataInicial = #date(MinAno, 1, 1),
    Hoje = Date.From(DateTime.LocalNow()),

    // Cálculo da duração (em dias)
    Duracao = Duration.Days(Hoje - DataInicial) + 1,

    // Geração da lista de datas
    Lista = List.Dates(DataInicial, Duracao, #duration(1,0,0,0)),

    // Convertendo a lista de datas para uma tabela
    #"Convertido para Tabela" = Table.FromList(Lista, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Colunas Renomeadas" = Table.RenameColumns(#"Convertido para Tabela",{{"Column1", "Data"}}),
    #"Tipo Alterado" = Table.TransformColumnTypes(#"Colunas Renomeadas",{{"Data", type date}})
in
    #"Tipo Alterado"
```


## DAX
```
dCalendario = CALENDAR(MIN(tab[Col_date]),MAX(tab[Col_date]))
```
