# Data-Driven-Marketing-PBI
Formulas em dax para Power BI para criação de card de valor total + valor do mês anterior + variação percentual.

## Card de Visualização com comparativo de data e variação percentual com formatação positiva ou negativa
1. Antes de começar deve extrair uma base contendo os dados que será utilizado, no meu caso os dados forma extraidos através do supermetricasde das plataformas Facebook Insights e Instagram Insights e depois exportados em xls para Power BI onde foram tratados e posteriormente feito calculos para gerar as medidas de **Impressions**, **Engagement** e **Engagement Tax**.
2. Também foi criado uma tabela de **Calendário** e conectada com a tabela de dados das plataformas insights para criação de comparativos e evoluções mensais.

<div align="center"

**Modelo Visual:**

![image](https://github.com/remargoni/Data-Driven-Marketing-PBI/assets/138613597/be632bbb-78ea-4d64-ab8c-6298c2bc5f51)

</div>

## Medidas Impressions
Deve ser alterado o formato para dinamico por que as formulas são aplicadas tanto no campo de medida quanto no campo de formato.

[MEDIDA] Impressions Calculated (IC)
  
                Impressions Calculated = sum('NOME-DA-TABELA'[Impressions])
  
[FORMATO] Impressions Calculated (IC)

                VAR vImpacto = sum('NOME-DA-TABELA'[Impressions])

                VAR vImpacto_ajustado =
                 """" & 
                       SWITCH(
                           TRUE(),
                           vImpacto >= 1000000000, FORMAT(vImpacto, "#,0,,,.00 Bi"),
                           vImpacto >= 1000000, FORMAT(vImpacto, "#,0,,.00 Mi"),
                           vImpacto >= 1000, FORMAT(vImpacto, "#,0,.00 K"),
                           FORMAT(vImpacto, "#")
                       )
                       & """"
                RETURN
                 vImpacto_ajustado
 
[MEDIDA] Impressions Previous Month (IPM)
 
               Impressions Previous Month = CALCULATE(SUM('NOME-DA-TABELA'[Impressions]),PREVIOUSMONTH('Calendário'[Data]))

[MEDIDA] Impressions Monthly Percentage Change (IMPC)
 
               Impressions Monthly Percentage Change = DIVIDE(SUM('IG-FB Orgãnico'[Impressions])-[Impressions Previous Month],[Impressions Previous Month],0)

[MEDIDA] Impressions Card Variation (ICV)

              Impressions Card Variation = [Impressions Previous Month]
              
[FORMATO] Impressions Card Variation (ICV)

              VAR vRealizado = [Impressions Previous Month]
              
              VAR vPorc_Variada = [Impressions Monthly Percentage Change]
              
              VAR vRealizado_ajustado =
               """" & 
                     SWITCH(
                         TRUE(),
                         vRealizado >= 1000000000, FORMAT(vRealizado, "#,0,,,.00 Bi"),
                         vRealizado >= 1000000, FORMAT(vRealizado, "#,0,,.00 Mi"),
                         vRealizado >= 1000, FORMAT(vRealizado, "#,0,.00 K"),
                         FORMAT(vRealizado, "#")
                     )
                  
                  & """"
              
              VAR vPorc_Ajustada =
               """" & 
                         FORMAT(vPorc_Variada, "0.00%")
                  
                  & """"
              
              VAR vIcone =
                   SWITCH(
                       TRUE(),
                        vPorc_Variada > 0, "▲ ",
                        vPorc_Variada < 0, "▼ "
                        )
              Return
                  vIcone & vRealizado_ajustado & " | " & vPorc_Ajustada

[MEDIDA] Impressions Card Condicional Form Validacion (ICV) - Deve ser aplicado no campo de fx dos valores do card

              Impressions Card Condicional Form Validacion = 
              
              Var vPorc_Variada = [Impressions Previous Month]
              
              Return
                   SWITCH(
                       TRUE(),
                        vPorc_Variada > 0, "Green",
                        vPorc_Variada < 0, "RED",
                        "GREY"
                        )


