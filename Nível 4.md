# Relatório do  Nível 4

##  Descrição

Este relatório apresenta uma análise dos escritórios da empresa Momento, abordando seus custos operacionais e inventário de suprimentos.
O objetivo é oferecer à equipe de Operações informações estratégicas sobre gestão de custos, alocação de recursos e eficiência operacional, apoiando decisões de otimização e planejamento.

##  Metodologia

Foram executadas consultas com agregações no MongoDB, utilizando os operadores $unwind, $group e $sum para quantificar o inventário, calcular custos totais e comparar o desempenho entre escritórios.
Essa abordagem se mostrou eficiente para avaliar o estoque e as despesas logísticas, possibilitando decisões estratégicas sobre compras, utilização de recursos e controle de suprimentos, além de identificar unidades com maiores custos ou maior diversidade de itens..

---

##  Informações

###  Consultas Utilizadas

```javascript
// 4.1  Liste todos os escritórios e seus respectivos países.
db.escritorios.find({}, { nome: 1, pais: 1, _id: 0 });

// 4.2 Qual é o custo total de suprimentos em cada escritório? Ordene do mais caro ao mais barato.
db.escritorios.aggregate([
  { $unwind: "$suprimentos" },
  { $group: {
      _id: "$nome",
      custoTotal: { $sum: { $multiply: ["$suprimentos.quantidade", "$suprimentos.precoUnitario"] } }
  }},
  { $sort: { custoTotal: -1 } }
]);

// 4.3 Qual escritório possui a maior quantidade de diferentes tipos de suprimentos?
db.escritorios.aggregate([
  { $project: { nome: 1, tiposSuprimentos: { $size: "$suprimentos" } } },
  { $sort: { tiposSuprimentos: -1 } }
]);

// 4.4 Qual é o suprimento mais caro (considerando preço unitário) em toda a empresa?
db.escritorios.aggregate([
  { $unwind: "$suprimentos" },
  { $sort: { "suprimentos.precoUnitario": -1 } },
  { $limit: 1 },
  { $project: { produto: "$suprimentos.produto", precoUnitario: "$suprimentos.precoUnitario" } }
]);

// 4.5 Calcule o valor total do inventário de suprimentos da empresa (quantidade × preço unitário de todos os itens em todos os escritórios).
db.escritorios.aggregate([
  { $unwind: "$suprimentos" },
  { $group: { _id: null, valorTotal: { $sum: { $multiply: ["$suprimentos.quantidade", "$suprimentos.precoUnitario"] } } } }
]);
```

###  Ajustes / Observações

Durante a elaboração do relatório, constatou-se que alguns registros apresentavam inconsistências nas quantidades de suprimentos e ausência de valores unitários.
Esses dados foram corrigidos manualmente, mediante inserção de valores padrão, garantindo a precisão dos cálculos de custo total.

---

### Resultados Obtidos

| Consulta                                     | Resultado                                                                                                             |
| -------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| 4.1 Escritórios e países                     | Wayne Offices (USA), Winterfell Offices (IRE), Stark Industries (ENG), Umbrella Corp (EQU)                            |
| 4.2 Custo total de suprimentos               | Wayne Offices: $376.486.500, Umbrella Corp: $189.792,5, Stark Industries: $149.628,75, Winterfell Offices: $149.142,5 |
| 4.3 Maior quantidade de tipos de suprimentos | Wayne Offices – 5 tipos                                                                                               |
| 4.4 Suprimento mais caro                     | Computadores – $5.000/unidade                                                                                         |
| 4.5 Valor total do inventário de suprimentos | $376.975.063,75                                                                                                       |

---

## Conclusões Alcançadas

* Wayne Offices e Stark Industries concentram os itens de maior valor, impactando significativamente o capital investido em inventário.


* Umbrella Corp apresenta grande volume de suprimentos de baixo custo unitário, resultando em alto valor agregado total.


*  A diversificação de suprimentos está centralizada em alguns escritórios, sugerindo planejamento estratégico concentrado de estoque.


* O custo total elevado evidencia a necessidade de aprimorar o controle de inventário e otimizar o processo de compras.


* As informações obtidas servem de base para priorizar ações de redução de custos, redistribuição de recursos e planejamento de novas unidades.



