# Dicionário de dados

Conforme o Plano de Gestão de Dados aprovado (FAPESP nº 2025/01530-0).

| Campo | Valor |
|---|---|
| Codificação | UTF-8 (CSVs com BOM — `utf-8-sig`) |
| Formatos | CSV, GeoJSON, XLSX, PNG, SVG |
| CRS das coordenadas de usina | **EPSG:5880** — SIRGAS 2000 / Brazil Polyconic |
| CRS de análise | EPSG:5880 |
| CRS das geometrias municipais | EPSG:4674 — SIRGAS 2000 |
| Unidade de análise | Usina certificada (CNPJ) e entorno territorial |
| Escala principal | Buffer de 25 km |

---

## Chaves

| Chave | Tipo | Descrição |
|---|---|---|
| `cnpj_clean` | string | CNPJ normalizado da usina — manter como string |
| `grid_id_used` | string | Identificador da célula da grade espacial do GEE |
| `buffer_km` | int | Raio do entorno: 25, 30, 50, 100 ou 150 |
| `schema` | string | Esquema temporal da fronteira |
| `period` | string | Período institucional |

## Períodos institucionais

| Código | Português | Inglês | Anos |
|---|---|---|---|
| `pre_law_1987_2001` | Pré-Lei, 1987–2001 | Pre-law period | 15 |
| `post_law_pre_zae_2002_2008` | Pós-Lei / pré-ZAE, 2002–2008 | Post-law / pre-ZAE | 7 |
| `zae_2009_2019` | Vigência do ZAE-Cana, 2009–2019 | ZAE-Cana period | 11 |
| `post_zae_2020_2023` | Pós-revogação, 2020–2023 | Post-revocation | 4 |

> A periodização ancora-se em marcos de governança do setor: a legislação
> setorial, a vigência do Zoneamento Agroecológico da Cana-de-Açúcar (ZAE-Cana)
> e sua revogação. É a variável explicativa central do artigo.

## Classes de uso anterior (MapBiomas)

| Código | Português | Inglês |
|---|---|---|
| 1 | Pastagem | Pasture |
| 2 | Agricultura anual | Annual agriculture |
| 3 | Agricultura perene | Perennial agriculture |
| 4 | Mosaico agropecuário | Agricultural mosaic |
| 5 | Silvicultura | Forestry |
| 6 | Vegetação nativa | Native vegetation |
| 7 | Outros usos | Other uses |

## Classes de declividade (TOPODATA/INPE)

| Código | Faixa |
|---|---|
| `00_03` | 0–3% |
| `03_06` | >3–6% |
| `06_09` | >6–9% |
| `09_12` | >9–12% |
| `12_20` | >12–20% |
| `gt_20` | >20% |

O limiar de 12% corresponde ao patamar de mecanização plena da colheita, usado
na variável `share_cane_slope_le12`.

## Períodos da fronteira

| Código | Português | Inglês |
|---|---|---|
| `frontier_until_2001` | Até 2001 | Until 2001 |
| `frontier_2002_2008` | 2002–2008 | 2002–2008 |
| `frontier_2009_2015` | 2009–2015 | 2009–2015 |
| `frontier_after_2015` | Depois de 2015 | After 2015 |

---

## 1. Insumos — `data/processed/`


### Arquivos tabulares

Estrutura de colunas dos insumos verificados pelo Gate 0.

| Arquivo | Dim. | Colunas |
|---|---|---|
| `painel_canonico_wide.csv` | 230×33 | `cnpj_clean`, `emissor`, `cidade`, `uf`, `rota`, `rota_tag`, `cohort`, `X`, `Y`, `neea_t0_2022`, `neea_t1_2023`, `neea_t2_2026` … (+21) |
| `crosswalk_centrosul.csv` | 2363×5 | `geocode`, `municipio`, `uf`, `cidade_uf_seeg`, `muni_key` |
| `mill_confounder_base.csv` | 182×35 | `cd_ibge`, `uf`, `nm_mun`, `n_usinas`, `neea_2022`, `neea_2023`, `neea_2025`, `vol_2022`, `vol_2023`, `vol_2025`, `log_pib_pc`, `log_pop` … (+23) |
| `base_psm_integrada_raw.csv` | 5570×165 | `0_ano_ref`, `0_cd_reg`, `0_nm_reg`, `0_cd_uf`, `0_sg_uf`, `0_nm_if`, `0_cd_ibge`, `0_nm_mun`, `0_amzon_legal`, `0_semiarido`, `1_vadc_agro`, `1_vadc_ind` … (+153) |

