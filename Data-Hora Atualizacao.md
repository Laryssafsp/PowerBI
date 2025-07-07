# Data com atualização atual:

```M
let

in

Fonte = DateTime. LocalNow(),
#"Convertido para Tabela" = #table(1, {{Fonte}}),
#"Colunas Renomeadas" = Table. RenameColumns(#"Convertido para Tabela", {{"Column1", "Ultima Atualizacao"}}),
#"Tipo Alterado1" = Table. TransformColumnTypes(#"Colunas Renomeadas", {{"Última Atualizacao", type datetime}}),
#"Tipo Alterado com Localidade" = Table. TransformColumnTypes(#"Tipo Alterado1", {{"Última Atualizacao", type datetime}}, "pt-BR")

#"Tipo Alterado com Localidade"
```
# Data com atualização atual
- acontece que quando o dashboard fosse publicado, acabava ficando com o horário +3h

```M
let
    FonteUTC = DateTimeZone.UtcNow(),
    AjusteFuso = DateTimeZone.RemoveZone(FonteUTC - #duration(0,3,0,0)),
    Tabela = #table(1, {{AjusteFuso}}),
    ColunasRenomeadas = Table.RenameColumns(Tabela, {{"Column1", "Última Atualizacao"}}),
    TipoAlterado = Table.TransformColumnTypes(ColunasRenomeadas, {{"Última Atualizacao", type datetime}})
in
    TipoAlterado

```
