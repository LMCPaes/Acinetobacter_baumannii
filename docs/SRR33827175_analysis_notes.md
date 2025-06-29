# Análise da Cepa SRR33827175
- **Data**: 29/06/2025
- **Cepa Analisada**: Acinetobacter baumannii SRR33827175
- **Genes de Resistência Identificados**:
[cite_start]  - `dfrA1`: Resistência a trimetoprim (dihidrofolato redutase DfrA1).
  - [cite_start]`catB2`: Resistência a cloranfenicol (cloranfenicol O-acetiltransferase CatB2).
  - [cite_start]`sat2`: Resistência a estreptotricina (N-acetiltransferase Sat2).
  - [cite_start]`aadA1`: Resistência a aminoglicosídeos como estreptomicina (aminoglicosídeo nucleotidiltransferase AadA1).
  - [cite_start]`tet(B)`: Resistência a tetraciclina (transportador de efluxo MFS Tet(B)).
  - [cite_start]`qnrD1`: Resistência a quinolonas (proteína QnrD1 de repetição de pentapeptídeos).
  - [cite_start]`hugA`: Resistência a beta-lactâmicos (beta-lactamase classe A).

- **Classes de Antibióticos Afetadas**:
[cite_start]  - Trimetoprim: `dfrA1`.
  - [cite_start]Fenicois (Cloranfenicol): `catB2`.
  - [cite_start]Estreptotricinas: `sat2`.
  - [cite_start]Aminoglicosídeos: `aadA1` (Streptomicina).
  - [cite_start]Tetraciclinas: `tet(B)`.
  - [cite_start]Quinolonas: `qnrD1`.
  - [cite_start]Beta-lactâmicos: `hugA`.

- **Comparação com Outras Cepas**:
  - **AB5075**: Possui `dfrA7` (trimetoprim), `catB2` (cloranfenicol), e `aadA1` (aminoglicosídeo). SRR33827175 compartilha `catB2` e `aadA1` com a AB5075. AB5075 possui genes de carbapenemase (`blaOXA-69`, `blaOXA-23`) e mutação `gyrA_S81L` que não foram detectados na SRR33827175.
  - **ACICU**: Possui `aadA1` (aminoglicosídeo), `catB2` (cloranfenicol), `qnrD1` (quinolona) e `dfrA1` (trimetoprim). SRR33827175 compartilha `dfrA1`, `catB2`, `aadA1` e `qnrD1` com a ACICU. ACICU possui genes de carbapenemase (`blaOXA-58`, `blaOXA-66`) e mutação `gyrA_S81L` que não foram detectados na SRR33827175.
  - **AYE**: *(Aguardando nota de análise completa para comparação)*.
  - **ATCC 19606**: *(Aguardando nota de análise completa para comparação)*.

- **Observações/Notas Adicionais**:
  - Esta cepa apresenta uma variedade de genes de resistência a múltiplas classes de antibióticos, incluindo trimetoprim, cloranfenicol, estreptotricina, aminoglicosídeos, tetraciclinas, quinolonas e beta-lactâmicos.
  - O gene `qnrD1` confere resistência a quinolonas sem ser uma mutação cromossômica, o que é um mecanismo importante a ser destacado.
  - A ausência de carbapenemases típicas (como `OXA-23`, `OXA-58`, `NDM`, `KPC`) e mutações em genes como `gyrA` (para quinolonas) que são comuns em outras cepas MDR de *A. baumannii* pode indicar um perfil de resistência distinto em comparação com as cepas mais problemáticas clinicamente, ou pode ser uma limitação da detecção do AMRFinderPlus. É importante investigar mais detalhadamente o perfil fenotípico desta cepa.
  - Verificar a presença de bombas de efluxo (ex.: `adeABC`, `mexAB-OprM`).
