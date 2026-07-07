# 🎓 Gestão Integrada de Treinamentos — O-HACKA-TÁ-ON

Solução de automação e BI desenvolvida para o **Desafio 01** da 4ª edição do **O-HACKA-TÁ-ON**, hackathon promovido pela **M. Dias Branco**, em parceria com a **Universidade de Fortaleza (Unifor Hub)** e a **Profectum** — maio de 2026.

> 🏅 Certificado de participação emitido pela M. Dias Branco / Unifor / Profectum.

---

## 📌 O desafio

A área de Treinamento e Desenvolvimento da M. Dias Branco opera com múltiplas plataformas externas de aprendizagem, cada uma atendendo públicos diferentes (Supply, comercial, estratégia, segurança da informação, entre outros). Como essas plataformas não são integradas ao sistema corporativo, a consolidação oficial dos dados de treinamento dependia de um processo manual: extração de relatórios, adaptação de formatos, preenchimento de planilhas e geração de reportes para cada liderança.

Esse processo gerava três problemas centrais:

- **Retrabalho** — cada ciclo de treinamento exigia tratamento manual de planilhas.
- **Lentidão** — os indicadores de adesão só chegavam às lideranças depois de várias etapas manuais.
- **Baixa escalabilidade** — o modelo não se sustentava com o crescimento do ecossistema de plataformas.

O desafio pedia uma solução que **automatizasse e centralizasse** essas informações, aumentando a confiabilidade dos dados e a capacidade de decisão das áreas.

## 💡 A solução

Construí um pipeline de **ETL automatizado** (extração, tratamento e carga) que elimina o tratamento manual de planilhas, conectando um relatório bruto até um dashboard executivo atualizado automaticamente:

```
Arquivo único (CSV) chega numa pasta do SharePoint
        │
        ▼
Get file content — captura o conteúdo bruto do arquivo
        │
        ▼
Compose — converte o conteúdo binário em texto puro
        │
        ▼
Separar Linhas — quebra o texto em uma lista de registros (split por %0A)
        │
        ▼
Apply to each — percorre cada uma das 1.402 linhas do arquivo
        │
        ▼
Condition — filtro de segurança
   ├─ ❌ Linha vazia ou cabeçalho ("Id", "Nome"...) → descarta
   └─ ✅ Linha de dados válida → segue
        │
        ▼
Create item — fatia a linha por vírgula (split) e grava por índice
   numérico ([0], [1], [2]...) na lista do SharePoint
   "Participantes Supply Go"
        │
        ▼
Power BI — conecta na lista já limpa e tipada e atualiza
   os gráficos automaticamente
```

### Por que mapear por índice numérico, e não por nome de coluna

Um dos principais problemas encontrados durante o desenvolvimento foi a quebra do fluxo por divergência de schema (ex.: erro na coluna `Title`, exigida internamente pelo SharePoint). Resolvi isso mapeando as colunas do CSV **por posição** (`split(item(), ',')[0]`, `[1]`, `[2]`...) em vez de depender de nomes de coluna, o que tornou o processo estável mesmo diante de pequenas variações no relatório de origem.

A condicional também garante que a primeira linha (cabeçalho) e linhas vazias nunca cheguem ao SharePoint, evitando dados "sujos" no repositório oficial.

## 📊 Dashboard — Power BI

O dashboard **"Acompanhamento Executivo — Engajamento de Treinamentos"** conecta diretamente na lista do SharePoint alimentada pelo fluxo acima, e traz:

- Indicadores gerais: total de participantes, treinamentos concluídos, taxa de conclusão e não iniciados
- Gráfico de rosca com o status de conclusão dos cursos (concluído / não concluído / não iniciado)
- Ranking de engajamento por estado, com filtro dinâmico por área
- Medidas construídas em **DAX**, com tratamento de formatação regional (moeda em Real, separador decimal brasileiro)

`![Dashboard Power BI](./assets/dashboard.png)`

## 🖼️ O desafio original

`![Card do Desafio 01](./assets/desafio-01.jpg)`

## 🛠️ Stack

- **Power Automate** — orquestração do fluxo ETL
- **SharePoint** — armazenamento do arquivo de origem e da lista tratada
- **Power BI + DAX** — modelagem e visualização dos indicadores
- **Power Query** — tratamento adicional de dados

## 🧠 Aprendizados

- Resolver ETL sem código tradicional, usando apenas expressões do Power Automate (`split`, `decodeUriComponent`, indexação de arrays)
- Projetar uma automação resiliente a variações de schema, mapeando por posição em vez de por nome
- Conectar uma automação de bastidor a um dashboard de decisão executiva, fechando o ciclo dado → indicador → decisão
- Comunicar um problema real de negócio (ecossistema de aprendizagem distribuído x gestão centralizada) em uma solução técnica objetiva

---

*Projeto desenvolvido para o O-HACKA-TÁ-ON 2026 (M. Dias Branco × Unifor Hub × Profectum).*
