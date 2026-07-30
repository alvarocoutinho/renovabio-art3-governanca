# Manifesto de dados

Registro das fontes primárias e dos insumos derivados do pacote de replicação.

| Dimensão | Escopo |
|---|---|
| Recorte espacial | Centro-Sul — GO, MG, MS, MT, PR, SP |
| Fronteira canavieira | 1985–2015 (esquema `pre_renovabio_1985_2015`) |
| Uso anterior | 1987–2015 (grade 1987–2023) |
| Unidade de análise | Usina certificada (CNPJ) e entorno territorial |
| Unidades no diagnóstico espacial | 225 |
| Escala principal | Buffer de 25 km |
| CRS das coordenadas de usina | EPSG:5880 — SIRGAS 2000 / Brazil Polyconic |
| Todos os acessos verificados em | 2026-07-28 |

---

## 1. Insumos obrigatórios — versionados neste repositório

Produtos desta pesquisa, em `data/processed/`, sob licença CC BY 4.0. São os
arquivos que o Gate 0 verifica antes de qualquer modelagem.

| Arquivo | Tamanho | Papel no pipeline | SHA-256 |
|---|---|---|---|
| `base_psm_integrada_raw.csv` | 5331 KB | Base municipal de indicadores ex-ante (2017) | `cd1bbcb153b09b3af58e9e9d45d38ab6ce3530a656219aa47c3f674a181e1973` |
| `centro_sul_munis.geojson` | 10180 KB | Geometrias municipais do recorte Centro-Sul | `46bba8347ebccc6191f0e65383b87ca9722e68e07aaf3a55511d322a39ab5fb0` |
| `crosswalk_centrosul.csv` | 133 KB | Crosswalk usina → município sede | `13955dbf3cf8ec53293dcf8025a75d5eff3b02a7b1ce84124ab74430aa30bf5a` |
| `mill_confounder_base.csv` | 68 KB | Confundidores em nível de usina | `be45aa71849a07e19a713d7797203b31a8a5c8483b60d235145ef3da3777808c` |
| `painel_canonico_wide.csv` | 97 KB | PRIMÁRIO. Painel de usinas: NEEA, vol%, coordenadas X/Y (EPSG:5880), CNPJ | `e16202d25f0d10b6fba5ffa345266910a8da296c15ea1bdfa9121e1ebee06c49` |

Verificação:

```bash
sha256sum data/processed/*
```

---

## 2. Caches de análise — versionados

Produtos intermediários custosos de regerar, preservados no pacote para permitir
reprodução sem reprocessamento das fontes brutas.

### 2.1 Topografia — `outputs/dem_selection/`

Cruzamento entre fronteira canavieira e declividade, derivado do modelo digital
de elevação (Seção 3 abaixo).

| Arquivo | Tamanho | SHA-256 |
|---|---|---|
| `dem_frontier_10y_1985_2024_25km.csv` | 148 KB | `be6a49f3fbc9b40e6f48927ecc071be22c4266fe2686842e318db7440f9de621` |
| `dem_frontier_5y_1985_2024_25km.csv` | 229 KB | `4c8212df5b5ae42c024e92d437e5d06fe6b47fca6dc41297207b219a4fdf63dc` |
| `dem_frontier_all_schemas.csv` | 572 KB | `ad0b1268da4aac5a1073bfe33be82f4df8a1c21c82f154ee1cb334e7725fb218` |
| `dem_frontier_all_schemas_25km.csv` | 572 KB | `ad0b1268da4aac5a1073bfe33be82f4df8a1c21c82f154ee1cb334e7725fb218` |
| `dem_frontier_neea_models.csv` | 20 KB | `a18ee3fdcf52dae68ee9f29f8ebfb682b06d4bbbeca9c3e5ed3d0bebe66e66e3` |
| `dem_frontier_pre_renovabio_1985_2015_25km.csv` | 190 KB | `d486c433eab69ba27b9089cd030a115c66c734f2668a1f6f02dccaccb46a2589` |
| `dem_slope_by_frontier_cohort.csv` | 2 KB | `6165c383b1037278cf5836146fa703bd73595db39e16556cf946f9946693bf3c` |
| `entry_origin_coverage.csv` | 1 KB | `486d66382d78182a434e225ef69e8558278bc12e29b49c966c167337bd6ca7fd` |
| `topographic_selection_bootstrap_draws.csv` | 2306 KB | `6d3ccd964c9b2ff030256cad70818400efec4927ac48919f554655e80d068581` |
| `topographic_selection_grid_long.csv` | 3004 KB | `a220a6a10eaf40a5de1b87f034d008d436a3c27e500f7f28dbece7ba08ea65df` |
| `topographic_selection_grid_wide.csv` | 788 KB | `8d3c364bc75193eaf19ce2fa68a993a1ee5e76c85a520d38aa47502f942f0931` |
| `topographic_selection_summary.csv` | 6 KB | `23d4fbbb549380be9ea0f54c7209542db6757e493b464fb63f02b27da8e1538c` |
| `topographic_selection_summary_with_bootstrap.csv` | 8 KB | `4098b10f11042897dc53f2664242f79986ecf274d40830307f622378f33f2caa` |

> `dem_frontier_all_schemas_25km.csv` é cópia idêntica de
> `dem_frontier_all_schemas.csv`, mantida com o buffer explícito no nome para
> que a resolução de caminhos do notebook não dependa do arquivo genérico.

