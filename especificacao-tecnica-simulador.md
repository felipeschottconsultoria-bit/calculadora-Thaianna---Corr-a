# Especificação Técnica — Simulador da Reforma Tributária (Simples Nacional Anexo IV + Comparativo de Regimes)

**Como usar este documento:** ele descreve, de forma auditável, tudo que foi implementado no arquivo `simulador-reforma-tributaria-advocacia.html`. Ele **não substitui o arquivo** — para revisar, testar ou pedir ajustes (inclusive a um Claude), sempre parta do HTML real, anexando-o à conversa. Este documento serve para conferir se a lógica está certa e para saber exatamente o que falta ou o que é premissa assumida.

---

## 1. Escopo da ferramenta

Duas abas:

- **Aba 1 — Simples Nacional, Anexo IV:** calcula a alíquota efetiva do DAS para um escritório de advocacia, ano a ano (2026 a 2033+), com a opção de recolher IBS/CBS dentro ou fora da DAS.
- **Aba 2 — Comparativo de Regimes:** compara, lado a lado, Pessoa Física (autônomo), Simples Nacional (dentro/fora da DAS), Lucro Presumido e Lucro Real, para o mesmo faturamento/ano.

---

## 2. Fontes normativas utilizadas

| Norma | Uso nesta simulação | Link oficial |
|---|---|---|
| **EC nº 132/2023** | Base constitucional da Reforma (IBS, CBS, Imposto Seletivo) | https://www.planalto.gov.br/ccivil_03/constituicao/emendas/emc/emc132.htm |
| **LC nº 123/2006** | Estatuto da ME/EPP; institui o Simples Nacional e o Anexo IV | https://www.planalto.gov.br/ccivil_03/leis/lcp/lcp123.htm |
| **LC nº 214/2025** | Institui IBS/CBS/Imposto Seletivo; integração do Simples ao novo regime; redução de 30% para profissões regulamentadas | https://www.planalto.gov.br/ccivil_03/leis/lcp/lcp214.htm |
| **LC nº 227/2026** | Segunda etapa da regulamentação; CGIBS; ajustes na LC 214/2025 e na LC 123/2006 | https://www.planalto.gov.br/ccivil_03/leis/lcp/lcp227.htm |
| **Resolução CGSN nº 140/2018** | Texto-base do Simples Nacional; Anexo IV vigente em 2026 (partilha IRPJ/CSLL/COFINS/PIS/ISS) | https://normas.receita.fazenda.gov.br/sijut2consulta/anexoOutros.action?idArquivoBinario=48433 |
| **Resolução CGSN nº 190/2026** | Altera a Resolução 140/2018; cronograma de transição do Anexo IV 2027–2033; art. 21, II, "a" (fórmula do sublimite) | https://www.in.gov.br/web/dou/-/resolucao-cgsn-n-190-de-4-de-agosto-de-2026-724454118 |

**Pendência conhecida:** não localizamos (nem foi fornecida) uma fonte formal e específica para o percentual exato de "redução de 30% para profissões regulamentadas" dentro da LC 214/2025 — o artigo exato deve ser conferido antes de uso em peça, embora o valor de 30% e os cálculos derivados dele tenham sido confirmados numericamente contra a tabela que a usuária forneceu (ver seção 6).

---

## 3. Anexo IV — faixas, nominal e dedução (idêntico em todos os anos, 2026–2033)

| Faixa | Receita Bruta em 12 meses | Alíquota nominal | Valor a deduzir |
|---|---|---|---|
| 1ª | até R$ 180.000,00 | 4,50% | R$ 0,00 |
| 2ª | R$ 180.000,01 a R$ 360.000,00 | 9,00% | R$ 8.100,00 |
| 3ª | R$ 360.000,01 a R$ 720.000,00 | 10,20% | R$ 12.420,00 |
| 4ª | R$ 720.000,01 a R$ 1.800.000,00 | 14,00% | R$ 39.780,00 |
| 5ª | R$ 1.800.000,01 a R$ 3.600.000,00 | 22,00% | R$ 183.780,00 |
| 6ª (sublimite) | R$ 3.600.000,01 a R$ 4.800.000,00 | 32,90% (2026–2028) / 33,00% (2029+) | R$ 828.000,00 |

