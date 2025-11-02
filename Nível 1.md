
# Relatório do Nível 1

##  Descrição

Este relatório tem como objetivo apresentar uma visão geral da estrutura organizacional da empresa Momento, abrangendo o número total de funcionários, a distribuição por departamentos e escritórios, além de destacar os principais setores em atividade.

* Os tópicos analisados incluem:

* Número total de funcionários

* Distribuição por departamento

* Localização e quantidade de escritórios

* Principais setores existentes

Essas informações constituem a base para os relatórios subsequentes, como Análise Financeira, Recursos Humanos e Operações, oferecendo uma visão consolidada do cenário organizacional da empresa.

##  Metodologia

Para o desenvolvimento deste relatório, foram utilizadas consultas básicas no MongoDB Compass, com o uso dos comandos find(), countDocuments() e distinct(), a fim de extrair informações gerais das coleções funcionarios, departamentos e escritorios.

Essas consultas possibilitaram identificar rapidamente o número total de colaboradores, a distribuição por departamentos e a localização dos escritórios, assegurando que o relatório apresentasse dados atualizados, consistentes e confiáveis sobre a estrutura da empresa.

---

##  Informações

###  Consultas Utilizadas

```js
// 1.1 db.funcionarios.insertOne({
  nome: "Laura",
  sobrenome: "Belen",
  data_nascimento: ISODate("2007-07-26T00:00:00Z"),
  data_contratacao: ISODate("2024-04-12T00:00:00Z"),
  salario: 5000,
  departamento: "Tecnologia",
  cargo: "Desenvolvedora",
  escritorio: "São Paulo"
});

// 1.2 Quantos funcionários temos ao total na empresa?
db.funcionarios.countDocuments();

// 1.3 Quantos funcionários trabalham especificamente no Departamento de Tecnologia?
db.funcionarios.countDocuments({ departamento: ObjectId("85992103f9b3e0b3b3c1fe74") });

// 1.4 Liste todos os departamentos que existem na empresa. Quantos são?
db.departamentos.distinct("nome");
db.departamentos.countDocuments();

// 1.5 Quantos escritórios a Momento possui? Em quais países?
db.escritorios.find({}, { nome: 1, pais: 1, _id: 0 });
```

---

###  Resultados Obtidos

| Consulta                                           | Resultado                                                                                                  |
| -------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| **1.2** Total de Funcionários                      | 24 funcionários ativos                                                                                     |
| **1.3** Funcionários no Departamento de Tecnologia | 6 profissionais                                                                                            |
| **1.4** Departamentos Existentes                   | Executivos, Vendas, Tecnologia, Recursos Humanos, Marketing, Finanças, Dados                          |
| **Total de Departamentos**                         | 7                                                                                                          |
| **1.5** Escritórios e Países                       | Wayne Offices (EUA), Winterfell Offices (Irlanda), Stark Industries (Reino Unido), Umbrella Corp (Equador) |
| **Totais**                                         | 4 escritórios em 4 países                                                                                  |

---

### Ajustes / Observações

Durante a execução das consultas, foi necessário verificar o ObjectId correto do Departamento de Tecnologia, associado à funcionária Victoria Marchan, para evitar retornos nulos em countDocuments().
Também se observou que alguns nomes de departamentos possuíam variações de capitalização (inicial maiúscula), o que exigiu padronização para garantir consistência nas consultas.
Após esses ajustes, não foram identificados erros de execução.

---

##  Conclusões Alcançadas

* A empresa Momento apresenta uma estrutura internacional consolidada, com escritórios distribuídos em quatro países e sete departamentos ativos.

* O Departamento de Tecnologia destaca-se como um dos maiores, refletindo o comprometimento da empresa com inovação e transformação digital.

* A diversificação dos setores demonstra que a Momento é uma organização madura e em expansão, preparada para sustentar operações em escala global.

* As informações coletadas fornecem a base essencial para os relatórios de Análise Financeira, Recursos Humanos e Operações a serem desenvolvidos nas próximas etapas.

