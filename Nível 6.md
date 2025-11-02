# Relátorio do Nivel 6

##  Descrição

Este relatório aborda atualizações estratégicas na estrutura organizacional, envolvendo a criação de novos setores, realocação de colaboradores, ajustes remuneratórios, promoções internas e gestão de materiais. O propósito é representar o avanço da empresa e manter o banco de dados devidamente atualizado.

##  Metodologia

As instruções SQL foram aplicadas para realizar as modificações de forma eficiente e segura:

Criação do setor "Inovações" com insertOne

Transferência de dois colaboradores do departamento de Tecnologia para Inovações com updateMany

Reajuste de 10% nos salários da área de Tecnologia utilizando $mul

Promoção de Bruce Ernst por meio de updateOne

Adição de headsets ao inventário do escritório Wayne Offices com $push

Exclusão de funcionários admitidos antes de 1990 através de deleteMany, após conferência com find()

---

##  Informações

###  Consultas

```javascript
// 6.1 Criar novo departamento "Inovações" no escritório "Wayne Offices"
db.departamentos.insertOne({
  nome: "Inovações",
  escritorio: ObjectId("5f8b3f3f9b3e0b3b3c1e3e3e")
});

// 6.2 Transferir 2 funcionários de Tecnologia para Inovações
db.funcionarios.updateOne(
  { nome: "Diana Lorentz" },
  {
    $set: {
      departamento: ObjectId("68efd0b5a8a7bd89c14eb64f"),
      cargo: "Diretora de Inovação",
      salario: 20434
    }
  }
);

db.funcionarios.updateOne(
  { nome: "Valli Stark" },
  {
    $set: {
      departamento: ObjectId("68efd0b5a8a7bd89c14eb64f"),
      cargo: "Líder de Projeto de IA",
      salario: 19532
    }
  }
);

// 6.3 Aumentar 10% dos salários do departamento de Tecnologia
db.funcionarios.updateMany(
  { departamento: ObjectId("85992103f9b3e0b3b3c1fe74") },
  { $mul: { salario: 1.1 } }
);

// 6.4 Promover Bruce Ernst
db.funcionarios.updateOne(
  { nome: "Bruce Ernst" },
  { $set: { cargo: "Senior Web Developer", salario: 5000 } }
);

// 6.5 Adicionar novo suprimento "Headsets" no escritório Wayne Offices
db.escritorios.updateOne(
  { nome: "Wayne Offices" },
  { $push: { suprimentos: { produto: "Headsets", quantidade: 15, precoUnitario: 150 } } }
);

// 6.6 Remover funcionários contratados antes de 1990
db.funcionarios.find({ dataAdmissao: { $lt: ISODate("1990-01-01") } });
db.funcionarios.deleteMany({ dataAdmissao: { $lt: ISODate("1990-01-01") } });
```

###  Ajustes / Observações

Foi necessário verificar os registros dos funcionários antes da exclusão para evitar remoções equivocadas.
O ID do novo setor Inovações precisou ser definido manualmente durante a transferência de colaboradores.
As datas de admissão foram revisadas para garantir consistência no formato.

### 📊 Resultados Obtidos

| Nº  | Ação                          | Resultado                                           |
| --- | ----------------------------- | --------------------------------------------------- |
| 6.1 | Novo departamento             | **Inovações** no Wayne Offices, 0 funcionários      |
| 6.2 | Transferência de funcionários | 2 funcionários de Tecnologia movidos para Inovações |
| 6.3 | Aumento do salario            | Funcionários de Tecnologia receberam **+10%**       |
| 6.4 | Promoção individual           | Bruce Ernst: **Senior Web Developer**, $5.000       |
| 6.5 | Novo suprimento               | Headsets adicionados: 15 unidades, $150 cada        |
| 6.6 | Aposentadoria                 | Funcionários contratados antes de 1990 removidos    |

---

## Conclusões Alcançadas

* A implantação do setor Inovações fortalece a área de pesquisa e desenvolvimento, evidenciando expansão estratégica.
* A realocação dos colaboradores Valli Stark e Diana Lorentz assegura que profissionais qualificados atuem onde são mais necessários.
* Os reajustes salariais e promoções demonstram políticas de valorização e incentivo ao time.
* A inclusão de novos equipamentos garante que o escritório mantenha infraestrutura moderna para suas operações.
* A remoção de registros antigos contribui para manter o banco de dados organizado e coerente com a atual estrutura corporativa.
