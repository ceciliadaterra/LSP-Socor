name: Gerar Checklist Diário Automático

on:
  schedule:
    # Roda todos os dias às 09:00 UTC (06:00 no horário de Brasília)
    - cron: '0 9 * * *'
  # Permite rodar manualmente pela aba Actions para testar a qualquer momento
  workflow_dispatch:

permissions:
  issues: write

jobs:
  criar-checklist:
    runs-on: ubuntu-latest
    steps:
      - name: Criar Issue do Dia
        uses: actions/github-script@v7
        with:
          script: |
            // Obtém a data atual ajustada para o fuso horário de Brasília (UTC-3)
            const agora = new Date();
            const dataBR = new Date(agora.toLocaleString("en-US", { timeZone: "America/Sao_Paulo" }));
            
            const dia = String(dataBR.getDate()).padStart(2, '0');
            const mes = String(dataBR.getMonth() + 1).padStart(2, '0');
            const ano = dataBR.getFullYear();
            const dataFormatada = `${dia}/${mes}/${ano}`;

            const titulo = `Rotina Diária - ${dataFormatada}`;
            const corpo = `# 🔬 Checklist Diário de Operação - Análises Clínicas

**Data:** ${dataFormatada}  
**Responsável Técnico / Operador:** @ceciliadaterra

---

## ☀️ 1. Manhã
- [ ] Verificar passagem de plantão
- [ ] Assinar exames executados do plantão anterior
- [ ] Verificar pendências de coleta do plantão anterior (das 16h do dia anterior até às 07h30 do dia atual)
- [ ] Contabilizar estoque e realizar requisição de insumos
- [ ] Lançar Controle Interno de Qualidade (CIQ) dos gasômetros ATÉ ÀS 10h

---

## 🕛 2. Tarde
- [ ] *(Inserir tarefa da tarde)*
- [ ] *(Inserir tarefa da tarde)*
- [ ] Observar lançamento de etiquetas da rotina de 16h, imprimir, separar por setor e entregar a colhedora do andar
- [ ] *(Inserir tarefa da tarde)*

---

## 🌃 3. Final do plantão
- [ ] Redigir passagem de plantão
- [ ] Preencher planilhas de bancada e equipamentos
- [ ] *(Inserir tarefa do final do plantão)*
- [ ] Armazenar e organizar amostras na soroteca
- [ ] Limpar e desinfetar bancadas de trabalho ao final do turno

---

## 🚨 Ocorrências / Observações do Dia
> Registre abaixo qualquer intercorrência, falha em equipamento ou inconsistência identificada durante o plantão:

* **Ocorrência:** 
* **Ação adotada:** `;

            await github.rest.issues.create({
              owner: context.repo.owner,
              repo: context.repo.repo,
              title: titulo,
              body: corpo,
              labels: ['rotina', 'checklist', 'operacao']
            });
