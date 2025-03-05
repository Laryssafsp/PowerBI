# Calendario no Power Query

```m
let
    // Defina a data mínima
    MinDate = #date(2020, 1, 1),  // Alterar para a data mínima desejada
    
    // Defina a data de hoje
    MaxDate = DateTime.LocalNow(),
    
    // Geração das datas entre a data mínima e a data de hoje
    DateList = List.Dates(MinDate, Duration.Days(MaxDate - MinDate) + 1, #duration(1, 0, 0, 0)),
    
    // Converter a lista em uma tabela
    DateTable = Table.FromList(DateList, Splitter.SplitByNothing(), {"Date"}),

    // Adicionar mais colunas (exemplo: Ano, Mês, Dia)
    AddYear = Table.AddColumn(DateTable, "Year", each Date.Year([Date]), Int64.Type),
    AddMonth = Table.AddColumn(AddYear, "Month", each Date.Month([Date]), Int64.Type),
    AddDay = Table.AddColumn(AddMonth, "Day", each Date.Day([Date]), Int64.Type),
    AddMonthName = Table.AddColumn(AddDay, "MonthName", each Date.ToText([Date], "MMMM")),
    AddDayOfWeek = Table.AddColumn(AddMonthName, "DayOfWeek", each Date.DayOfWeek([Date]), Int64.Type),
    AddDayName = Table.AddColumn(AddDayOfWeek, "DayName", each Date.ToText([Date], "dddd"))
in
    AddDayName
```
