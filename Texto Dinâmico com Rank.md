Realizar um texto dinâmico informando os horários que deverão ser evitados conforme o usuário filtro os dias da semana.

### realizar agrupamento dos horários em 30 min ou 1h para facilitar a sumarização (30min ou 1h)

```
Hour_30minutes = 
IF (
    NOT (ISBLANK('tabela1'[hora_agendamentos])), 
    VAR HoraOriginal = 'tabela1'[hora_agendamentos]
    VAR Minutos = MINUTE(HoraOriginal)
    VAR HoraArredondada =
        IF (
            Minutos < 30, 
            TIME(HOUR(HoraOriginal), 0, 0),  -- Se os minutos forem menores que 30, arredonda para a hora cheia anterior
            TIME(HOUR(HoraOriginal), 30, 0)  -- Se os minutos forem 30 ou mais, arredonda para a próxima meia hora
        )
    RETURN HoraArredondada,
    BLANK()  -- Retorna em branco se o horário estiver vazio

----
Horario (1h) = 
IF (
    NOT(ISBLANK('tabela1'[hora_agendamentos])),  -- Verifica se o horário não é nulo
    IF (
        MINUTE('tabela1'[hora_agendamentos]) > 0,  -- Verifica se os minutos são maiores que zero
        TIME(HOUR('tabela1'[hora_agendamentos]), 0, 0),  -- Retorna a hora cheia anterior
        TIME(HOUR('tabela1'[hora_agendamentos]), 0, 0)   -- Retorna a hora cheia
    ),
    BLANK()  -- Retorna em branco se o horário estiver vazio
)

```

---

### traduzir os dias da semana
```
Dia da Semana = 
SWITCH(
    TRUE(),
    'tabela1'[dias_agendamentos] = "Monday", "Segunda-feira",
    'tabela1'[dias_agendamentos] = "Tuesday", "Terça-feira",
    'tabela1'[dias_agendamentos] = "Wednesday", "Quarta-feira",
    'tabela1'[dias_agendamentos] = "Thursday", "Quinta-feira",
    'tabela1'[dias_agendamentos] = "Friday", "Sexta-feira",
    'tabela1'[dias_agendamentos] = "Saturday", "Sábado",
    'tabela1'[dias_agendamentos] = "Sunday", "Domingo",
    "Dia não registrado"  -- Caso o valor não corresponda a um dia válido
)

```

---

### criar ordenação para os dias da semana
```
Dia da Semana(orde) = 
SWITCH(
    TRUE(),
    'tabela1'[dias_agendamentos] = "Monday", "1",
    'tabela1'[dias_agendamentos] = "Tuesday", "2",
    'tabela1'[dias_agendamentos] = "Wednesday", "3",
    'tabela1'[dias_agendamentos] = "Thursday", "4",
    'tabela1'[dias_agendamentos] = "Friday", "5",
    'tabela1'[dias_agendamentos] = "Saturday", "6",
    'tabela1'[dias_agendamentos] = "Sunday", "0",
    "7"  -- Caso o valor não corresponda a um dia válido
)

```

### criar a mensagem dinâmica

#### 1 - com todos os dias sendo informados de uma vez:

