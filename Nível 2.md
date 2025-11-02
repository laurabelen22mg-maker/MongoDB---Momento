# Relatório do Nível 2

##  Descrição

Este relatório apresenta uma visão financeira consolidada da empresa Momento, abordando o número de funcionários por departamento, o custo total da folha de pagamento, as médias salariais e os comparativos entre setores.
O objetivo é oferecer à diretoria financeira (CFO) informações claras e precisas sobre custos operacionais e distribuição salarial, apoiando o planejamento orçamentário e a tomada de decisões estratégicas.
##  Metodologia

Para a elaboração deste relatório, foram empregadas agregações no MongoDB com os operadores $match, $group, $avg e $sum, a fim de calcular o total e a média salarial por departamento, além de identificar os setores com maior e menor impacto financeiro.
Essa metodologia permitiu consolidar informações dispersas na coleção funcionarios, proporcionando uma visão clara da estrutura de custos e facilitando análises de desempenho financeiro e eficiência operacional

---

## Informações

### Consultas Utilizadas

```javascript
// 2.1 Quantos funcionários trabalham no Departamento de Vendas?
db.funcionarios.countDocuments({ departamento: ObjectId("85992103f9b3e0b3b3c1fe71") });

// 2.2 Qual é o custo total com salários do Departamento de Vendas?
db.funcionarios.aggregate([
  { $match: { departamento:  ObjectId("85992103f9b3e0b3b3c1fe71") } },
  { $group: { _id: null, custoTotal: { $sum: "$salario" } } }
]);

// 2.3 Qual é a média salarial da empresa, excluindo os cargos de CEO, CMO e CFO?
db.funcionarios.aggregate([
  { $match: { cargo: { $nin: ["CEO", "CFO", "CMO"] } } },
  { $group: { _id: null, mediaSalarial: { $avg: "$salario" } } }
]);

// 2.4 Qual é a média salarial do Departamento de Tecnologia?
db.funcionarios.aggregate([
  { $match: { departamento: "Tecnologia" } },
  { $group: { _id: "$departamento", media: { $avg: "$salario" } } }
]);

// 2.5 Qual departamento possui a maior média salarial?
db.funcionarios.aggregate([
  { $group: { _id: "$departamento", media: { $avg: "$salario" } } },
  { $sort: { media: -1 } },
  { $limit: 1 }
]);

// 2.6 Qual departamento possui o menor número de funcionários?
db.funcionarios.aggregate([
  { $group: { _id: "$departamento", total: { $sum: 1 } } },
  { $sort: { total: 1 } },
  { $limit: 1 }
]);
```

### Ajustes / Observações

Durante o desenvolvimento das consultas, foi necessário ajustar o filtro do Departamento de Tecnologia, substituindo o uso do ObjectId por uma comparação direta do nome do departamento, uma vez que os registros estavam armazenados como strings. Sem essa correção, as consultas retornavam resultados vazios. Após o ajuste, os dados foram processados corretamente.

---

###  Resultados Obtidos

| Nº  | Consulta                                         | Resultado                     |
| --- | ------------------------------------------------ | ----------------------------- |
| 2.1 | Funcionários no Departamento de Vendas           | **10 funcionários**           |
| 2.2 | Custo total com salários (Vendas)                | **$95.100**                   |
| 2.3 | Média salarial da empresa (excluindo executivos) | **$9.551,30**                 |
| 2.4 | Média salarial do Departamento de Tecnologia     | **$4.633,34**                 |
| 2.5 | Departamento com maior média salarial            | **Financeiro ($71.000)**      |
| 2.6 | Departamento com menor número de funcionários    | **Executivo (1 colaborador)** |

---

##  Conclusões Alcançadas

* O Departamento de Vendas representa um custo significativo na folha de pagamento, proporcional à sua dimensão e relevância estratégica.

* O setor de Tecnologia apresenta média salarial acima da média geral da empresa, refletindo o investimento em profissionais técnicos e de inovação.

* O Departamento Financeiro lidera em média salarial, devido à concentração de cargos executivos.

* O Departamento Executivo, embora conte com apenas um colaborador, possui alta representatividade estratégica e financeira.

* A análise evidencia uma estrutura de custos equilibrada, com potencial de crescimento nas áreas de Tecnologia e Inovação.

* Recomenda-se a realização de monitoramentos trimestrais da folha salarial, visando controle financeiro contínuo e retenção de talentos.