### 2.2 Uso anterior — `outputs/next_analyses/previous_land_use/`

Base consolidada da grade de uso anterior: **299.922 linhas, 1.158 células,
1987–2023**. É o produto direto da consolidação das oito exportações do Google
Earth Engine e a rota canônica de entrada do módulo de uso anterior.

| Arquivo | Tamanho | SHA-256 |
|---|---|---|
| `previous_land_use_grid_year_long.csv` | 34.7 MB | `da338cbc8801919f9988b15b8d493534c9cf16cc547a365ba08fcb34a557ee9a` |

> **Insumo obrigatório da rota canônica.** Sem este arquivo, o módulo de uso
> anterior exige as oito exportações brutas do GEE
> (`previous_land_use_AAAA_AAAA.csv`), que não integram o pacote.

---

## 3. Fontes primárias — não redistribuídas

Dados abertos de instituições públicas, sujeitos às licenças de seus provedores.

| # | Órgão | Conteúdo | URL | Data de acesso | Versão / Coleção | SHA-256 | Licença |
|---|---|---|---|---|---|---|---|
| 1 | ANP | Certificação RenovaBio: NEEA e volume elegível por usina | https://www.gov.br/anp/ | 2026-07-28 | — | `PREENCHER` | Dados abertos governamentais |
| 2 | MapBiomas | Cobertura e uso do solo, série anual | https://brasil.mapbiomas.org/ | 2026-07-28 | **Coleção 10.1** | `PREENCHER` | CC BY-SA 4.0 |
| 3 | **INPE — TOPODATA** | Modelo digital de elevação e declividade | http://www.dsr.inpe.br/topodata/ | 2026-07-28 | Resolução 1 arco-segundo (~30 m) | `PREENCHER` | Dados abertos |
| 4 | IBGE | Malhas municipais | https://www.ibge.gov.br/geociencias/organizacao-do-territorio/malhas-territoriais.html | 2026-07-28 | Ano de referência 2022 | `PREENCHER` | Dados abertos governamentais |
| 5 | IBGE | PAM — Produção Agrícola Municipal | https://sidra.ibge.gov.br/tabela/1612 | 2026-07-28 | Tabela 1612 | `PREENCHER` | Dados abertos governamentais |
| 6 | IBGE / Atlas Brasil / IPEA / ANA / INPE | Covariáveis socioeconômicas de baseline (17 blocos) | ver `renovabio-art2-causal` | 2026-07-28 | Ano de referência 2017 | `cd1bbcb153b09b3af58e9e9d45d38ab6ce3530a656219aa47c3f674a181e1973` | Dados abertos |

### 3.1 Modelo digital de elevação — fonte não prevista no PGD original

O **TOPODATA/INPE** foi incorporado no curso da pesquisa e não constava do Plano
de Gestão de Dados aprovado. É um refinamento nacional dos dados SRTM, com
resolução de 1 arco-segundo (~30 m), distribuído em acesso aberto pelo Instituto
Nacional de Pesquisas Espaciais.

A incorporação está declarada como modificação do PGD na Seção 2.3 do Relatório
Científico Final do processo FAPESP nº 2025/01530-0.

| Campo | Valor |
|---|---|
| Produto | TOPODATA — Banco de Dados Geomorfométricos do Brasil |
| Provedor | INPE — Divisão de Sensoriamento Remoto |
| Resolução | 1 arco-segundo (~30 m) |
| Variável derivada | Declividade em classes: 0–3%, >3–6%, >6–9%, >9–12%, >12–20%, >20% |
| Folhas utilizadas | `PREENCHER` |
| Método de cálculo da declividade | `PREENCHER` (algoritmo, janela, unidade) |
| SHA-256 | `PREENCHER` |

Referência: Valeriano, M. M., & Rossetti, D. F. (2012). Topodata: Brazilian full
coverage refinement of SRTM data. *Applied Geography, 32*(2), 300–309.

### 3.2 Notas transversais

> **Share-alike.** O MapBiomas é licenciado em CC BY-SA 4.0. A cláusula de
> compartilhamento pela mesma licença pode alcançar produtos derivados — em
> especial as tabelas de uso anterior, que derivam diretamente da grade.
> Verificar a compatibilidade com a publicação sob CC BY 4.0.

> **Revisão retroativa.** MapBiomas e PAM revisam séries históricas entre
> edições. O registro da coleção e do hash é o que permite distinguir uma falha
> de replicação de uma atualização da fonte.

> **Base compartilhada.** O `base_psm_integrada_raw.csv` é o mesmo arquivo
> utilizado nos pacotes de replicação dos Artigos 1 e 2, com hash idêntico.

---

## 4. Fontes descartadas no curso da pesquisa

| Fonte | Situação |
|---|---|
| SICAR | Prevista no Plano de Gestão de Dados original. Não utilizada na versão final da análise, por inadequação da unidade de análise e por envolver dados pessoais de titulares de imóveis rurais (LGPD). |

---

## 5. Dados não versionados

| Item | Motivo |
|---|---|
| Oito exportações brutas do GEE (`previous_land_use_AAAA_AAAA.csv`) | Substituídas pela base consolidada da Seção 2.2, que é o produto direto delas |
| Pacote consolidado (`dist/*.zip`) | Gerado pela Seção 7.2 a cada execução |