### `centro_sul_munis.geojson`

Geometrias municipais do recorte Centro-Sul, em EPSG:4674. Usado nos mapas das
seções 3 e 4. `PREENCHER`: documentar as colunas de atributo.

---

## 2. Tabelas do manuscrito — `outputs/tables/`


### Tabelas numeradas

| Arquivo | Dim. | Colunas |
|---|---|---|
| `Tabela_1_estatisticas_descritivas_amostra_analitica.csv` | 10×10 | `analytic_sample`, `variable`, `n`, `mean`, `std`, `min`, `p25`, `median`, `p75`, `max` |
| `Tabela_2_expansao_por_periodo.csv` | 4×7 | `period`, `period_label`, `all_persistent_entry_ha`, `entry_from_productive_land_ha`, `productive_origin_share`, `years_in_period`, `annual_entry_ha` |
| `Tabela_3_selecao_topografica_por_periodo.csv` | 24×14 | `period`, `period_label`, `slope_bin`, `slope_label`, `selected_area_ha`, `exposure_ha_years`, `grids_with_entry`, `grids_with_exposure`, `selected_total_period`, `exposure_total_period`, `selected_share`, `available_share` … (+2) |
| `Tabela_4_contrastes_topograficos_bootstrap.csv` | 18×18 | `earlier_period`, `later_period`, `contrast`, `slope_bin`, `slope_label`, `selection_ratio_earlier`, `selection_ratio_later`, `ratio_of_selection_ratios`, `log2_difference`, `log2_ci_low`, `log2_boot_median`, `log2_ci_high` … (+6) |
| `Tabela_5_uso_anterior_por_periodo.csv` | 32×15 | `period`, `period_label`, `period_label_en`, `origin_code`, `origin_label`, `origin_label_en`, `entry_area_ha`, `mapped_origin_area_ha`, `period_entry_total_ha`, `unclassified_gap_ha`, `origin_share`, `mapped_origin_share` … (+3) |
| `Tabela_6_contrastes_uso_anterior_bootstrap.csv` | 21×12 | `contrast`, `contrast_en`, `origin_code`, `origin_label`, `origin_label_en`, `difference_pp`, `difference_pp_ci_low`, `difference_pp_ci_high`, `ratio_of_shares`, `ratio_ci_low`, `ratio_ci_high`, `difference_supported` |
| `Tabela_7_modelos_NEEA_vol.csv` | 74×14 | `outcome`, `model`, `term`, `coef`, `std_err_hc3`, `p_value`, `ci_low`, `ci_high`, `n`, `r2`, `r2_adj`, `aic` … (+2) |
| `Tabela_8_efeito_fronteira_por_decada.csv` | 10×17 | `outcome`, `model`, `term`, `coef`, `std_err_hc3`, `p_value`, `ci_low`, `ci_high`, `n`, `r2`, `r2_adj`, `aic` … (+5) |
| `Tabela_9_descritivas_por_geracao_institucional.csv` | 6×10 | `outcome`, `frontier_period_generation`, `n`, `frontier_mean`, `delta_neea_mean`, `delta_neea_median`, `delta_vol_mean`, `delta_vol_median`, `frontier_generation_label_ptbr`, `frontier_generation_label_en` |

### Bases e testes de apoio

