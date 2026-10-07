# Reflexão — Governança de Dados e Viés (Fase 2)

Esta atividade pede, além do código, uma reflexão sobre a qualidade e a justiça dos dados usados — o primeiro passo para desenvolver IA de forma responsável em um domínio sensível como saúde. Este documento reúne essa reflexão para as duas partes da Fase 2.

## Origem dos dados usados

Diferente da Fase 1 (que usou datasets públicos reais, como o UCI Heart Disease), os dados desta Fase 2 foram **criados manualmente**, para fins didáticos:

- As 10 frases de sintomas (`data/frases_sintomas.txt`) foram escritas manualmente, simulando relatos de pacientes.
- A base de triagem (`data/triagem_dataset.csv`) também foi escrita manualmente, tentando simular frases de "baixo risco" e "alto risco" de forma plausível.
- O mapa de conhecimento (`data/mapa_conhecimento.csv`) começou com associações sintoma → doença baseadas em conhecimento geral, mas **cada linha foi depois checada contra uma fonte clínica reconhecida** (Manuais MSD, diretrizes da Sociedade Brasileira de Cardiologia, Ministério da Saúde) — a fonte de cada associação está registrada na própria coluna `fonte` do CSV, e a lista completa de referências está no notebook `parte1_diagnostico_automatizado.ipynb` (Seção 5) e ao final deste documento.

Isso é importante deixar explícito: **nenhum dado real de paciente foi usado nesta fase**, e os resultados dos dois sistemas não têm validade clínica — mesmo o mapa de conhecimento tendo sido checado contra literatura médica, ele não passou por validação clínica formal (revisão por um médico, testes com casos reais, etc.).


## Vieses identificados

### Parte 1 — Mapa de conhecimento

- **Viés de autoria única:** o mapa foi escrito por uma pessoa, então reflete o conhecimento (e os pontos cegos) dessa pessoa sobre cardiologia. Doenças menos "óbvias" ou sintomas atípicos (por exemplo, sintomas de infarto que se apresentam de forma diferente em mulheres — muitas vezes sem a clássica "dor no peito") não estão representados.
- **Viés de cobertura:** apenas 10 doenças estão no mapa. Um sistema real precisaria cobrir um espectro muito mais amplo de condições, incluindo diagnósticos diferenciais não cardíacos (ansiedade, problemas musculoesqueléticos, refluxo) que compartilham sintomas com doenças cardíacas — a ausência desses "concorrentes" no mapa infla artificialmente a confiança nos diagnósticos cardíacos sugeridos.
- **Ambiguidade tratada de forma arbitrária:** quando um sintoma aparece em mais de uma doença, o desempate por "especificidade da expressão" (tamanho do texto) é uma escolha de engenharia, não uma regra clínica validada.

### Parte 2 — Classificador de risco

- **Base de treino pequena e desbalanceada em vocabulário:** com ~50 frases, o modelo provavelmente aprendeu a associar palavras-gatilho específicas ("leve", "forte", "súbito", "intensa") ao risco, em vez de compreender o sintoma. Isso é um viés lexical: uma descrição de sintoma grave que não usa essas palavras (por exemplo, um relato mais "comedido" de quem tende a minimizar o próprio sintoma) pode ser mal classificada.
- **Viés de quem escreveu os exemplos:** como as frases de treino foram escritas por uma só pessoa, elas tendem a seguir um padrão de linguagem específico. Pacientes reais descrevem sintomas de formas muito mais diversas — vocabulário regional, gírias, diferentes níveis de escolaridade, diferentes idiomas/dialetos — e um modelo treinado só com um estilo de escrita generaliza mal para esses casos.
- **Risco assimétrico não tratado:** o modelo foi avaliado só por acurácia, que trata igualmente os dois tipos de erro. Em triagem clínica, um falso negativo (dizer que é baixo risco quando na verdade é alto risco) é muito mais grave que um falso positivo. Um sistema real precisaria priorizar métricas como *recall* da classe "alto risco", mesmo à custa de acurácia geral.
- **Comparação com um protocolo real:** as frases de "alto risco" foram escritas usando sinais de alerta reconhecidos na literatura (dor torácica prolongada e irradiada, fraqueza de um lado do corpo, síncope com sudorese fria), mas o classificador em si está muito longe do **Protocolo de Manchester**, usado de fato nos prontos-socorros e UPAs do SUS, que tem 5 níveis de prioridade (por cor) e critérios padronizados por queixa clínica, definidos por um grupo certificador (GBCR). A classificação binária baixo/alto risco usada aqui é uma simplificação didática, não um protocolo de triagem validado.

