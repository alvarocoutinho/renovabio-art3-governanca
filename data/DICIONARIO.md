# Dicionário de dados

Documentação das variáveis conforme o Plano de Gestão de Dados aprovado
(FAPESP nº 2025/01530-0).

**Codificação:** UTF-8
**Formato:** CSV (separador `,`), Parquet, XLSX
**CRS das coordenadas de unidade:** `PREENCHER` — ver `configs/pipeline.yaml`
**CRS de análise (Moran/LISA):** EPSG:5880 (Brazil Polyconic)
**Recorte temporal:** 1985–2015 (fronteira); 1987–2015 (uso anterior)
**Unidade de análise:** usina certificada (CNPJ) e seu entorno territorial por buffer

---

## Chaves

| Chave | Tipo | Descrição |
|---|---|---|
| `PREENCHER` (CNPJ da unidade) | string | Identificador da usina — manter como string |
| `PREENCHER` (geocode) | string | Município IBGE 7 dígitos com `zfill` |
| `buffer_km` | int | Raio do entorno territorial: 25, 30, 50, 100, 150 |
| `schema` | string | Esquema temporal (`pre_renovabio_1985_2015`) |

---

## Camada `processed/` — base analítica canônica

### `painel_canonico_wide.csv` / `painel_canonico_wide_corrigido.csv`

| Variável | Tipo | Unidade | Descrição |
|---|---|---|---|
| `PREENCHER` | | | |

> Documentar a diferença entre `painel_canonico_wide` e a versão `_corrigido`:
> o que foi corrigido, quando, e qual das duas sustenta os resultados publicados.

### `area_cana_anos_chave.csv`
Área de cana em anos de referência.

| Variável | Tipo | Unidade | Descrição |
|---|---|---|---|
| `PREENCHER` | | ha | |

---

## Camada `interim/` — caches por buffer

### `dem_frontier_{buffer_km}km.csv`
Cruzamento fronteira × topografia por buffer.

| Variável | Tipo | Unidade | Descrição |
|---|---|---|---|
| `PREENCHER` (declividade) | float | `PREENCHER` (graus ou %) | Método de cálculo: `PREENCHER` |
| `PREENCHER` | | | |

### `previous_land_use_units_{buffer_km}km.csv`
Uso anterior das novas áreas de cana, por unidade e buffer.

| Variável | Tipo | Unidade | Descrição |
|---|---|---|---|
| `PREENCHER` (classe MapBiomas de origem) | int/string | — | Código da classe; incluir a legenda |
| `PREENCHER` | | ha ou % | |

### `overlay_frontier_merged_long_{buffer_km}km.csv`
Sobreposição da fronteira, formato longo.

### `frontier_temporal_robustness_all_schemas_{buffer_km}km.csv`
Robustez temporal por esquema e buffer.

### `previous_land_use_bootstrap_draws.parquet`
1500 réplicas de bootstrap. Regenerável com `origin_seed: 20260609`.

---

## Parâmetros de análise

| Parâmetro | Valor | Onde |
|---|---|---|
| Esquema temporal principal | `pre_renovabio_1985_2015` | `configs/pipeline.yaml` |
| Buffer principal | 30 km | idem |
| Buffer legado | 25 km | idem |
| Buffers de robustez | 25, 30, 50, 100, 150 km | idem |
| Janela do modelo de uso anterior | 1987–2015 | idem |
| Réplicas de bootstrap | 1500 | idem |
| Semente do bootstrap | 20260609 | idem |
| Vizinhos (KNN) | 6 | idem |
| KNN — robustez | 4, 6, 8, 10 | idem |
| Permutações Moran/LISA | 999 | idem |
| Semente espacial | `PREENCHER` | **a definir** — ver README |
| α do FDR | 0,05 | `configs/pipeline.yaml` |
| DPI das figuras | 600 | idem |

---

## Gerações institucionais

| Geração | Período | Marco de governança |
|---|---|---|
| `PREENCHER` | | |
| `PREENCHER` | | |

> Documentar o critério de corte entre gerações e sua âncora histórica. É a
> variável explicativa central do artigo — precisa ser justificada, não apenas
> declarada.

---

## Outputs — tabelas do manuscrito

| Arquivo | Conteúdo |
|---|---|
| `Tabela_1_estatisticas_descritivas_amostra_analitica.csv` | Descritivas da amostra |
| `Tabela_2_expansao_por_periodo.csv` | Expansão canavieira por período |
| `Tabela_3_selecao_topografica_por_periodo.csv` | Seleção topográfica por período |
| `Tabela_4_contrastes_topograficos_bootstrap.csv` | Contrastes topográficos (bootstrap) |
| `Tabela_5_uso_anterior_por_periodo.csv` | Uso anterior por período |
| `Tabela_6_contrastes_uso_anterior_bootstrap.csv` | Contrastes de uso anterior (bootstrap) |
| `Tabela_7_modelos_NEEA_vol.csv` | Modelos de ΔNEEA e Δvol% |
| `Tabela_8_efeito_fronteira_por_decada.csv` | Efeito fronteira por década |
| `Tabela_9_descritivas_por_geracao_institucional.csv` | Descritivas por geração |
| `Tabela_S2_robustez_buffers_modelos.csv` | Robustez a buffers (suplementar) |
| `Tabelas_artigo_AmbSoc_print_ready.xlsx` | Consolidado formatado |

Versões em inglês com sufixo `_en`; em português, `_ptbr`.

## Outputs — diagnósticos

| Arquivo | Conteúdo |
|---|---|
| `diagnostico_crs_coordenadas_usinas.csv` | CRS inferido e pontuação dos candidatos |
| `moran_global_residuos_NEEA.csv` | I de Moran global dos resíduos |
| `moran_global_residuos_NEEA_knn_robustness.csv` | Moran sob KNN 4/6/8/10 |
| `residuos_NEEA_com_LISA.csv` | Resíduos com classificação LISA |
| `lisa_residuos_NEEA_counts_knn_robustness.csv` | Contagens LISA por KNN |
| `anova_linear_vs_spline_NEEA.csv` | Comparação linear × spline |
| `diagnostico_influencia_delta_vol.csv` | Influência (Cook / leverage) |
| `robustez_delta_vol_outliers.csv` | Robustez a outliers |
| `entry_origin_coverage_corrected.csv` | Cobertura de origem de entrada |
| `output_manifest.csv` | Manifesto de saídas gerado pelo notebook |

## Outputs — registros de lacunas

| Arquivo | Conteúdo |
|---|---|
| `missing_buffer_caches.csv` | Caches de buffer ausentes na execução |
| `missing_approx_frontier_buffers.csv` | Buffers sem fronteira aproximada |
| `missing_unit_origin_buffers.csv` | Buffers sem origem por unidade |

> **Publicar estes três é essencial.** São o registro de quando o mecanismo de
> fallback foi acionado — a diferença entre um resultado exato e um aproximado.