| Arquivo | Dim. | Colunas |
|---|---|---|
| `overlay_frontier_merged_long.csv` | 1356×76 | `outcome`, `overlay_cluster`, `overlay_lisa_class`, `cell_count`, `cnpj_clean`, `emissor_x`, `cidade_x`, `uf_x`, `rota`, `rota_tag`, `cohort`, `X` … (+64) |
| `area_cana_anos_chave.csv` | 5×3 | `year`, `area_ha_approx`, `area_mha_approx` |
| `frontier_overlay_group_tests.csv` | 18×10 | `outcome`, `schema`, `group_var`, `anova_f`, `anova_p`, `eta_squared`, `omega_squared`, `kruskal_h`, `kruskal_p`, `n` |
| `frontier_performance_model_samples_25km.csv` | 24×8 | `schema`, `model`, `outcome`, `n_available`, `missing_outcome`, `missing_initial`, `missing_frontier`, `missing_cane_area` |
| `frontier_temporal_robustness_all_schemas_25km.csv` | 684×32 | `area_1985`, `area_1995`, `area_2005`, `area_2015`, `area_2024`, `cidade`, `cnpj_clean`, `emissor`, `uf`, `cane_area_total_ha`, `mean_first_key_year`, `dominant_first_key_year` … (+20) |
| `frontier_temporal_robustness_cluster_tests_25km.csv` | 9×9 | `schema`, `test`, `statistic`, `p_value`, `effect_size`, `effect_size_corrected`, `n`, `eta_squared`, `omega_squared` |
| `frontier_temporal_robustness_descriptives_25km.csv` | 3×10 | `schema`, `n_total`, `n_with_frontier`, `mean_first_key_year_mean`, `mean_first_key_year_median`, `cane_area_total_ha_mean`, `share_old_le_1995_mean`, `share_mid_2000_2010_mean`, `share_recent_ge_2015_mean`, `share_recent_ge_2010_mean` |
| `frontier_temporal_robustness_frontier_effects_only_25km.csv` | 24×14 | `schema`, `model`, `outcome`, `variable`, `coef`, `std_err`, `p_value`, `ci_low`, `ci_high`, `n`, `r2`, `adj_r2` … (+2) |
| `frontier_temporal_robustness_performance_models_25km.csv` | 96×14 | `schema`, `model`, `outcome`, `variable`, `coef`, `std_err`, `p_value`, `ci_low`, `ci_high`, `n`, `r2`, `adj_r2` … (+2) |
| `nested_model_central_comparisons.csv` | 18×12 | `outcome`, `schema`, `comparison`, `r2_reduced`, `r2_full`, `delta_r2`, `f_statistic`, `p_value`, `df_diff`, `delta_aic`, `delta_bic`, `n` |
| `robustez_vol_pct_outliers.csv` | 20×10 | `model`, `variable`, `coef`, `std_err`, `p_value`, `ci_low`, `ci_high`, `n`, `r2`, `adj_r2` |

### Versões bilíngues

`outputs/tables/ptbr/` e `outputs/tables/en/` contêm as tabelas 5, 6 e S2 com
rótulos traduzidos. `Tabelas_artigo_AmbSoc_print_ready.xlsx`, na raiz de
`outputs/`, consolida as tabelas formatadas.

---

## 3. Diagnósticos — `outputs/diagnostics/`


### CRS e autocorrelação espacial

