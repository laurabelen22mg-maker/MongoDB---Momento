#  Relatório do Nível 7

##  Descrição

Este relatório apresenta análises avançadas baseadas em agregações no MongoDB, com foco na estrutura e desempenho da empresa Momento.
O objetivo é extrair insights detalhados sobre departamentos, cargos, crescimento de funcionários, ranking de vendedores e produtos exclusivos comercializados por cada vendedor.

## ️ Metodologia

Foram utilizados pipelines de agregação com os operadores $group, $sort, $avg, $sum, $match e $lookup. Essa abordagem permitiu consolidar dados por departamento, identificar colaboradores com salários acima da média, calcular o crescimento anual da empresa, gerar rankings de desempenho de vendedores e localizar produtos vendidos de forma exclusiva por determinados profissionais.

---

##  Informações

###  Consultas Utilizadas

```javascript
// 7.1 Relatório por departamento: nome, nº funcionários, salário total e média salarial
db.departamentos.aggregate([
  {
    $lookup: {
      from: "funcionarios",
      localField: "_id",
      foreignField: "departamento",
      as: "colaboradores"
    }
  },
  {
    $project: {
      _id: 1,
      nome: 1,
      numeroFuncionarios: { $size: "$colaboradores" },
      salarioTotal: { $sum: "$colaboradores.salario" },
      mediaSalarial: { $avg: "$colaboradores.salario" }
    }
  }
]);

// 7.2 3 cargos mais comuns
db.funcionarios.aggregate([
  { $group: { _id: "$cargo", count: { $sum: 1 } } },
  { $sort: { count: -1 } },
  { $limit: 3 }
]);

// 7.3 Funcionários acima da média salarial do seu departamento

const mediaPorDepto = db.funcionarios.aggregate([
  { 
    $group: { 
      _id: "$departamento", 
      mediaSalario: { $avg: "$salario" } 
    } 
  }
]).toArray();

db.funcionarios.aggregate([
  {
    $lookup: {
      from: "departamentos",
      localField: "departamento",
      foreignField: "_id",
      as: "departamentoInfo"
    }
  },
  { $unwind: "$departamentoInfo" },
  {
    $lookup: {
      from: "funcionarios",
      let: { deptId: "$departamento" },
      pipeline: [
        { $match: { $expr: { $eq: ["$departamento", "$$deptId"] } } },
        { $group: { _id: null, mediaSalario: { $avg: "$salario" } } }
      ],
      as: "mediaDepto"
    }
  },
  { $unwind: "$mediaDepto" },
  {
    $match: { $expr: { $gt: ["$salario", "$mediaDepto.mediaSalario"] } }
  },
  { $project: { nome: 1, departamento: "$departamentoInfo.nome", salario: 1, mediaDepto: "$mediaDepto.mediaSalario" } }
]);


// 7.4 Taxa de crescimento por ano (ano de contratação)
db.funcionarios.aggregate([
  { $addFields: { ano: { $year: "$dataAdmissao" } } },
  { $group: { _id: "$ano", novos: { $sum: 1 } } },
  { $sort: { _id: 1 } }
]);

// 7.5 Ranking de vendedores: nome, número de vendas e receita total
db.vendas.aggregate([
  {
    $match: { vendedor: { $exists: true, $ne: null } } // só vendas com vendedor válido
  },
  {
    $group: {
      _id: "$vendedor", 
      numeroVendas: { $sum: 1 },
      receitaTotal: { $sum: { $multiply: ["$quantidade", "$precoUnitario"] } }
    }
  },
  {
    $lookup: {
      from: "funcionarios",
      localField: "_id",
      foreignField: "_id",
      as: "vendedorInfo"
    }
  },
  { $unwind: "$vendedorInfo" },
  {
    $project: {
      nome: "$vendedorInfo.nome",
      numeroVendas: 1,
      receitaTotal: 1
    }
  },
  { $sort: { receitaTotal: -1 } } 
])

// 7.6 Produtos vendidos por apenas um vendedor
db.vendas.aggregate([
  { $group: { _id: "$produto", vendedores: { $addToSet: "$vendedor" } } },
  { $match: { $expr: { $eq: [{ $size: "$vendedores" }, 1] } } }
]);

// 7.7 Extra: Contratação nos departamentos de Marketing, Financeiro e Dados
db.funcionarios.insertMany([
  {
    _id: ObjectId(),
    nome: "Maria Lima",
    telefone: "11999999991",
    email: "mariasabrinalima@momento.org",
    dataAdmissao: ISODate("2025-01-15"),
    cargo: "Analista de Dados",
    salario: 8500,
    departamento: ObjectId("85992103f9b3e0b3b3c1fe76") 
  },
  {
    _id: ObjectId(),
    nome: "Caroline Del Valle",
    telefone: "11999999494",
    email: "c.delvalle@momento.org",
    dataAdmissao: ISODate("2025-02-20"),
    cargo: "Cientista de Dados",
    salario: 9200,
    departamento: ObjectId("85992103f9b3e0b3b3c1fe76")
  },
  {
    _id: ObjectId(),
    nome: "Ana Clara Campos",
    telefone: "11999994583",
    email: "a.campos@momento.org",
    dataAdmissao: ISODate("2025-03-01"),
    cargo: "Analista de Marketing",
    salario: 7800,
    departamento: ObjectId("85992103f9b3e0b3b3c1fe72") 
  },
  {
    _id: ObjectId(),
    nome: "Karina Mendes",
    telefone: "11999999994",
    email: "k.mendes@momento.org",
    dataAdmissao: ISODate("2025-03-05"),
    cargo: "Especialista em Marketing Digital",
    salario: 8200,
    departamento: ObjectId("85992103f9b3e0b3b3c1fe72")
  },
  {
    _id: ObjectId(),
    nome: "Matheus Felipe Paez",
    telefone: "11999929516",
    email: "m.paez@momento.org",
    dataAdmissao: ISODate("2025-03-10"),
    cargo: "Coordenador de Marketing",
    salario: 9000,
    departamento: ObjectId("85992103f9b3e0b3b3c1fe72")
  },
  {
    _id: ObjectId(),
    nome: "Eliza Oliveira",
    telefone: "11999999996",
    email: "eliza.h@momento.org",
    dataAdmissao: ISODate("2025-04-01"),
    cargo: "Analista Financeiro",
    salario: 8800,
    departamento: ObjectId("85992103f9b3e0b3b3c1fe73")
  },
  {
    _id: ObjectId(),
    nome: "Manoel Quintino",
    telefone: "11999992632",
    email: "m.quintino@momento.org",
    dataAdmissao: ISODate("2025-04-05"),
    cargo: "Contador",
    salario: 8700,
    departamento: ObjectId("85992103f9b3e0b3b3c1fe73")
  },
  {
    _id: ObjectId(),
    nome: "Daniela Cruz",
    telefone: "11996939498",
    email: "d.cruz@momento.org",
    dataAdmissao: ISODate("2025-04-10"),
    cargo: "Analista de Planejamento Financeiro",
    salario: 9100,
    departamento: ObjectId("85992103f9b3e0b3b3c1fe73")
  },
  {
    _id: ObjectId(),
    nome: "José Matos",
    telefone: "1199992647",
    email: "j.matos@momento.org",
    dataAdmissao: ISODate("2025-04-15"),
    cargo: "Coordenador Financeiro",
    salario: 9500,
    departamento: ObjectId("85992103f9b3e0b3b3c1fe73")
  }
]);

```

