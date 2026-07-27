# Manifesto de dados brutos

Este diretório **não contém dados**. As fontes primárias não são redistribuídas
neste repositório; permanecem sujeitas às licenças e termos de uso de seus
provedores originais.

O hash SHA-256 é o que garante que você obteve exatamente o arquivo que gerou os
resultados publicados. Divergência indica revisão da fonte desde o acesso original.

## Como gerar e verificar

```bash
find data/raw -type f -exec sha256sum {} \; > hashes.txt
```

```powershell
Get-ChildItem -Recurse data\raw -File | Get-FileHash -Algorithm SHA256 |
  Select-Object Hash, Path | Export-Csv hashes.csv -NoTypeInformation
```

---

## Fontes

### 1. ANP — certificação e coordenadas das usinas

| Arquivo | URL | Data de acesso | Versão | SHA-256 | Licença |
|---|---|---|---|---|---|
| `PREENCHER` (NEEA / volume elegível) | https://www.gov.br/anp/ | AAAA-MM-DD | — | `PREENCHER` | Dados abertos governamentais |
| `PREENCHER` (cadastro de unidades / CNPJ / coordenadas) | | AAAA-MM-DD | | `PREENCHER` | |

> **Crítico — CRS.** As coordenadas das usinas são o insumo mais sensível deste
> pipeline. Registrar aqui o **sistema de coordenadas declarado pela fonte**, não o
> inferido pelo notebook. Ver `configs/pipeline.yaml → crs.unit_coord_crs`.

### 2. MapBiomas — cobertura e uso do solo

Base da reconstrução da fronteira e da análise de uso anterior das novas áreas de cana.

| Arquivo | URL | Data de acesso | Coleção | SHA-256 | Licença |
|---|---|---|---|---|---|
| `PREENCHER` (grade anual 1985–2015+) | https://brasil.mapbiomas.org/ | AAAA-MM-DD | Coleção nº `PREENCHER` | `PREENCHER` | CC BY-SA 4.0 |
| `PREENCHER` (estatísticas) | | AAAA-MM-DD | | `PREENCHER` | CC BY-SA 4.0 |

> **Registrar o número da coleção é obrigatório.** O MapBiomas relança coleções
> inteiras com metodologia alterada; a série 1985–2015 muda entre coleções.
>
> **Share-alike:** ver `LICENSE-DATA`.

### 3. Modelo digital de elevação (DEM) — seleção topográfica

**Fonte não prevista no Plano de Gestão de Dados aprovado.** Incorporada no curso da
pesquisa; declarar como modificação na Seção 2.3 do Relatório Científico Final.

| Arquivo | Produto | URL | Data de acesso | Resolução | SHA-256 | Licença |
|---|---|---|---|---|---|---|
| `PREENCHER` | `PREENCHER` (SRTM / Copernicus GLO-30 / TOPODATA-INPE / outro) | `PREENCHER` | AAAA-MM-DD | `PREENCHER` m | `PREENCHER` | `PREENCHER` |

> Registrar também o método de cálculo da declividade (algoritmo, janela, unidade —
> graus ou percentual) em `data/DICIONARIO.md`.

### 4. IBGE — malhas territoriais e base municipal

| Arquivo | URL | Data de acesso | Ano de referência | SHA-256 | Licença |
|---|---|---|---|---|---|
| `PREENCHER` (malha municipal) | https://www.ibge.gov.br/geociencias/organizacao-do-territorio/malhas-territoriais.html | AAAA-MM-DD | AAAA | `PREENCHER` | Dados abertos |

### 5. Outras fontes

| Fonte | Arquivo | URL | Data de acesso | Referência | SHA-256 | Licença |
|---|---|---|---|---|---|---|
| `PREENCHER` | | | AAAA-MM-DD | | `PREENCHER` | |

---

## Fontes descartadas no curso da pesquisa

- **SICAR** — previsto no Plano de Gestão de Dados original; não utilizado na versão
  final da análise.

---

## Caches intermediários depositados

Os caches por buffer são custosos de regerar e determinam se o mecanismo de fallback
é acionado (ver README, requisito crítico nº 2). Depositados como registro `Dataset`
separado:

**DOI:** `10.5281/zenodo.PREENCHER`

| Grupo de arquivos | Buffers | Descrição |
|---|---|---|
| `dem_frontier_{b}km.csv` | 25, 30, 50, 100, 150 | Fronteira × topografia |
| `previous_land_use_units_{b}km.csv` | 25, 30, 50, 100, 150 | Uso anterior por unidade |
| `overlay_frontier_merged_long_{b}km.csv` | 25, 30, 50, 100, 150 | Sobreposição fronteira |
| `frontier_temporal_robustness_all_schemas_{b}km.csv` | 25, 30, 50, 100, 150 | Robustez temporal |

Regeneráveis a partir da semente `origin_seed: 20260609` e, portanto, **não**
depositados:

- `previous_land_use_bootstrap_draws.parquet` (1500 réplicas)
- `previous_land_use_grid_year_long.parquet`

---

## Cobertura

| Dimensão | Escopo |
|---|---|
| Recorte temporal — fronteira | 1985–2015 (esquema `pre_renovabio_1985_2015`) |
| Recorte temporal — uso anterior | 1987–2015 |
| Unidade de análise | Usina certificada (CNPJ), com entorno territorial por buffer |
| Buffer principal | 30 km |
| Buffers de robustez | 25, 50, 100, 150 km |
| Nº de unidades | `PREENCHER` |

---

## Dados derivados

Produtos desta pipeline estão em `data/interim/`, `data/processed/` e `outputs/`,
sob licença CC BY 4.0 (com ressalva de share-alike — ver `LICENSE-DATA`).
Ver `data/DICIONARIO.md`.