| Arquivo | Dim. | Colunas |
|---|---|---|
| `diagnostico_crs_coordenadas_usinas.csv` | 1×8 | `x_col`, `y_col`, `x_min`, `x_max`, `y_min`, `y_max`, `crs_used`, `n_units` |
| `moran_global_residuos_NEEA.csv` | 1×9 | `model`, `n`, `k_neighbors`, `moran_i`, `expected_i`, `p_permutation`, `z_sim`, `permutations`, `is_main_knn` |
| `moran_global_residuos_NEEA_knn_robustness.csv` | 4×9 | `model`, `n`, `k_neighbors`, `moran_i`, `expected_i`, `p_permutation`, `z_sim`, `permutations`, `is_main_knn` |
| `residuos_NEEA_com_LISA.csv` | 225×110 | `outcome`, `overlay_cluster`, `overlay_lisa_class`, `cell_count`, `cnpj_clean`, `emissor_x`, `cidade_x`, `uf_x`, `rota`, `rota_tag`, `cohort`, `X` … (+98) |
| `residuos_NEEA_com_LISA_knn6.csv` | 225×110 | `outcome`, `overlay_cluster`, `overlay_lisa_class`, `cell_count`, `cnpj_clean`, `emissor_x`, `cidade_x`, `uf_x`, `rota`, `rota_tag`, `cohort`, `X` … (+98) |
| `lisa_residuos_NEEA_counts_knn_robustness.csv` | 4×5 | `lisa_residual_class`, `count`, `k_neighbors`, `model`, `is_main_knn` |

### Forma funcional, influência e robustez

| Arquivo | Dim. | Colunas |
|---|---|---|
| `anova_linear_vs_spline_NEEA.csv` | 2×7 | `Unnamed: 0`, `df_resid`, `ssr`, `df_diff`, `ss_diff`, `F`, `Pr(>F)` |
| `comparacao_linear_categorias_spline_NEEA.csv` | 3×8 | `model`, `AIC`, `BIC`, `R2`, `R2_adj`, `n`, `delta_AIC`, `delta_BIC` |
| `comparacao_linear_categorias_institucionais_spline_NEEA.csv` | 3×8 | `model`, `AIC`, `BIC`, `R2`, `R2_adj`, `n`, `delta_AIC`, `delta_BIC` |
| `tendencias_segmentadas_coeficientes.csv` | 30×13 | `slope_bin`, `slope_label`, `variable`, `coef`, `std_err`, `p_value`, `ci_low`, `ci_high`, `n`, `r2`, `adj_r2`, `aic` … (+1) |
| `tendencias_segmentadas_inclinacoes.csv` | 24×4 | `slope_bin`, `slope_label`, `segment`, `annual_log2_slope` |
| `amostras_modelos_NEEA_vol.csv` | 10×5 | `outcome`, `model`, `n`, `formula`, `buffer_km` |
| `diagnostico_influencia_delta_vol.csv` | 221×107 | `outcome`, `overlay_cluster`, `overlay_lisa_class`, `cell_count`, `cnpj_clean`, `emissor_x`, `cidade_x`, `uf_x`, `rota`, `rota_tag`, `cohort`, `X` … (+95) |
| `robustez_delta_vol_outliers.csv` | 49×9 | `model`, `term`, `coef`, `std_err`, `p_value`, `ci_low`, `ci_high`, `n`, `r2` |
| `estoque_inicial_declividade.csv` | 6×6 | `period`, `period_label`, `slope_bin`, `slope_label`, `stock_area_ha`, `stock_share` |
| `descritivas_por_geracao_legacy_1995_2005.csv` | 6×6 | `outcome`, `frontier_generation_legacy`, `n`, `frontier_mean`, `delta_neea_mean`, `delta_vol_mean` |

---

## 4. Robustez — `outputs/robustness/`


### Multiescala

O `missing_buffer_caches.csv` registra os buffers sem cache exato — 30, 50, 100
e 150 km. Publicar esse registro é o que permite distinguir uma estimativa exata
de uma aproximada. A coluna `frontier_source_method` nas tabelas de robustez
identifica o método usado em cada linha.

