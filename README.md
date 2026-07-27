# Da fronteira mecanizada ao carbono certificado: governança histórica, seleção territorial e desempenho no RenovaBio

Pacote de replicação do manuscrito submetido a *Ambiente & Sociedade* (ANPPAS).

**Autor:** Alvaro Luz Alves Coutinho

**DOI:** `10.5281/zenodo.XXXXXXX` <!-- substituir pelo concept DOI após o primeiro release -->

**Versão do notebook:** v4.2 final

---

## Resumo

Análise histórico-institucional da expansão do setor sucroenergético, da seleção
territorial da fronteira canavieira e da relação entre trajetória de governança e
desempenho contemporâneo certificado no RenovaBio. Combina reconstrução da geografia
histórica da fronteira (1985–2015), seleção topográfica da expansão a partir de
modelo digital de elevação, uso anterior das novas áreas de cana e modelagem de
ΔNEEA e Δvol% por geração institucional, com diagnóstico de autocorrelação espacial
dos resíduos.

Saídas em **português e inglês** (`ptbr/` e `en/`).

---

## Estrutura

```
├── notebooks/
│   └── AmbSoc_RenovaBio_consolidado_local_multiescala_PTBR_EN_v4_2_final.ipynb
├── data/
│   ├── raw/          # fontes brutas — NÃO versionadas (ver MANIFEST.md)
│   ├── interim/      # caches de fronteira, topografia e uso anterior por buffer
│   ├── processed/    # base analítica canônica
│   └── DICIONARIO.md
├── configs/
│   └── pipeline.yaml # parâmetros CFG externalizados
├── outputs/
│   ├── tables/{ptbr,en}/
│   ├── figures/{ptbr,en}/
│   ├── diagnostics/
│   └── robustness/
└── requirements.txt
```

---

## Como reproduzir

1. Instale as dependências:

   ```bash
   python -m venv .venv && source .venv/bin/activate
   pip install -r requirements.txt
   ```

2. Obtenha os dados brutos conforme `data/raw/MANIFEST.md` e verifique os hashes.

3. Ajuste os caminhos em `configs/pipeline.yaml`.

4. Execute o notebook na ordem das seções 1 a 8.

| Seção | Conteúdo |
|---|---|
| 1 | Funções auxiliares e auditoria dos caches |
| 2 | Base analítica canônica |
| 3 | Geografia histórica da fronteira |
| 4 | Seleção topográfica da expansão |
| 5 | Uso anterior das novas áreas de cana (5.4 robustez de buffers; 5.5 fronteira aproximada) |
| 6 | Geração histórica da fronteira, ΔNEEA e Δvol% (6.2 robustez de escala) |
| 7 | Tabelas consolidadas e pacote final |
| 8 | Leitura rápida esperada |

---

## ⚠️ Requisitos críticos de reprodutibilidade

Três pontos precisam ser resolvidos **antes** de considerar este pacote replicável.
Estão listados aqui em vez de escondidos porque afetam os números publicados.

### 1. Fixar o CRS das coordenadas das usinas

O notebook **infere** o sistema de coordenadas por pontuação entre candidatos
(`EPSG:31981`–`31986`, `3857`, `5880`). Todos os buffers, a geografia da fronteira e
o diagnóstico Moran/LISA dependem dessa escolha. Defina explicitamente:

```yaml
unit_coord_crs: "EPSG:PREENCHER"   # em configs/pipeline.yaml
```

O diagnóstico `diagnostico_crs_coordenadas_usinas.csv` registra qual CRS foi usado —
confira antes de fixar.

### 2. Desativar o fallback de buffers

Com `fallback_to_available_buffer: true`, o notebook substitui caches ausentes por
aproximação construída a partir da grade de origem. Isso faz o resultado depender de
**quais caches existem no disco**, o que é incompatível com replicação por terceiros.

Para o pacote publicado, use `fallback_to_available_buffer: false` e distribua os
caches exatos (ver seção Dados abaixo), ou documente explicitamente que os resultados
publicados usaram aproximação, indicando quais buffers.

Os arquivos `missing_buffer_caches.csv`, `missing_approx_frontier_buffers.csv` e
`missing_unit_origin_buffers.csv` em `outputs/diagnostics/` registram o que faltou em
cada execução. **Publique-os.**

### 3. Semear a inferência por permutação

`origin_seed: 20260609` governa apenas o bootstrap de uso anterior
(`n_boot_origin: 1500`). As `permutations: 999` do Moran global e do LISA usam o RNG
interno do `esda`, **não semeado** — os p-valores variam entre execuções.

```python
np.random.seed(CFG['spatial_seed'])   # antes de instanciar Moran / Moran_Local
```

Defina `spatial_seed` em `configs/pipeline.yaml` e reexecute a Seção 6 antes do
release, para que os valores publicados sejam os do pacote.

---

## Dados

Dados derivados em `data/interim/`, `data/processed/` e `outputs/` são produto desta
pesquisa, sob licença CC BY 4.0.

Dados brutos de terceiros **não são redistribuídos** — ver `data/raw/MANIFEST.md`.

### Caches intermediários

Os caches por buffer (`dem_frontier_{b}km.csv`, `previous_land_use_units_{b}km.csv`,
`overlay_frontier_merged_long_{b}km.csv`, `frontier_temporal_robustness_*`) são
custosos de regerar e determinam se o fallback é acionado. Recomendação: **depositar
os caches do buffer principal (30 km) e dos buffers de robustez (25/50/100/150 km)
como registro `Dataset` no Zenodo**, referenciado pelo DOI no `MANIFEST.md`.

O `previous_land_use_bootstrap_draws.parquet` (1500 réplicas) pode ficar de fora se o
volume pesar — é regenerável a partir da semente declarada.

---

## Licenciamento

- **Código** (`notebooks/`): MIT — ver `LICENSE`
- **Dados derivados, tabelas e figuras**: CC BY 4.0 — ver `LICENSE-DATA`

## Como citar

Ver `CITATION.cff` ou o botão *Cite this repository* no GitHub.

## Financiamento

This study was financed, in part, by the São Paulo Research Foundation (FAPESP),
Brazil. Process Number #2025/01530-0.