## Cuidados para um uso responsável (e para as próximas fases)

- **Nunca usar estes protótipos para diagnóstico ou triagem real.** Eles servem apenas para entender, na prática, como esse tipo de sistema é construído.
- **Ampliar e diversificar os dados** antes de qualquer uso mais sério: mais doenças, mais variações de linguagem, e idealmente dados reais (anonimizados) revisados por profissionais de saúde.
- **Envolver especialistas clínicos na curadoria** do mapa de conhecimento e na rotulagem da base de triagem, em vez de depender do conhecimento de uma única pessoa não-médica.
- **Medir e reportar erros por subgrupo**, quando houver dados demográficos disponíveis, para identificar se o sistema erra mais para algum perfil de paciente.
- **Manter humano no circuito (human-in-the-loop):** qualquer sistema de apoio ao diagnóstico ou triagem deve ser revisado por um profissional de saúde, nunca decidir sozinho — especialmente porque erros de falso negativo em saúde podem ter consequências graves.

## Referências consultadas

- Manual MSD (edição profissional). *Infarto Agudo do Miocárdio (IAM)*. https://www.msdmanuals.com/pt/profissional/doencas-cardiovasculares/doenca-coronariana/infarto-agudo-do-miocardio-iam
- Manual MSD (versão para a família). *Angina*. https://www.msdmanuals.com/pt/casa/disturbios-do-coracao-e-dos-vasos-sanguineos/doenca-arterial-coronariana/angina
- Sociedade Brasileira de Cardiologia. *II Diretriz Brasileira de Insuficiência Cardíaca Aguda*. https://www.scielo.br/j/abc/a/6CWscRNFQdbmnHBVMqjBtfC/?format=html&lang=pt
- Manual MSD (versão para a família). *Considerações gerais sobre arritmias cardíacas*. https://www.msdmanuals.com/pt/casa/disturbios-do-coracao-e-dos-vasos-sanguineos/arritmias-cardiacas/consideracoes-gerais-sobre-arritmias-cardiacas
- Manual MSD (edição profissional). *Fibrilação Atrial*. https://www.msdmanuals.com/pt/profissional/doencas-cardiovasculares/arritmias-cardiacas-especificas/fibrilacao-atrial
- Manual MSD (edição profissional). *Síncope*. https://www.msdmanuals.com/pt/profissional/doencas-cardiovasculares/sintomas-de-doencas-cardiovasculares/sincope
- Sociedade Brasileira de Cardiologia. *Posicionamento Luso-Brasileiro de Emergências Hipertensivas – 2020*. https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9744343/
- Sociedade Brasileira de Cardiologia. *Diretrizes Brasileiras de Hipertensão Arterial – 2020*. https://pmc.ncbi.nlm.nih.gov/articles/PMC9949730/
- Ministério da Saúde. *AVC*. https://www.gov.br/saude/pt-br/assuntos/saude-de-a-a-z/a/avc
- Manual MSD (edição profissional). *Doença Arterial Periférica*. https://www.msdmanuals.com/pt-pt/profissional/doencas-cardiovasculares/disturbios-arteriais-perifericos/doenca-arterial-periferica
- Cemig Saúde. *Protocolo de Manchester: entenda como funciona a classificação de risco nos prontos-socorros*. https://cemigsaude.org.br/noticias/protocolo-de-manchester-entenda-como-funciona-classificacao-de-risco-nos-prontos-socorros