| Arquivo | Dim. | Colunas |
|---|---|---|
| `robustez_buffers_modelos_fronteira.csv` | 22×20 | `buffer_km`, `outcome`, `model`, `n`, `coef_frontier_year`, `std_err_frontier_year`, `p_value_frontier_year`, `ci_low_frontier_year`, `ci_high_frontier_year`, `effect_per_decade`, `ci_low_per_decade`, `ci_high_per_decade` … (+8) |
| `frontier_approx_from_origin_grid_all_buffers.csv` | 912×12 | `cnpj_clean`, `cane_area_total_ha`, `mean_first_key_year`, `first_entry_year`, `last_entry_year`, `n_entry_years`, `schema`, `buffer_km`, `frontier_source_method`, `frontier_year_window`, `frontier_area_definition`, `cane_area_total_ha_approx_from_origin` |
| `previous_land_use_units_all_buffers.csv` | 1140×20 | `cnpj_clean`, `buffer_km`, `prior_entry_area_total_ha`, `prior_pasture_area_ha`, `prior_pasture_share`, `prior_annual_agriculture_area_ha`, `prior_annual_agriculture_share`, `prior_perennial_agriculture_area_ha`, `prior_perennial_agriculture_share`, `prior_agricultural_mosaic_area_ha`, `prior_agricultural_mosaic_share`, `prior_forestry_area_ha` … (+8) |
| `missing_buffer_caches.csv` | 4×2 | `buffer_km`, `reason` |

---

## 5. Seleção topográfica — `outputs/dem_selection/`


### Cruzamento fronteira × declividade

| Arquivo | Dim. | Colunas |
|---|---|---|
| `dem_frontier_10y_1985_2024_25km.csv` | 228×37 | `cidade`, `cnpj_clean`, `dem_area_1985`, `dem_area_1995`, `dem_area_2005`, `dem_area_2015`, `dem_area_2024`, `dem_area_le12_1985`, `dem_area_le12_1995`, `dem_area_le12_2005`, `dem_area_le12_2015`, `dem_area_le12_2024` … (+25) |
| `dem_frontier_5y_1985_2024_25km.csv` | 228×57 | `cidade`, `cnpj_clean`, `dem_area_1985`, `dem_area_1990`, `dem_area_1995`, `dem_area_2000`, `dem_area_2005`, `dem_area_2010`, `dem_area_2015`, `dem_area_2020`, `dem_area_2024`, `dem_area_le12_1985` … (+45) |
| `dem_frontier_all_schemas.csv` | 684×57 | `cidade`, `cnpj_clean`, `dem_area_1985`, `dem_area_1995`, `dem_area_2005`, `dem_area_2015`, `dem_area_2024`, `dem_area_le12_1985`, `dem_area_le12_1995`, `dem_area_le12_2005`, `dem_area_le12_2015`, `dem_area_le12_2024` … (+45) |
| `dem_frontier_all_schemas_25km.csv` | 684×57 | `cidade`, `cnpj_clean`, `dem_area_1985`, `dem_area_1995`, `dem_area_2005`, `dem_area_2015`, `dem_area_2024`, `dem_area_le12_1985`, `dem_area_le12_1995`, `dem_area_le12_2005`, `dem_area_le12_2015`, `dem_area_le12_2024` … (+45) |
| `dem_frontier_neea_models.csv` | 90×13 | `schema`, `model`, `variable`, `coef`, `std_err`, `p_value`, `ci_low`, `ci_high`, `n`, `r2`, `adj_r2`, `aic` … (+1) |
| `dem_frontier_pre_renovabio_1985_2015_25km.csv` | 228×47 | `cidade`, `cnpj_clean`, `dem_area_1985`, `dem_area_1990`, `dem_area_1995`, `dem_area_2000`, `dem_area_2005`, `dem_area_2010`, `dem_area_2015`, `dem_area_le12_1985`, `dem_area_le12_1990`, `dem_area_le12_1995` … (+35) |
| `dem_slope_by_frontier_cohort.csv` | 21×6 | `schema`, `first_key_year`, `buffer_area_ha_sum`, `share_slope_le12_weighted`, `mean_slope_pct_weighted`, `n_buffers_with_cohort` |
| `entry_origin_coverage.csv` | 4×5 | `period`, `period_label`, `all_persistent_entry_ha`, `entry_from_productive_land_ha`, `productive_origin_share` |
| `topographic_selection_bootstrap_draws.csv` | 48000×4 | `iteration`, `period`, `slope_bin`, `selection_ratio` |
| `topographic_selection_grid_long.csv` | 25703×8 | `grid_id`, `grid_area_km2`, `period`, `period_label`, `slope_bin`, `slope_label`, `selected_area_ha`, `exposure_ha_years` |
| `topographic_selection_grid_wide.csv` | 1158×58 | `entry_all__post_law_pre_zae_2002_2008`, `entry_all__post_zae_2020_2023`, `entry_all__pre_law_1986_2001`, `entry_all__zae_2009_2019`, `entry_productive__post_law_pre_zae_2002_2008`, `entry_productive__post_zae_2020_2023`, `entry_productive__pre_law_1986_2001`, `entry_productive__zae_2009_2019`, `exp__post_law_pre_zae_2002_2008__00_03`, `exp__post_law_pre_zae_2002_2008__03_06`, `exp__post_law_pre_zae_2002_2008__06_09`, `exp__post_law_pre_zae_2002_2008__09_12` … (+46) |
| `topographic_selection_summary.csv` | 24×15 | `period`, `period_label`, `slope_bin`, `slope_label`, `selected_area_ha`, `exposure_ha_years`, `grids_with_exposure`, `grids_with_entry`, `selected_total_period`, `exposure_total_period`, `selected_share`, `available_share` … (+3) |
| `topographic_selection_summary_with_bootstrap.csv` | 24×20 | `period`, `period_label`, `slope_bin`, `slope_label`, `selected_area_ha`, `exposure_ha_years`, `grids_with_exposure`, `grids_with_entry`, `selected_total_period`, `exposure_total_period`, `selected_share`, `available_share` … (+8) |