Fórmula da alíquota efetiva: **[(RBT12 × alíquota nominal) − valor a deduzir] ÷ RBT12**

Acima de R$ 4.800.000,00 a empresa sai do Simples Nacional — a calculadora não estima esse cenário.

---

## 4. Partilha por ano (percentual de repartição dos tributos dentro da DAS)

### 2026 (regime vigente, pré-Reforma — fonte: Resolução CGSN 140/2018)
| Faixa | IRPJ | CSLL | COFINS | PIS | ISS |
|---|---|---|---|---|---|
| 1ª | 18,80% | 15,20% | 17,67% | 3,83% | 44,50% |
| 2ª | 19,80% | 15,20% | 20,55% | 4,45% | 40,00% |
| 3ª | 20,80% | 15,20% | 19,73% | 4,27% | 40,00% |
| 4ª | 17,80% | 19,20% | 18,90% | 4,10% | 40,00% |
| 5ª | 18,80% | 19,20% | 18,08% | 3,92% | 40,00% |
| 6ª | 53,50% | 21,50% | 20,55% | 4,45% | — |

5ª faixa, teto de ISS 5% quando alíquota efetiva > 12,5%: excedente rateado por IRPJ 31,33% / CSLL 32,00% / COFINS 30,13% / PIS 6,54%.

*(Não há CBS/IBS na partilha de 2026 — são recolhidos à parte, ver seção 6.)*

### 2027–2028 (Resolução CGSN 190/2026)
| Faixa | IRPJ | CSLL | CBS | ISS | IBS |
|---|---|---|---|---|---|
| 1ª | 18,80% | 15,20% | 21,26% | 44,50% | 0,24% |
| 2ª | 19,80% | 15,20% | 24,73% | 40,00% | 0,27% |
| 3ª | 20,80% | 15,20% | 23,74% | 40,00% | 0,26% |
| 4ª | 17,80% | 19,20% | 22,75% | 40,00% | 0,25% |
| 5ª | 18,80% | 19,20% | 21,76% | 40,00% | 0,24% |
| 6ª | 53,71% | 21,59% | 24,70% | — | — |

5ª faixa, teto ISS 5% (>12,5%): IRPJ 31,33% / CSLL 32,00% / CBS 36,27% / IBS 0,40% sobre o excedente.

### 2029
IRPJ/CSLL/CBS/ISS/IBS por faixa: 1ª 18,80/15,20/21,50/40,05/4,45 · 2ª 19,80/15,20/25,00/36,00/4,00 · 3ª 20,80/15,20/24,00/36,00/4,00 · 4ª 17,80/19,20/23,00/36,00/4,00 · 5ª 18,80/19,20/22,00/36,00/4,00 · 6ª 53,50/21,50/25,00/—/—.
Teto ISS 4,5% (>12,5%): IRPJ 29,38% / CSLL 30,00% / CBS 34,38% / IBS 6,25%.

### 2030
1ª 18,80/15,20/21,50/35,60/8,90 · 2ª 19,80/15,20/25,00/32,00/8,00 · 3ª 20,80/15,20/24,00/32,00/8,00 · 4ª 17,80/19,20/23,00/32,00/8,00 · 5ª 18,80/19,20/22,00/32,00/8,00 · 6ª 53,50/21,50/25,00/—/—.
Teto ISS 4,0% (>12,5%): IRPJ 27,65% / CSLL 28,24% / CBS 32,35% / IBS 11,76%.

### 2031
1ª 18,80/15,20/21,50/31,15/13,35 · 2ª 19,80/15,20/25,00/28,00/12,00 · 3ª 20,80/15,20/24,00/28,00/12,00 · 4ª 17,80/19,20/23,00/28,00/12,00 · 5ª 18,80/19,20/22,00/28,00/12,00 · 6ª 53,50/21,50/25,00/—/—.
Teto ISS 3,5% (>12,5%): IRPJ 26,11% / CSLL 26,67% / CBS 30,56% / IBS 16,67%.