```
Mensagem_Evitar_Horarios = 
var top_N = 5

var Segunda_Feira = 
CONCATENATEX(
    DISTINCT(
        SELECTCOLUMNS(
            FILTER(
                tabela1,
                tabela1[rank] < top_N && tabela1[Dia da Semana] = "Segunda-feira"
            ),
            "Horario", tabela1[Hour_30minutes]
        )
    ),
    [Horario],","
)

var Terca_Feira = 
CONCATENATEX(
    DISTINCT(
        SELECTCOLUMNS(
            FILTER(
                tabela1,
                tabela1[rank] < top_N && tabela1[Dia da Semana] = "Terça-feira"
            ),
            "Horario", tabela1[Hour_30minutes]
        )
    ),
    [Horario],","
)

var Quarta_Feira = 
CONCATENATEX(
    DISTINCT(
        SELECTCOLUMNS(
            FILTER(
                tabela1,
                tabela1[rank] < top_N && tabela1[Dia da Semana] = "Quarta-feira"
            ),
            "Horario", tabela1[Hour_30minutes]
        )
    ),
    [Horario],","
)

var Quinta_Feira = 
CONCATENATEX(
    DISTINCT(
        SELECTCOLUMNS(
            FILTER(
                tabela1,
                tabela1[rank] < top_N && tabela1[Dia da Semana] = "Quinta-feira"
            ),
            "Horario", tabela1[Hour_30minutes]
        )
    ),
    [Horario],","
)

var Sexta_Feira = 
CONCATENATEX(
    DISTINCT(
        SELECTCOLUMNS(
            FILTER(
                tabela1,
                tabela1[rank] < top_N && tabela1[Dia da Semana] = "Sexta-feira"
            ),
            "Horario", tabela1[Hour_30minutes]
        )
    ),
    [Horario],","
)

var Sabado = 
CONCATENATEX(
    DISTINCT(
        SELECTCOLUMNS(
            FILTER(
                tabela1,
                tabela1[rank] < top_N && tabela1[Dia da Semana] = "Sábado"
            ),
            "Horario", tabela1[Hour_30minutes]
        )
    ),
    [Horario],","
)

var Domingo = 
CONCATENATEX(
    DISTINCT(
        SELECTCOLUMNS(
            FILTER(
                tabela1,
                tabela1[rank] < top_N && tabela1[Dia da Semana] = "Domingo"
            ),
            "Horario", tabela1[Hour_30minutes]
        )
    ),
    [Horario],","
)




var text_segunda =  "Nas Segundas-feiras, evite os seguintes horários: " & (Segunda_Feira)
var text_terca =  "Nas Terca-feira, evite os seguintes horários: " & (Terca_Feira)
var text_quarta =  "Nas Quarta_Feira, evite os seguintes horários: " & (Quarta_Feira)
var text_quinta =  "Nas Quinta_Feira, evite os seguintes horários: " & (Quinta_Feira)
var text_sexta =  "Nas Sexta_Feiras, evite os seguintes horários: " & (Sexta_Feira)
var text_sabado =  "Nas Sabado, evite os seguintes horários: " & (Sabado)
var text_domingo =  "Nas Domingo, evite os seguintes horários: " & (Domingo)

return 


text_segunda & CONCATENATE("   ", UNICHAR(10)) & UNICHAR(10) & // Quebra de linha
text_terca & CONCATENATE("   ", UNICHAR(10)) & UNICHAR(10) &
text_quarta & CONCATENATE("   ", UNICHAR(10)) & UNICHAR(10) &
text_quinta & CONCATENATE("   ", UNICHAR(10)) & UNICHAR(10) &
text_sexta & CONCATENATE("   ", UNICHAR(10)) & UNICHAR(10) &
text_domingo & CONCATENATE("   ", UNICHAR(10)) & UNICHAR(10) &
text_terca & CONCATENATE("   ", UNICHAR(10)) & UNICHAR(10) 
```

#### 2 - mensagem com título

