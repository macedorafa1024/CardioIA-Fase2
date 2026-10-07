# FIAP - Faculdade de Informática e Administração Paulista

<p align="center">
<a href= "https://www.fiap.com.br/"><img src="assets/logo-fiap.png" alt="FIAP - Faculdade de Informática e Admnistração Paulista" border="0" width=40% height=40%></a>
</p>

<br>

# CardioIA: A Nova Era da Cardiologia Inteligente

## Fase 2 – Diagnóstico Automatizado: IA no Estetoscópio Digital

## 👨‍🎓 Integrantes:
- Rafael Gomes de Macedo - RM 566955

## 👩‍🏫 Professores:
### Tutor(a)
- Leonardo Orabona
### Coordenador(a)
- Andre Godoi

## 📜 Descrição

Esta é a **Fase 2 — Diagnóstico Automatizado** do projeto **CardioIA**, desenvolvido na disciplina de Tecnologia em Inteligência Artificial da FIAP, dentro do formato PBL (Project Based Learning). Na Fase 1 o trabalho foi reunir dados reais sobre cardiologia (numéricos, textuais e de imagem); nesta fase, a proposta é simular a automatização do diagnóstico com IA, aplicando NLP, classificação de texto e uma primeira reflexão sobre viés de dados em saúde.

O projeto está dividido em duas partes:

- **Parte 1 — Frases de sintomas + extração de informações:** um pequeno sistema de apoio ao diagnóstico que lê frases de pacientes descrevendo sintomas, usa um mapa de conhecimento (sintoma → doença) para identificar quais sintomas aparecem em cada frase, e sugere um possível diagnóstico.
- **Parte 2 — Classificador básico de texto:** um classificador de Machine Learning (TF-IDF + Regressão Logística) treinado para prever, a partir de uma frase, se a situação relatada é de **baixo risco** ou **alto risco**, simulando uma triagem clínica automatizada.

## 📁 Estrutura de pastas

- <b>.github</b>: configurações do GitHub (padrão do template da FIAP).
- <b>assets</b>: arquivos não-estruturados do repositório, como a logo da FIAP.
- <b>data</b>: todos os dados usados nas duas partes da atividade — frases de sintomas, mapa de conhecimento e a base rotulada de triagem.
- <b>notebooks</b>: os dois notebooks Jupyter (`.ipynb`) com o código das Partes 1 e 2, já executados com as saídas visíveis.
- <b>document</b>: reflexão sobre governança de dados e vieses identificados no projeto.
- <b>README.md</b>: este arquivo.

---

## 🩺 Parte 1 — Frases de sintomas + extração de informações

**Arquivos:**
- [`data/frases_sintomas.txt`](data/frases_sintomas.txt) — 10 frases simulando relatos de pacientes, cada uma descrevendo o sintoma, há quanto tempo começou e como afeta a rotina.
- [`data/mapa_conhecimento.csv`](data/mapa_conhecimento.csv) — mapa de conhecimento no formato `sintoma_1 | sintoma_2 | doenca_associada | fonte`, relacionando expressões comuns de sintomas a possíveis diagnósticos (Infarto Agudo do Miocárdio, Angina, Insuficiência Cardíaca, Arritmia Cardíaca, Fibrilação Atrial, Síncope Cardíaca, Crise Hipertensiva, AVC e Doença Arterial Periférica). Cada linha traz, na coluna `fonte`, a referência clínica (Manual MSD, diretriz da Sociedade Brasileira de Cardiologia ou Ministério da Saúde) que embasa aquela associação — a lista completa está no notebook da Parte 1 e em [`document/reflexao_governanca_dados.md`](document/reflexao_governanca_dados.md).
- [`notebooks/parte1_diagnostico_automatizado.ipynb`](notebooks/parte1_diagnostico_automatizado.ipynb) — código Python que lê as frases, normaliza o texto (minúsculas + remoção de acentos), procura as expressões do mapa de conhecimento em cada frase e sugere o diagnóstico mais provável, com base na especificidade dos sintomas encontrados.

### Como funciona a lógica

1. O mapa de conhecimento é carregado e cada expressão de sintoma é normalizada.
2. Cada frase do arquivo `.txt` também é normalizada.
3. O código procura, por busca de substring, quais expressões do mapa aparecem na frase.
4. Cada doença associada aos sintomas encontrados recebe uma pontuação (soma do tamanho das expressões reconhecidas — expressões mais longas e específicas pesam mais).
5. A doença com maior pontuação é sugerida como diagnóstico.