### 2032
1ª 18,80/15,20/21,50/26,70/17,80 · 2ª 19,80/15,20/25,00/24,00/16,00 · 3ª 20,80/15,20/24,00/24,00/16,00 · 4ª 17,80/19,20/23,00/24,00/16,00 · 5ª 18,80/19,20/22,00/24,00/16,00 · 6ª 53,50/21,50/25,00/—/—.
Teto ISS 3,0% (>12,5%): IRPJ 24,74% / CSLL 25,26% / CBS 28,95% / IBS 21,05%.

### 2033 (ISS extinto, só IRPJ/CSLL/CBS/IBS)
1ª 18,80/15,20/21,50/44,50 · 2ª 19,80/15,20/25,00/40,00 · 3ª 20,80/15,20/24,00/40,00 · 4ª 17,80/19,20/23,00/40,00 · 5ª 18,80/19,20/22,00/40,00 · 6ª 53,50/21,50/25,00/—.
Sem teto/regra de redistribuição em 2033 (não há mais ISS a limitar).

**Verificação estrutural:** em toda faixa 1–5, de todo ano, a soma dos percentuais de partilha é exatamente 100% — confirmado por checagem numérica. Isso implica que, **dentro da DAS**, a alíquota efetiva total não muda com o ano (só a composição interna entre os tributos muda); o valor só cresce com o ano quando o CBS/IBS é recolhido **fora** da DAS (seção 6).

### Regra do sublimite (6ª faixa, RBT12 entre R$ 3.600.000,01 e R$ 4.800.000,00)
IRPJ/CSLL/CBS(ou PIS/COFINS em 2026) calculados pela alíquota/dedução da 6ª faixa. ISS e IBS **não entram nessa conta** — são calculados à parte, por:

```
ISS = {[(RBT12 × 22%) − R$ 183.780,00] / RBT12} × percentual de ISS da 5ª faixa do mesmo ano
IBS = {[(RBT12 × 22%) − R$ 183.780,00] / RBT12} × percentual de IBS da 5ª faixa do mesmo ano
```

---

## 5. Cronograma de CBS/IBS "fora da DAS" (alíquota cheia vs. efetiva)

Fonte: tabela fornecida pela usuária e confirmada nesta conversa. A coluna "efetiva" = alíquota cheia × 0,7 (redução de 30% para profissão regulamentada).

| Ano | CBS cheia | CBS efetiva | IBS Estadual cheia | IBS Estadual efetiva | IBS Municipal cheia | IBS Municipal efetiva | Total efetivo |
|---|---|---|---|---|---|---|---|
| 2026 | 0,90% | 0,63% | 0,10% | 0,07% | 0% | 0% | 0,70% |
| 2027 | 8,40% | 5,88% | 0,05% | 0,035% | 0,05% | 0,035% | 5,95% |
| 2028 | 8,40% | 5,88% | 0,05% | 0,035% | 0,05% | 0,035% | 5,95% |
| 2029 | 8,50% | 5,95% | 1,60% | 1,12% | 0,25% | 0,175% | 7,245% |
| 2030 | 8,50% | 5,95% | 3,20% | 2,24% | 0,50% | 0,35% | 8,54% |
| 2031 | 8,50% | 5,95% | 4,80% | 3,36% | 0,75% | 0,525% | 9,835% |
| 2032 | 8,50% | 5,95% | 6,40% | 4,48% | 1,00% | 0,70% | 11,13% |
| 2033 | 8,50% | 5,95% | 16,00% | 11,20% | 2,50% | 1,75% | 18,90% |

**Lógica do checkbox "redução de 30%":** desmarcado = usa a coluna cheia; marcado = multiplica por 0,7 (chega na coluna efetiva). Aplica-se em: Simples fora da DAS, Pessoa Física, Lucro Presumido e Lucro Real.