###  Ajustes / Observações

* Foi necessário aplicar $lookup e $unwind em algumas consultas para calcular corretamente as médias salariais por departamento, evitando erros de correspondência entre coleções.

* Realizaram-se conversões de tipo ($toObjectId) em campos de departamento e vendedor, uma vez que alguns registros estavam armazenados como strings.

* Foram feitos ajustes nas datas de contratação, garantindo cálculos precisos de crescimento anual, sem geração de valores nulos.

* Durante a análise, observou-se ausência de funcionários nos departamentos de Marketing, Dados e Finanças, o que motivou a abertura de um processo seletivo em larga escala, resultando na contratação de nove novos colaboradores.

---

### Resultados Obtidos

| Nº  | Consulta                                             | Resultado                                                              |
| --- | ---------------------------------------------------- | ---------------------------------------------------------------------- |
| 7.1 | Departamento: nº funcionários, salário total e média | Executivos (1, $71.100, $71.100), Vendas (10, $95.100, $9.510), Tecnologia (4, $17.600, $4.400), RH (5, $64.080, $12.816), Marketing (3, $25.000, $8.333), Financeiro (4, $36.100, $ 9.025), Inovações (2, $39.966, $19.983 ), Dados(2, $ 17.700,$ 8.850 )        |
| 7.2 | 3 cargos mais comuns                                 | Consultor de Vendas (8), Web Developver (3), Gerente de Estoque (3)          |
| 7.3 | Funcionários acima da média salarial do departamento | 11 funcionários identificados (Pat Ferreira, Bruce Ernst, Diana Lorentz, Matthew Weiss, Payam Kaufling, Normam Osborn, Caroline Del Valle, Matheus Felipe Paez, Victoria Marchan, José Matos e Daniela Cruz.)                                     |
| 7.4 | Crescimento anual da empresa                         | 1994: 1, 1996: 3, 1995: 2, 1999: 1,  2000: 1, 2005: 1, 2008: 1 , 2009: 1, 2010: 1, 2011: 1, 2011: 1, 2019: 2, 2020: 1, 2024: 1.                              |
| 7.5 | Ranking de vendedores                                | Michael Hartstein: 3 vendas, $13.256.17 receita; Jon Deegan: 3 vendas, $4.459 receita; Jenny Tseng: 2 vendas, $4.051|
| 7.6 | Produtos vendidos por apenas um vendedor             |Sentinelas do Bolivar Trask, Uniforme de Moléculas Instáveis, Fake Batarang,         Sabre de Luz (Mace Windu)                                |

---

## Conclusões Alcançadas

* Departamentos com maior número de funcionários e médias salariais elevadas refletem áreas estratégicas de investimento, como Tecnologia, Recursos Humanos e Vendas.

* O ranking de cargos mais recorrentes evidencia uma concentração de talentos em funções essenciais para a operação da empresa.

* A identificação de funcionários com remuneração acima da média departamental fornece subsídios para análises de retenção e valorização de talentos.

* A taxa de crescimento anual destaca períodos de expansão e sazonalidade nas contratações, auxiliando no planejamento de recursos humanos.

* O ranking de vendedores e a análise de produtos exclusivos permitem avaliar desempenho individual e identificar oportunidades de mercado.

* A recente contratação de novos talentos contribui para o crescimento e fortalecimento organizacional; contudo, é recomendável manter um equilíbrio constante entre os departamentos, evitando carências futuras de pessoal.