```
Evitar_erro_com_filtro_sem_titulo = 
VAR DiaSelecionado = VALUES(tabela1[Dia da Semana])
var top_N = 5
VAR Segunda_Feira =
    CONCATENATEX(
        DISTINCT(
            SELECTCOLUMNS(
                FILTER(
                    tabela1,
                    tabela1[rank] < top_N &&
                    tabela1[Dia da Semana] = "Segunda-feira"
                ),
                "Horario", tabela1[Hour_30minutes]
            )
        ),
        [Horario], " , "
    )

VAR Terca_Feira =
    CONCATENATEX(
        DISTINCT(
            SELECTCOLUMNS(
                FILTER(
                    tabela1,
                    tabela1[rank] < top_N &&
                    tabela1[Dia da Semana] = "Terça-feira"
                ),
                "Horario", tabela1[Hour_30minutes]
            )
        ),
        [Horario], " , "
    )

VAR Quarta_Feira =
    CONCATENATEX(
        DISTINCT(
            SELECTCOLUMNS(
                FILTER(
                    tabela1,
                    tabela1[rank] < top_N &&
                    tabela1[Dia da Semana] = "Quarta-feira"
                ),
                "Horario", tabela1[Hour_30minutes]
            )
        ),
        [Horario], " , "
    )

VAR Quinta_Feira =
    CONCATENATEX(
        DISTINCT(
            SELECTCOLUMNS(
                FILTER(
                    tabela1,
                    tabela1[rank] < top_N &&
                    tabela1[Dia da Semana] = "Quinta-feira"
                ),
                "Horario", tabela1[Hour_30minutes]
            )
        ),
        [Horario], " , "
    )

VAR Sexta_Feira =
    CONCATENATEX(
        DISTINCT(
            SELECTCOLUMNS(
                FILTER(
                    tabela1,
                    tabela1[rank] < top_N &&
                    tabela1[Dia da Semana] = "Sexta-feira"
                ),
                "Horario", tabela1[Hour_30minutes]
            )
        ),
        [Horario], " , "
    )

VAR Sabado =
    CONCATENATEX(
        DISTINCT(
            SELECTCOLUMNS(
                FILTER(
                    tabela1,
                    tabela1[rank] < top_N &&
                    tabela1[Dia da Semana] = "Sábado"
                ),
                "Horario", tabela1[Hour_30minutes]
            )
        ),
        [Horario], " , "
    )

VAR Domingo =
    CONCATENATEX(
        DISTINCT(
            SELECTCOLUMNS(
                FILTER(
                    tabela1,
                    tabela1[rank] < top_N &&
                    tabela1[Dia da Semana] = "Domingo"
                ),
                "Horario", tabela1[Hour_30minutes]
            )
        ),
        [Horario], " , "
    )

VAR Resultado = 
    IF(
        ISFILTERED(tabela1[Dia da Semana]),
        IF(
            "Segunda-feira" IN DiaSelecionado,
            "As Segundas-feiras, evite: " & Segunda_Feira & UNICHAR(10),
            ""
        ) &
        IF(
            "Terça-feira" IN DiaSelecionado,
            "As Terças-feiras, evite: " & Terca_Feira & UNICHAR(10),
            ""
        ) &
        IF(
            "Quarta-feira" IN DiaSelecionado,
            "As Quartas-feiras, evite: " & Quarta_Feira & UNICHAR(10),
            ""
        ) &
        IF(
            "Quinta-feira" IN DiaSelecionado,
            "As Quintas-feiras, evite: " & Quinta_Feira & UNICHAR(10),
            ""
        ) &
        IF(
            "Sexta-feira" IN DiaSelecionado,
            "As Sextas-feiras, evite: " & Sexta_Feira & UNICHAR(10),
            ""
        ) &
        IF(
            "Sábado" IN DiaSelecionado,
            "Aos Sábados, evite: " & Sabado & UNICHAR(10),
            ""
        ) &
        IF(
            "Domingo" IN DiaSelecionado,
            "Aos Domingos, evite: " & Domingo & UNICHAR(10),
            ""
        ),"..."
        --"Selecione os dias para verificar os melhores horários"
    )

RETURN
    Resultado

```

#### 3 - Mensagem mostrando todos e filtrando conforme selecionado