---

## 6. Módulo "Carga Total Combinada" (Aba 1, quando "fora da DAS" está selecionado)

O topo da tela mostra o **total combinado** = (o que resta na DAS: IRPJ + CSLL + ISS, ou + PIS/COFINS em 2026) **+** (CBS/IBS recolhidos separadamente, seção 5) — não apenas o resíduo da DAS. Isso evita a impressão de que a carga cai quando, na verdade, ela é segregada e cresce ano a ano.

---

## 7. Comparativo de Regimes (Aba 2) — fórmulas por regime

**Pessoa Física (autônomo):**
- IRPF: alíquota efetiva informada pelo usuário (não recalculada por faixa progressiva)
- INSS: 20% sobre o rendimento, limitado ao teto de **R$ 8.475,55**
- ISS: % informado ou valor fixo
- CBS/IBS: cronograma da seção 5 (com ou sem redução de 30%)

**Simples Nacional — dentro da DAS:** motor da Aba 1 (seções 3–4), sem segregar CBS/IBS. **+ INSS Patronal (20% sobre folha + pró-labore) + retenção do sócio/segurado (11% sobre o pró-labore, limitada ao teto de R$ 8.475,55)** — ambos recolhidos fora da DAS mesmo nesta opção, porque o Anexo IV exclui a CPP do cálculo do DAS por definição legal. **RAT e Contribuição a Terceiros/Sistema S NÃO se aplicam** (dispensa legal do Simples Nacional).

**Simples Nacional — fora da DAS:** motor da Aba 1 com CBS/IBS segregados (seção 5) **+ o mesmo INSS Patronal e retenção do sócio** acima.

**Lucro Presumido:** presunção de 32% sobre o faturamento; IRPJ 15% + adicional de 10% sobre a parcela do lucro presumido que exceder R$ 20.000,00/mês; CSLL 9%; PIS 0,65%/COFINS 3% (só até 2026, substituídos por CBS a partir de 2027); INSS Patronal 20% (folha + pró-labore); retenção sócio 11% (limitada ao teto R$ 8.475,55); RAT 1% (sobre folha); Terceiros 4,5% (sobre folha); ISS informado; CBS/IBS pelo cronograma (a partir de 2027).

**Lucro Real:** mesma estrutura do Presumido, mas a base do IRPJ/CSLL/adicional é **faturamento − despesas informadas** (lucro real), não a presunção de 32%; PIS 1,65%/COFINS 7,60% até 2026.

**Correção aplicada nesta conversa:** a retenção do sócio (11%) não estava respeitando o teto de R$ 8.475,55 em nenhum regime — foi corrigida para todos ao mesmo tempo.

---

## 8. Limitações conhecidas / pontos que ainda dependem de confirmação da usuária

1. O artigo exato da LC 214/2025 que prevê a redução de 30% para profissões regulamentadas não foi localizado/formalmente citado — o valor foi validado apenas numericamente contra a tabela fornecida.
2. RAT e Terceiros foram assumidos como não aplicáveis ao Simples Nacional (dispensa legal geral do regime) — não há uma tabela específica da usuária confirmando isso para o Anexo IV especificamente.
3. IRPF (pessoa física) e ISS são sempre valores informados manualmente — a calculadora não aplica tabela progressiva de IRPF nem verifica alíquota mínima/máxima de ISS por município.
4. Acima de R$ 4.800.000,00 de RBT12 (fora do Simples Nacional) não é simulado.
5. Para 2026, a calculadora usa a redação da Resolução CGSN 140/2018 vigente no momento da pesquisa (pode ter sido alterada por normas supervenientes).

---

## 9. Para pedir ajustes a um Claude

Anexe **o arquivo `simulador-reforma-tributaria-advocacia.html`** junto com este documento e descreva a mudança desejada. Não peça para "recriar" o simulador só com este texto — peça para **editar o arquivo anexado**, o que garante que nada do que já foi corrigido nesta conversa se perca.
