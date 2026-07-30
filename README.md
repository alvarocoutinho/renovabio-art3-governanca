# Da fronteira mecanizada ao carbono certificado: governança histórica, seleção territorial e desempenho no RenovaBio

[![DOI](https://zenodo.org/badge/1314198567.svg)](https://doi.org/10.5281/zenodo.21695174)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Pacote de replicação do manuscrito submetido a *Ambiente & Sociedade* (ANPPAS).

**Autor:** Alvaro Luz Alves Coutinho

**DOI (todas as versões):** 10.5281/zenodo.21695174

Saídas bilíngues: português (`ptbr/`) e inglês (`en/`).

---

## Resumo

Análise histórico-institucional da expansão do setor sucroenergético, da seleção
territorial da fronteira canavieira e da relação entre trajetória de governança e
desempenho contemporâneo certificado no RenovaBio.

O desenho combina a reconstrução da geografia histórica da fronteira (1985–2015),
a seleção topográfica da expansão a partir de modelo digital de elevação, a
análise do uso anterior das novas áreas de cana com bootstrap, e a modelagem de
ΔNEEA e Δvol% por geração institucional, com diagnóstico de autocorrelação
espacial dos resíduos.

| Dimensão | Escopo |
|---|---|
| Recorte espacial | Centro-Sul — GO, MG, MS, MT, PR, SP |
| Fronteira canavieira | 1985–2015 (esquema `pre_renovabio_1985_2015`) |
| Uso anterior | 1987–2015 |
| Unidade de análise | Usina certificada (CNPJ) e seu entorno territorial |
| Unidades no diagnóstico espacial | 225 |
| Escala principal | Buffer de 25 km |
| Robustez multiescala | 30, 50, 100 e 150 km |

---

## Validação de replicação

Executado a partir de **clone limpo**, sem acesso aos arquivos locais do autor:

| Resultado | |
|---|---|
| Outputs reproduzidos byte a byte | **154** |
| Divergências inesperadas | **0** |
| Divergências esperadas | 4 arquivos de inferência por permutação (ver abaixo) |
| Figuras | 75 (bytes diferem por timestamp de renderização) |

### Sobre as divergências esperadas

A inferência por permutação do I de Moran e do LISA passou a ser semeada nesta
versão (`spatial_seed = 20260609`). Os p-valores, portanto, diferem da versão
anterior do pacote — e passam a ser reproduzíveis. A estatística I, que não
depende de permutação, reproduz exatamente.

| | Versão anterior | Esta versão |
|---|---|---|
| Moran's I | 0,026 | **0,026296** |
| p (permutação) | 0,184 | **0,171** |

**Valor canônico a citar:**

```
Moran's I = 0,026296  |  p = 0,171  |  z = 0,94506
Modelo M5 (+ uso anterior)  |  n = 225  |  k = 6  |  999 permutações
```

### Robustez à vizinhança

| k | Moran's I | p |
|---|---|---|
| 4 | 0,034613 | 0,175 |
| **6** | **0,026296** | **0,171** |
| 8 | 0,011104 | 0,317 |
| 10 | −0,005924 | 0,490 |

A estatística decresce monotonicamente com k e nenhum p-valor se aproxima de
0,05 — a ausência de autocorrelação residual significativa é robusta à escolha
de vizinhança.

---

## Estrutura

```
├── notebooks/
│   └── renovabio_art3_governanca.ipynb   # pipeline completo, seções 0 a 8
├── pipeline.yaml                          # fonte canônica de parâmetros
├── data/
│   ├── raw/            # NÃO versionado — ver MANIFEST.md
│   ├── processed/      # insumos derivados, versionados (entrada do pipeline)
│   └── DICIONARIO.md
├── outputs/
│   ├── tables/{ptbr,en}/     # tabelas do manuscrito
│   ├── figures/{ptbr,en}/    # figuras a 600 dpi
│   ├── diagnostics/          # CRS, Moran/LISA, influência, tendências
│   ├── robustness/           # buffers, aproximações, caches ausentes
│   ├── dem_selection/        # seleção topográfica
│   └── next_analyses/        # uso anterior por unidade e buffer
└── requirements.txt
```

## Como reproduzir

```bash
git clone https://github.com/alvarocoutinho/renovabio-art3-governanca.git
cd renovabio-art3-governanca

python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
```

Execute o notebook em `notebooks/`, seguindo a ordem das seções (0 a 8). A
Seção 0 resolve os caminhos a partir da raiz do repositório, carrega os
parâmetros de `pipeline.yaml` e verifica os insumos obrigatórios antes de
qualquer modelagem (Gate 0) — nenhum insumo canônico é substituído ou simulado.

Para executar a partir de outro diretório:

```bash
export AMBSOC_REPO_ROOT=/caminho/para/o/repositorio
```

> O pacote consolidado gerado pela Seção 7.2 é gravado em `dist/`, fora de
> `outputs/`, e não é versionado.

### Roteiro das seções

| Seção | Conteúdo |
|---|---|
| 0 | Ambiente, caminhos, parâmetros e Gate 0 |
| 1 | Funções auxiliares e auditoria dos caches |
| 2 | Base analítica canônica |
| 3 | Geografia histórica da fronteira |
| 4 | Seleção topográfica da expansão |
| 5 | Uso anterior das novas áreas de cana (5.4 robustez de buffers; 5.5 fronteira aproximada) |
| 6 | Geração histórica, ΔNEEA e Δvol% (6.2 robustez de escala) |
| 7 | Tabelas consolidadas e pacote final |
| 8 | Leitura rápida esperada |

---

## Decisões metodológicas registradas no código

### Sistema de coordenadas — declarado, não inferido

As coordenadas X/Y das usinas estão em **EPSG:5880** (SIRGAS 2000 / Brazil
Polyconic), declarado em `pipeline.yaml`. Todos os buffers e a matriz de pesos
espaciais dependem dessa definição.

Verificação independente: os limites observados (X 4.435.218–6.176.905;
Y 7.359.058–8.620.088), interpretados nesse sistema, correspondem a longitude
−59,5 a −43,2 e latitude −23,8 a −12,3 — exatamente o recorte Centro-Sul.
O registro consta de `outputs/diagnostics/diagnostico_crs_coordenadas_usinas.csv`.

### Escala principal de 25 km

O modelo completo principal usa o buffer de 25 km, única escala com cache exato
disponível para fronteira, topografia e uso anterior. Os buffers de robustez
(30, 50, 100 e 150 km) são estimados por aproximação a partir da grade espacial
de 50 km das exportações do Google Earth Engine — método declarado no manuscrito.

Os buffers sem cache exato são registrados em
`outputs/robustness/missing_buffer_caches.csv`. Publicar esse registro é o que
permite distinguir uma estimativa exata de uma aproximada.

> Os buffers devem ser interpretados como aproximações espaciais da área de
> influência territorial da usina, não como delimitação observada de fornecedores.

### Substituição do buffer principal — desativada

`fallback_to_available_buffer: false`. A ausência do cache principal interrompe a
execução em vez de ser contornada em silêncio, evitando que o resultado dependa
de quais arquivos existem no disco.

### Sementes

| Procedimento | Semente |
|---|---|
| Bootstrap de uso anterior (1.500 réplicas) | `origin_seed = 20260609` |
| Inferência por permutação — Moran e LISA | `spatial_seed = 20260609` |

O RNG usado pelo `esda` é global. A Seção 0 define `seed_spatial_inference(offset)`,
chamada imediatamente antes de cada estimativa, com offset derivado de `k` — cada
teste tem semente própria e reproduzível.

### Base de uso anterior — duas rotas

O módulo de uso anterior aceita, em ordem de precedência:

1. **Base consolidada publicada** — `previous_land_use_grid_year_long.csv`
   (299.922 linhas, 1.158 células da grade, 1987–2023). Rota canônica.
2. **As oito exportações brutas do Google Earth Engine**, no padrão
   `previous_land_use_AAAA_AAAA.csv`. Rota original de construção.

A base consolidada é o produto direto da consolidação das oito exportações e
está preservada no pacote. Ambas as rotas produzem estrutura idêntica.

---

## Dados e licenciamento

Dados derivados em `data/processed/` e `outputs/` são produto desta pesquisa,
sob CC BY 4.0. Dados brutos de terceiros **não são redistribuídos** — fontes,
URLs, datas de acesso, versões e hashes SHA-256 em `data/raw/MANIFEST.md`.

- **Código** (`notebooks/`): MIT — ver `LICENSE`
- **Dados derivados, tabelas e figuras**: CC BY 4.0 — ver `LICENSE-DATA`

> **Atenção — share-alike.** Os dados de cobertura e uso do solo do MapBiomas são
> licenciados em CC BY-SA 4.0. A cláusula de compartilhamento pela mesma licença
> pode alcançar as tabelas de uso anterior, que derivam diretamente da grade.
> Ver `LICENSE-DATA`.

## Como citar

Ver `CITATION.cff` ou o botão *Cite this repository* no GitHub.

## Financiamento

This study was financed, in part, by the São Paulo Research Foundation (FAPESP),
Brazil. Process Number #2025/01530-0.