### Seleção anual corrigida

Subpasta `corrected_annual_selection/`.

| Arquivo | Dim. | Colunas |
|---|---|---|
| `annual_grid_block_1987_1991.csv` | 1158×79 | `grid_id`, `grid_area_km2`, `entry_all__1987`, `entry_all__1988`, `entry_all__1989`, `entry_all__1990`, `entry_all__1991`, `entry_productive__1987`, `entry_productive__1988`, `entry_productive__1989`, `entry_productive__1990`, `entry_productive__1991` … (+67) |
| `annual_grid_block_1992_1996.csv` | 1158×72 | `grid_id`, `grid_area_km2`, `entry_all__1992`, `entry_all__1993`, `entry_all__1994`, `entry_all__1995`, `entry_all__1996`, `entry_productive__1992`, `entry_productive__1993`, `entry_productive__1994`, `entry_productive__1995`, `entry_productive__1996` … (+60) |
| `annual_grid_block_1997_2001.csv` | 1158×72 | `grid_id`, `grid_area_km2`, `entry_all__1997`, `entry_all__1998`, `entry_all__1999`, `entry_all__2000`, `entry_all__2001`, `entry_productive__1997`, `entry_productive__1998`, `entry_productive__1999`, `entry_productive__2000`, `entry_productive__2001` … (+60) |
| `annual_grid_block_2002_2006.csv` | 1158×72 | `grid_id`, `grid_area_km2`, `entry_all__2002`, `entry_all__2003`, `entry_all__2004`, `entry_all__2005`, `entry_all__2006`, `entry_productive__2002`, `entry_productive__2003`, `entry_productive__2004`, `entry_productive__2005`, `entry_productive__2006` … (+60) |
| `annual_grid_block_2007_2011.csv` | 1158×72 | `grid_id`, `grid_area_km2`, `entry_all__2007`, `entry_all__2008`, `entry_all__2009`, `entry_all__2010`, `entry_all__2011`, `entry_productive__2007`, `entry_productive__2008`, `entry_productive__2009`, `entry_productive__2010`, `entry_productive__2011` … (+60) |
| `annual_grid_block_2012_2016.csv` | 1158×72 | `grid_id`, `grid_area_km2`, `entry_all__2012`, `entry_all__2013`, `entry_all__2014`, `entry_all__2015`, `entry_all__2016`, `entry_productive__2012`, `entry_productive__2013`, `entry_productive__2014`, `entry_productive__2015`, `entry_productive__2016` … (+60) |
| `annual_grid_block_2017_2021.csv` | 1158×72 | `grid_id`, `grid_area_km2`, `entry_all__2017`, `entry_all__2018`, `entry_all__2019`, `entry_all__2020`, `entry_all__2021`, `entry_productive__2017`, `entry_productive__2018`, `entry_productive__2019`, `entry_productive__2020`, `entry_productive__2021` … (+60) |
| `annual_grid_block_2022_2023.csv` | 1158×30 | `grid_id`, `grid_area_km2`, `entry_all__2022`, `entry_all__2023`, `entry_productive__2022`, `entry_productive__2023`, `exp__2022__00_03`, `exp__2022__03_06`, `exp__2022__06_09`, `exp__2022__09_12`, `exp__2022__12_20`, `exp__2022__gt_20` … (+18) |
| `annual_segmented_regression_coefficients.csv` | 30×13 | `slope_bin`, `slope_label`, `variable`, `coef`, `std_err`, `p_value`, `ci_low`, `ci_high`, `n`, `r2`, `adj_r2`, `aic` … (+1) |
| `annual_segmented_regression_implied_slopes.csv` | 24×4 | `slope_bin`, `slope_label`, `segment`, `annual_log2_slope` |
| `annual_topographic_selection_grid_long.csv` | 257076×10 | `grid_id`, `year`, `period`, `period_label`, `slope_bin`, `slope_label`, `selected_area_ha`, `exposure_ha_years`, `entry_all_ha`, `entry_productive_ha` |
| `annual_topographic_selection_grid_wide.csv` | 1158×527 | `grid_id`, `grid_area_km2`, `entry_all__1987`, `entry_all__1988`, `entry_all__1989`, `entry_all__1990`, `entry_all__1991`, `entry_productive__1987`, `entry_productive__1988`, `entry_productive__1989`, `entry_productive__1990`, `entry_productive__1991` … (+515) |

