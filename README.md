# GreenRoute Agro: quando a chuva muda a rota do trator

**Português** · [English](README.en.md)

Projeto do **Global Solution 2026.1 da FIAP** (Data Science, turma 1TSCOR), com o tema *indústria espacial e dados*. Foi escolhido como **o melhor projeto da turma**.

A proposta é uma plataforma que recalcula a rota de máquinas agrícolas com dados abertos de satélite e de clima. Este repositório traz a parte analítica que foi construída: um estudo com dados reais do INMET que mostra quantos dias da safra são de risco operacional para o maquinário e como esses dias se comportam.

---

## O problema

Fazendas grandes costumam usar rotas fixas para tratores e colheitadeiras. Depois de uma chuva forte, o solo encharcado aumenta a patinagem dos pneus e a resistência ao rolamento: o trator gasta mais diesel, desgasta mais e compacta o solo. A pergunta da análise é simples: **com que frequência isso acontece numa safra real, e dá para identificar esses dias com dados públicos?**

## A proposta (o que foi apresentado no pitch)

O GreenRoute Agro cruzaria relevo (Copernicus DEM), vegetação (MapBiomas), clima (INMET) e limites da propriedade (CAR) num grafo do terreno. Um algoritmo A\* calcularia a rota de menor custo, e um painel em Streamlit com mapa em Folium mostraria a rota e a estimativa de CO₂ evitado.

**Essa parte é proposta, não implementação.** O A\*, o painel e a arquitetura em nuvem foram desenhados e apresentados no pitch, mas não estão neste repositório. Os números de economia do pitch (como os 34% de diesel) são projeções baseadas em referências do setor, não resultados medidos.

## A análise (o que foi construído)

**Dados:** estação automática A904 do INMET, em Sorriso (MT), de 01/01 a 31/12/2025. São 8.760 registros horários de precipitação e temperatura, agregados em 365 dias.

**Etapas do notebook:**
1. **Carga e limpeza:** CSV do INMET com separador `;`, decimal com vírgula e falhas de sensor; conversão para número e agregação diária (chuva somada, temperatura média).
2. **Regra de risco operacional:** dia com mais de 5 mm de chuva = risco alto de patinagem.
3. **Estatística descritiva e série temporal** da chuva, com a linha de risco.
4. **Correlação e teste de hipótese:** a temperatura cai nos dias de risco? (Pearson e teste t unilateral)
5. **Chuva acumulada na safra** (soma cumulativa, a "integral discreta" da precipitação diária).

**Resultados:**
- **80 dos 365 dias** (22%) passaram de 5 mm de chuva. Eles se concentram de novembro a março: dezembro e janeiro têm 14 e 15 dias de risco; julho e agosto, nenhum.
- **1.787 mm** de chuva acumulada em 2025.
- Os dias de risco foram **cerca de 2 °C mais frios** (25,0 °C contra 27,1 °C; teste t unilateral, p < 0,001), e a correlação entre chuva e temperatura foi fraca e negativa (r = −0,28).

**O que isso significa para a proposta:** em quase um quarto do ano, a rota planejada no seco deixa de ser a melhor. É essa janela, concentrada entre o fim do plantio e a colheita da soja, que justifica uma rota que muda com o clima.

## Limitações

- **Uma estação para uma fazenda inteira.** A chuva medida num ponto do município não representa uma propriedade de milhares de hectares.
- **O limite de 5 mm é uma regra simplificada.** O efeito da chuva na tração depende do tipo de solo e da umidade acumulada nos dias anteriores.
- **Horas sem registro contam como zero.** 633 horas (7%) não têm leitura de chuva; na soma diária, elas entram como zero, então a chuva pode estar subestimada.
- **Dia em UTC.** O INMET registra a hora em UTC, e a agregação diária usa a data em UTC, não no horário de Mato Grosso (UTC−4).
- **O teste mostra associação, não causa.** E temperatura não é o que gasta diesel; ela só ajuda a caracterizar os dias de chuva.
- **Consumo de diesel não foi medido.** O valor de 12,8 L/ha citado na conclusão do notebook vem de referência do setor, não destes dados. Validar a economia exigiria telemetria real das máquinas.

## Como rodar

```bash
pip install -r requirements.txt
jupyter notebook greenroute_agro_analise_climatica.ipynb
```

O notebook lê `dados/dados_inmet_sorriso_2025.csv` e grava `dados/greenroute_diario_final.csv` (base diária com a classificação de risco e a chuva acumulada).

## Estrutura

```
├── dados/
│   ├── dados_inmet_sorriso_2025.csv   dados horários brutos do INMET (estação A904)
│   └── greenroute_diario_final.csv    base diária gerada pelo notebook
├── greenroute_agro_analise_climatica.ipynb
└── requirements.txt
```

## Equipe

Projeto em grupo de **Bruno Gomide** (representante), **Leonardo Lacerda**, **Pedro Freixo** e **Vitor Coppe**. Minha parte principal foi o notebook de análise.

Fonte dos dados: [INMET – Instituto Nacional de Meteorologia](https://tempo.inmet.gov.br/), dados abertos.

---

Publicado por **Pedro Freixo** · [GitHub](https://github.com/freixopedro23)