Rodando o notebook com as 10 frases fornecidas, o sistema reconhece corretamente os sintomas de cada relato e sugere um diagnóstico plausível para todas elas (por exemplo: dor no peito + suor frio + náusea → Infarto Agudo do Miocárdio; fraqueza de um lado do corpo + dificuldade para falar → AVC; batimento irregular + pulso irregular → Fibrilação Atrial).

---

## 🧪 Parte 2 — Classificador básico de texto (triagem de risco)

**Arquivos:**
- [`data/triagem_dataset.csv`](data/triagem_dataset.csv) — base simulada com 51 frases rotuladas como `alto risco` ou `baixo risco` (colunas `frase,situacao`).
- [`notebooks/parte2_classificador_risco.ipynb`](notebooks/parte2_classificador_risco.ipynb) — código com todo o pipeline: divisão treino/teste, vetorização TF-IDF, treinamento de um modelo de Regressão Logística (Scikit-learn), avaliação (acurácia, relatório de classificação, matriz de confusão) e teste com frases novas.

### Resultado obtido

Com a base de ~50 frases, o modelo atingiu **~85% de acurácia** no conjunto de teste, e classificou corretamente frases novas (fora do treino/teste) como "estou com o peito apertado e suando frio" (alto risco) e "tive um pequeno desconforto na barriga depois do almoço" (baixo risco).

O notebook também traz uma reflexão sobre os padrões e limitações identificados — em especial o risco de o modelo se apoiar demais em palavras-gatilho ("leve", "forte", "súbito") em vez de entender o sintoma de fato, e a importância de minimizar falsos negativos (classificar um caso grave como baixo risco) num cenário real de triagem.

---

## ⚖️ Governança de dados e reflexão sobre vieses

Uma discussão mais detalhada sobre a qualidade dos dados, os vieses identificados em cada parte e os cuidados necessários para um uso responsável de IA em saúde está no documento [`document/reflexao_governanca_dados.md`](document/reflexao_governanca_dados.md). Um resumo rápido:

- Os dados usados aqui (frases de sintomas e a base de triagem) foram **criados manualmente para fins didáticos**, não são dados clínicos reais — então carregam o viés e as limitações de conhecimento de quem os escreveu, não de uma amostra real de pacientes.
- O **mapa de conhecimento foi checado contra fontes clínicas reconhecidas** (Manuais MSD, diretrizes da Sociedade Brasileira de Cardiologia, Ministério da Saúde) — essa checagem corrigiu duas associações que o conhecimento geral tinha simplificado demais: "falta de ar" foi movida de Angina para Insuficiência Cardíaca, e a associação "dor de cabeça → Hipertensão Arterial crônica" foi removida (a hipertensão crônica é, segundo a diretriz da SBC, "frequentemente assintomática"). Mesmo assim, isso **não substitui validação clínica formal**.
- O mapa de conhecimento e o classificador continuam sendo **protótipos didáticos**, não devem ser usados para diagnóstico ou triagem real. A classificação binária da Parte 2 (baixo/alto risco), por exemplo, está bem longe do Protocolo de Manchester (5 níveis, por cor) realmente usado nos prontos-socorros do SUS.
- Um sistema real precisaria de dados representativos de populações diversas, validação clínica por profissionais de saúde e auditoria contínua de viés antes de qualquer uso em produção.

---

## 🔧 Como executar o código

Pré-requisitos: Python 3.10+ e as bibliotecas `pandas` e `scikit-learn`.

```bash
pip install -r requirements.txt
```

Depois, abra os notebooks com Jupyter ou VS Code:

```bash
jupyter notebook notebooks/parte1_diagnostico_automatizado.ipynb
jupyter notebook notebooks/parte2_classificador_risco.ipynb
```

Os notebooks já foram executados e salvos com as saídas visíveis, então também dá para só ler o resultado direto no GitHub, sem precisar rodar nada.

## 🎥 Vídeo de demonstração

Vídeo (até 4 minutos, não listado no YouTube) mostrando o funcionamento completo da solução: **[[CLIQUE AQUI](https://youtu.be/qEj77AHdpCU)]**

## 📋 Licença

<img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/cc.svg?ref=chooser-v1"><img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/by.svg?ref=chooser-v1"><p xmlns:cc="http://creativecommons.org/ns#" xmlns:dct="http://purl.org/dc/terms/"><a property="dct:title" rel="cc:attributionURL" href="https://github.com/agodoi/template">MODELO GIT FIAP</a> por <a rel="cc:attributionURL dct:creator" property="cc:attributionName" href="https://fiap.com.br">Fiap</a> está licenciado sobre <a href="http://creativecommons.org/licenses/by/4.0/?ref=chooser-v1" target="_blank" rel="license noopener noreferrer" style="display:inline-block;">Attribution 4.0 International</a>.</p>