---

## 6. Uso anterior — `outputs/next_analyses/`


### Grade e unidades

| Arquivo | Dim. | Colunas |
|---|---|---|
| `frontier_neea_category_descriptives.csv` | 3×6 | `frontier_period`, `n`, `frontier_mean`, `delta_neea_mean`, `delta_neea_median`, `delta_neea_sd` |
| `frontier_neea_category_model_coefficients.csv` | 12×6 | `term`, `coef`, `std_err_hc3`, `p_value_hc3`, `ci_low_hc3`, `ci_high_hc3` |
| `frontier_neea_linear_vs_spline_anova.csv` | 2×7 | `Unnamed: 0`, `df_resid`, `ssr`, `df_diff`, `ss_diff`, `F`, `Pr(>F)` |
| `frontier_neea_model_comparison.csv` | 3×8 | `model`, `AIC`, `BIC`, `R2`, `R2_adj`, `n`, `delta_AIC`, `delta_BIC` |
| `frontier_neea_spline_predictions.csv` | 200×8 | `mean_first_key_year`, `neea_first`, `_log_scale`, `share_cane_slope_le12`, `uf_x`, `predicted_delta_neea`, `ci_low`, `ci_high` |
| `neea_residual_extreme_cases.csv` | 30×9 | `cnpj_clean`, `uf_x`, `delta_neea`, `mean_first_key_year`, `neea_fitted`, `neea_residual`, `residual_z`, `lisa_residual_class`, `local_p_fdr` |
| `neea_residual_model_coefficients.csv` | 10×6 | `term`, `coef`, `std_err_hc3`, `p_value`, `ci_low`, `ci_high` |
| `neea_residual_moran_global.csv` | 1×7 | `n`, `k_neighbors`, `moran_i`, `expected_i`, `p_permutation`, `z_sim`, `permutations` |
| `neea_residuals_with_lisa.csv` | 225×87 | `outcome`, `overlay_cluster`, `overlay_lisa_class`, `cell_count`, `cnpj_clean`, `emissor_x`, `cidade_x`, `uf_x`, `rota`, `rota_tag`, `cohort`, `X` … (+75) |
| `frontier_temporal_robustness_all_schemas_100km_approx_from_origin_grid.csv` | 228×12 | `cnpj_clean`, `cane_area_total_ha`, `mean_first_key_year`, `first_entry_year`, `last_entry_year`, `n_entry_years`, `schema`, `buffer_km`, `frontier_source_method`, `frontier_year_window`, `frontier_area_definition`, `cane_area_total_ha_approx_from_origin` |
| `frontier_temporal_robustness_all_schemas_150km_approx_from_origin_grid.csv` | 228×12 | `cnpj_clean`, `cane_area_total_ha`, `mean_first_key_year`, `first_entry_year`, `last_entry_year`, `n_entry_years`, `schema`, `buffer_km`, `frontier_source_method`, `frontier_year_window`, `frontier_area_definition`, `cane_area_total_ha_approx_from_origin` |
| `frontier_temporal_robustness_all_schemas_30km_approx_from_origin_grid.csv` | 228×12 | `cnpj_clean`, `cane_area_total_ha`, `mean_first_key_year`, `first_entry_year`, `last_entry_year`, `n_entry_years`, `schema`, `buffer_km`, `frontier_source_method`, `frontier_year_window`, `frontier_area_definition`, `cane_area_total_ha_approx_from_origin` |
| `frontier_temporal_robustness_all_schemas_50km_approx_from_origin_grid.csv` | 228×12 | `cnpj_clean`, `cane_area_total_ha`, `mean_first_key_year`, `first_entry_year`, `last_entry_year`, `n_entry_years`, `schema`, `buffer_km`, `frontier_source_method`, `frontier_year_window`, `frontier_area_definition`, `cane_area_total_ha_approx_from_origin` |
| `overlay_frontier_merged_long_100km_approx_from_origin_grid.csv` | 456×27 | `cnpj_clean`, `emissor`, `cidade`, `uf`, `rota`, `rota_tag`, `cohort`, `X`, `Y`, `neea_first`, `neea_t2_2026`, `delta_neea` … (+15) |
| `overlay_frontier_merged_long_150km_approx_from_origin_grid.csv` | 456×27 | `cnpj_clean`, `emissor`, `cidade`, `uf`, `rota`, `rota_tag`, `cohort`, `X`, `Y`, `neea_first`, `neea_t2_2026`, `delta_neea` … (+15) |
| `overlay_frontier_merged_long_30km_approx_from_origin_grid.csv` | 456×27 | `cnpj_clean`, `emissor`, `cidade`, `uf`, `rota`, `rota_tag`, `cohort`, `X`, `Y`, `neea_first`, `neea_t2_2026`, `delta_neea` … (+15) |

---

## 7. Parâmetros de análise

Fonte canônica: `pipeline.yaml`.

| Parâmetro | Valor |
|---|---|
| Esquema temporal principal | `pre_renovabio_1985_2015` |
| Buffer principal | 25 km |
| Buffers de robustez | 30, 50, 100, 150 km (aproximados) |
| Substituição do buffer principal | desativada (`false`) |
| Aproximação a partir da grade de origem | ativa (`true`) |
| Janela do modelo de uso anterior | 1987–2015 |
| Réplicas de bootstrap | 1.500 |
| Semente do bootstrap | 20260609 |
| Semente da inferência por permutação | 20260609 |
| Vizinhos (KNN) — principal | 6 |
| KNN — robustez | 4, 6, 8, 10 |
| Permutações (Moran, LISA) | 999 |
| α do FDR | 0,05 |
| DPI das figuras | 600 |

## 8. Resultado canônico do diagnóstico espacial

```
Moran's I = 0,026296  |  E[I] = −0,004464  |  p = 0,171  |  z = 0,94506
Modelo M5 (+ uso anterior)  |  n = 225  |  k = 6  |  999 permutações
```

