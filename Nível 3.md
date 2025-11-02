#  Relatório do  Nível 3

##  Descrição

Este relatório apresenta uma análise detalhada do perfil demográfico e histórico dos colaboradores da empresa Momento, abrangendo informações sobre dependentes, tempo de serviço, períodos de contratação e variações salariais. O objetivo é oferecer ao setor de Recursos Humanos dados estratégicos para apoiar a gestão de pessoal, definição de benefícios e planejamento de sucessão.

##  Metodologia

Foram utilizadas consultas básicas (find()) e operações de agregação com o uso de $count e projeções ($project) para investigar dependentes, datas de admissão e tempo de casa.
Essa abordagem permitiu identificar padrões de contratação, distribuição familiar e níveis de experiência, fornecendo insumos valiosos para tomadas de decisão estratégicas em gestão de pessoas.

---

## Informações

###  Consultas Utilizadas

```javascript
// 3.1 Quantos funcionários possuem cônjuges?
db.funcionarios.countDocuments({ "dependentes.conjuge": { $exists: true } });

// 3.2 Quantos funcionários possuem filhos registrados?
db.funcionarios.countDocuments({ "dependentes.filhos": { $exists: true } });

// 3.3 Funcionário contratado há mais tempo
 db.funcionarios.find().sort({ dataAdmissao: 1 }).limit(1);

// 3.4 Funcionário contratado há menos tempo
 db.funcionarios.find().sort({ dataAdmissao: -1 }).limit(1);

// 3.5 Cinco funcionários com mais tempo de casa
 db.funcionarios.find().sort({ dataAdmissao: 1 }).limit(5);

// 3.6 Funcionários contratados na década de 1990
 db.funcionarios.countDocuments({ dataAdmissao: { $gte: ISODate("1990-01-01"), $lte: ISODate("1999-12-31") }});

// 3.7 Evolução da média salarial por ano de contratação
 db.funcionarios.aggregate([
   { $group: { _id: { $year: "$dataAdmissao" }, mediaSalarial: { $avg: "$salario" } } },
   { $sort: { "_id": 1 } }
]);
```

###  Ajustes / Observações

Foi necessário complementar datas de admissão em registros incompletos, evitando inconsistências nas consultas de ordenação (sort) e nas agregações por período.

---

###  Resultados Obtidos

| Nº  | Consulta                                   | Resultado                                                                 |
| --- | ------------------------------------------ | ------------------------------------------------------------------------- |
| 3.1 | Funcionários com cônjuge                   | **7 colaboradores**                                                       |
| 3.2 | Funcionários com filhos                    | **7 colaboradores**                                                       |
| 3.3 | Funcionário contratado há mais tempo       | **Irene Adler (1980-09-28)**                                              |
| 3.4 | Funcionário contratado há menos tempo      | **Victoria Marchan (2024-04-12)**                                         |
| 3.5 | Cinco funcionários com mais tempo de casa  | Jennifer Whalen, Irene Adler, Den Raphaely, Normam Osborn, Payam Kaufling |
| 3.6 | Funcionários contratados na década de 1990 | **12 colaboradores**                                                      |
| 3.7 | Evolução média salarial por ano            | Tabela abaixo                                                             |

**3.7 Evolução na média salarial:**

| Ano  | Média Salarial |
| ---- | -------------- |
| 1987 | $9.700         |
| 1987 | $23.000        |
| 1994 | $12.500        |
| 1995 | $13.840        |
| 1996 | $30.966        |
| 1997 | $13.420        |
| 1999 | $8.900         |
| 2000 | $4.100         |
| 2005 | $3.400         |
| 2008 | $3.500         |
| 2009 | $2.900         |
| 2010 | $6.000         |
| 2024 | $5.000         |

---

##  Conclusões Alcançadas

* Uma proporção expressiva de colaboradores possui dependentes, evidenciando a importância de políticas voltadas ao bem-estar familiar e benefícios inclusivos.

* A empresa mantém profissionais com ampla experiência, incluindo contratações antigas ainda em atividade, o que reforça o capital intelectual e a continuidade organizacional.

* A média salarial apresentou crescimento entre os cargos executivos, enquanto as áreas técnicas mantêm remuneração estável e equilibrada.

* O setor de RH dispõe agora de base sólida para planejar sucessão, capacitações e integração de novos talentos, promovendo continuidade operacional e retenção de conhecimento.
