# 📘 Como lidar com duplicidade no `LOOKUPVALUE` no Power BI (DAX)

## Problema

Ao usar `LOOKUPVALUE`, pode aparecer o erro:

> "Uma tabela de vários valores foi fornecida, sendo que um único valor era esperado."

Isso acontece quando a chave de busca retorna **mais de um valor possível**.

Exemplo (com erro):

```DAX
UnidadeNegocio =
LOOKUPVALUE (
    Dim_Times[BusinessUnit],
    Dim_Times[NomeTime], Fato_Qualidade[NomeTime]
)
```

Se existir mais de um `BusinessUnit` para o mesmo `NomeTime` em `Dim_Times`, o DAX não consegue decidir qual retornar.

---

## Soluções

### 🔹 1. Garantir unicidade via modelagem

O ideal é que a tabela de dimensões (`Dim_Times`) tenha apenas **um registro por NomeTime**.
Você pode construir uma tabela distinta:

```DAX
Dim_Times =
DISTINCT (
    SELECTCOLUMNS (
        TabelaOriginal,
        "NomeTime", TabelaOriginal[NomeTime],
        "BusinessUnit", TabelaOriginal[BusinessUnit]
    )
)
```

Depois relacione `Dim_Times` com a tabela de fatos (`Fato_Qualidade`).

---

### 🔹 2. Pegar apenas um valor (quando não há unicidade garantida)

Se você só precisa **do primeiro valor encontrado** (por exemplo, porque a tabela já guarda apenas o registro mais recente), pode usar:

#### ✅ Usando `FIRSTNONBLANK`

```DAX
UnidadeNegocio =
FIRSTNONBLANK (
    CALCULATETABLE (
        Dim_Times[BusinessUnit],
        FILTER (
            Dim_Times,
            Dim_Times[NomeTime] = Fato_Qualidade[NomeTime]
        )
    ),
    1
)
```

#### ✅ Usando `MINX` (ou `MAXX`)

Outra forma é escolher sempre o menor/maior valor válido:

```DAX
UnidadeNegocio =
MINX (
    FILTER (
        Dim_Times,
        Dim_Times[NomeTime] = Fato_Qualidade[NomeTime]
    ),
    Dim_Times[BusinessUnit]
)
```

---

## Quando usar cada abordagem

* **Modelagem com DISTINCT** → quando você pode ajustar os dados para garantir 1:1.
* **FIRSTNONBLANK/MINX** → quando basta pegar um valor e você sabe que não terá impacto de negócio (ex: registros já filtrados para o mais recente).
