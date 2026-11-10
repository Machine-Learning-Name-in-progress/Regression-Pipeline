
# Regression Pipeline — SRAG por VSR em bebês menores de 6 meses

### Pergunta

Se a relação observada antes da campanha de vacinação materna contra o VSR (iniciada no SUS em 04/12/2025) se mantivesse, quantas notificações semanais de SRAG com VSR esperaríamos em bebês de **0 a menos de 6 meses** em 2026?

- **Alvo:** contagem semanal de notificações de SRAG com VSR na faixa de 0 a <6 meses.
- **Entradas:** contagens na mesma semana nas faixas de 6 a <12, 12 a <24 e 24 a <48 meses.
- **Hipótese:** a relação histórica entre as faixas etárias se manteria. Como algumas crianças menores de 24 meses são elegíveis ao nirsevimabe, há uma análise de sensibilidade usando apenas a faixa de 24 a <48 meses.

## Dados

`data/vsr.csv`: notificações de SRAG do Sivep-Gripe (Ministério da Saúde) de crianças de até 4 anos completos, com primeiros sintomas entre 01/01/2023 e 31/10/2026. São 514.225 notificações e 5 colunas:

| Coluna         | Conteúdo                                        |
| -------------- | ------------------------------------------------ |
| `DT_SIN_PRI` | Data dos primeiros sintomas                      |
| `NU_IDADE_N` | Valor da idade                                   |
| `TP_IDADE`   | Unidade da idade (1 = dias, 2 = meses, 3 = anos) |
| `PCR_VSR`    | VSR detectado por RT-PCR (1 = sim)               |
| `AN_VSR`     | VSR detectado por teste de antígeno (1 = sim)   |

Extratos de origem: [2023](https://s3.sa-east-1.amazonaws.com/ckan.saude.gov.br/SRAG/2023/INFLUD23-23-03-2026.parquet) e [2024](https://s3.sa-east-1.amazonaws.com/ckan.saude.gov.br/SRAG/2024/INFLUD24-23-03-2026.parquet), de 23/03/2026; [2025](https://s3.sa-east-1.amazonaws.com/ckan.saude.gov.br/SRAG/2025/INFLUD25-02-11-2026.parquet) e [2026](https://dadosabertos.saude.gov.br/dataset/srag-2019-a-2026/resource/74091efc-3f75-42e8-a6fa-6b79a8d30582), de 02/11/2026. [Dicionário de dados](https://s3.sa-east-1.amazonaws.com/ckan.saude.gov.br/SRAG/dicionario-de-dados-2019-a-2025.pdf).

## Como executar

Requisitos: Python 3.11 ou superior e [uv](https://docs.astral.sh/uv/).

```bash
git clone https://github.com/Machine-Learning-Name-in-progress/Regression-Pipeline.git
cd Regression-Pipeline
uv sync
```


## Etapas do notebook

| Seção                  | Conteúdo                                                                                             |
| ------------------------ | ----------------------------------------------------------------------------------------------------- |
| 0. Configuração        | Reprodutibilidade e execução                                                                        |
| 1. Problema e dados      | Pergunta, variáveis, origem dos dados                                                                |
| 2. EDA                   | Valores ausentes, filtro de casos positivos para VSR, faixas etárias, série semanal e correlações |
| 3. Divisão treino/teste | Painel semanal (uma linha por semana epidemiológica) e corte por data                                |
| 4. Pré-processamento    | Checagem de ausentes e padronização com parâmetros do treino                                       |
| 5. Modelos candidatos    | Mediana (baseline), mínimos quadrados, Ridge, Lasso e rede neural (MLP)                              |
| 6. Validação           | Validação por blocos temporais e busca de hiperparâmetros                                          |
| 7. Comparação          | Escolha do modelo final                                                                               |
| 8. Avaliação final     | Métricas no teste e cenário de referência para 2026                                                |
| 9. Conclusão            | Interpretação e limitações                                                                        |

## Reprodutibilidade

- **Biblioteca:** a ML-Library é instalada a partir de um commit fixo (`[tool.uv.sources]` no `pyproject.toml`), travado no `uv.lock`.
- **Semente:** `SEED = 67`, aplicada com `set_seed` da biblioteca.
- **Divisão temporal, sem sorteio:** semanas epidemiológicas de domingo a sábado. Treino com as semanas de 2023 e 2024 (até 28/12/2024); teste de 04/01/2025 a 29/11/2025, última semana completa antes da campanha; cenário a partir de 10/01/2026. As semanas de dezembro de 2025 não entram em nenhum conjunto.

## Licença

[MIT](LICENSE)