```
Evitar_erro_com_filtro_sem_titulo = 
VAR DiaSelecionado = VALUES(tabela1[Dia da Semana])
var top_N = 5

VAR Segunda_Feira =
    CONCATENATEX(
        DISTINCT(
            SELECTCOLUMNS(
                FILTER(
                    tabela1,
                    tabela1[rank] < top_N &&
                    tabela1[Dia da Semana] = "Segunda-feira"
                ),
                "Horario", tabela1[Hour_30minutes]
            )
        ),
        [Horario], " , ", 
        [Horario], ASC
    )

VAR Terca_Feira =
    CONCATENATEX(
        DISTINCT(
            SELECTCOLUMNS(
                FILTER(
                    tabela1,
                    tabela1[rank] < top_N &&
                    tabela1[Dia da Semana] = "Terça-feira"
                ),
                "Horario", tabela1[Hour_30minutes]
            )
        ),
        [Horario], " , ", 
        [Horario], ASC
    )

VAR Quarta_Feira =
    CONCATENATEX(
        DISTINCT(
            SELECTCOLUMNS(
                FILTER(
                    tabela1,
                    tabela1[rank] < top_N &&
                    tabela1[Dia da Semana] = "Quarta-feira"
                ),
                "Horario", tabela1[Hour_30minutes]
            )
        ),
        [Horario], " , ", 
        [Horario], ASC
    )

VAR Quinta_Feira =
    CONCATENATEX(
        DISTINCT(
            SELECTCOLUMNS(
                FILTER(
                    tabela1,
                    tabela1[rank] < top_N &&
                    tabela1[Dia da Semana] = "Quinta-feira"
                ),
                "Horario", tabela1[Hour_30minutes]
            )
        ),
        [Horario], " , ", 
        [Horario], ASC
    )

VAR Sexta_Feira =
    CONCATENATEX(
        DISTINCT(
            SELECTCOLUMNS(
                FILTER(
                    tabela1,
                    tabela1[rank] < top_N &&
                    tabela1[Dia da Semana] = "Sexta-feira"
                ),
                "Horario", tabela1[Hour_30minutes]
            )
        ),
        [Horario], " , ", 
        [Horario], ASC
    )

VAR Sabado =
    CONCATENATEX(
        DISTINCT(
            SELECTCOLUMNS(
                FILTER(
                    tabela1,
                    tabela1[rank] < top_N &&
                    tabela1[Dia da Semana] = "Sábado"
                ),
                "Horario", tabela1[Hour_30minutes]
            )
        ),
        [Horario], " , ", 
        [Horario], ASC
    )

VAR Domingo =
    CONCATENATEX(
        DISTINCT(
            SELECTCOLUMNS(
                FILTER(
                    tabela1,
                    tabela1[rank] < top_N &&
                    tabela1[Dia da Semana] = "Domingo"
                ),
                "Horario", tabela1[Hour_30minutes]
            )
        ),
        [Horario], " , ", 
        [Horario], ASC
    )

VAR Resultado = 
    IF(
        ISFILTERED(tabela1[Dia da Semana]),
        IF(
            "Segunda-feira" IN DiaSelecionado,
            "As Segundas-feiras, evite: " & Segunda_Feira & UNICHAR(10),
            ""
        ) &
        IF(
            "Terça-feira" IN DiaSelecionado,
            "As Terças-feiras, evite: " & Terca_Feira & UNICHAR(10),
            ""
        ) &
        IF(
            "Quarta-feira" IN DiaSelecionado,
            "As Quartas-feiras, evite: " & Quarta_Feira & UNICHAR(10),
            ""
        ) &
        IF(
            "Quinta-feira" IN DiaSelecionado,
            "As Quintas-feiras, evite: " & Quinta_Feira & UNICHAR(10),
            ""
        ) &
        IF(
            "Sexta-feira" IN DiaSelecionado,
            "As Sextas-feiras, evite: " & Sexta_Feira & UNICHAR(10),
            ""
        ) &
        IF(
            "Sábado" IN DiaSelecionado,
            "Aos Sábados, evite: " & Sabado & UNICHAR(10),
            ""
        ) &
        IF(
            "Domingo" IN DiaSelecionado,
            "Aos Domingos, evite: " & Domingo & UNICHAR(10),
            ""
        )
        ,"..."
        --"Selecione os dias para verificar os melhores horários"
    )

RETURN
    Resultado

```

---


#### 4 - Mensagem mostrando conforme selecionados e horarios distintos

```
Evitar_erro_com_filtro_sem_titulo = 
VAR DiaSelecionado = VALUES(tabela1[Dia da Semana])
VAR top_N = 5

-- Função para concatenar horários distintos para um dia específico
VAR HorariosDistintos =
    CONCATENATEX(
        DISTINCT(
            SELECTCOLUMNS(
                FILTER(
                    tabela1,
                    tabela1[rank] < top_N &&
                    tabela1[Dia da Semana] IN DiaSelecionado
                ),
                "Horario", tabela1[Hour_30minutes]
            )
        ),
        [Horario], " , ", 
        [Horario], ASC
    )

-- Função para formatar os dias selecionados
VAR DiasFormatados =
    CONCATENATEX(
        DiaSelecionado,
        [Dia da Semana],
        ", "
    )

-- Resultado baseado na filtragem
VAR Resultado = 
    IF(
        -- Se nenhum dia for selecionado
        NOT ISFILTERED(tabela1[Dia da Semana]),
        "Selecione os dias para verificar os melhores horários",
        
        -- Caso dias sejam filtrados, mostre a mensagem com os horários
        "Nos dias " & DiasFormatados & ", você deve evitar os horários: " & HorariosDistintos
    )

RETURN
    Resultado

```
