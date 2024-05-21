

# Exercício 1

Através do uso do Protégé criei a ontologia que se encontra na pasta ex1 com o nome historia.ttl.

As queries pedidas estão definidas no ficheiro queries.txt e são as seguintes:

1. Quantas classes estão definidas na Ontologia?

```
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX : <http://rpcw.di.uminho.pt/2024/historia/>

SELECT (COUNT(?class) AS ?numClasses)
WHERE {
  ?class rdf:type owl:Class .
}
```
2. Quantas Object Properties estão definidas na Ontologia?
```
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX : <http://rpcw.di.uminho.pt/2024/historia/>

SELECT (COUNT(?property) AS ?numObjectProperties)
WHERE {
  ?property rdf:type owl:ObjectProperty .
}
```
3. Quantos indivíduos existem na tua ontologia?
```PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX : <http://rpcw.di.uminho.pt/2024/historia/>

SELECT (COUNT(?individual) AS ?numIndividuals)
WHERE {
  ?individual rdf:type owl:NamedIndividual .
}

```
4. Quem planta tomates?
```
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX : <http://rpcw.di.uminho.pt/2024/historia/>

SELECT ?agricultor
WHERE {
  ?agricultor :cultivaVegetal :Tomate .
}
```
5. Quem contrata trabalhadores temporários?
```
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX : <http://rpcw.di.uminho.pt/2024/historia/>

SELECT ?agricultor
WHERE {
  ?agricultor :contrataTrabalhador :Trabalhador .
}
```



# Exercício 2

Para povoar a ontologia foi criado o ficheiro povoaOntologia.py que se encontra dentro da pasta ex2. Este ficheiro foi usado para gerar todos os outputs pedidos até à alínea 10, basta correr o ficheiro normalmente.

Estes outputs são possíveis através da separação do código com os seguintes parâmetros:

```
# Carregar a ontologia existente
g.parse("../medical.ttl", format="turtle")

...

# Salvar a ontologia atualizada
g.serialize(destination="med_doencas.ttl", format="turtle")
```

```
# Carregar a ontologia existente
g.parse("med_doencas.ttl", format="turtle")

...

# Salvar a ontologia atualizada
g.serialize(destination="med_tratamentos.ttl", format="turtle")

```

```
# Carregar a ontologia existente
g.parse("med_tratamentos.ttl", format="turtle")

...

# Salvar a ontologia atualizada com os doentes e sintomas associados
g.serialize(destination="med_doentes.ttl", format="turtle")

```

## Alínea 11

As queries pedidas nesta alínea encontram-se no ficheiro queries.txt que se encontra dentro da pasta ex2 e são as seguintes:

1. Quantas doenças estão presentes na ontologia?
```
PREFIX : <http://www.example.org/disease-ontology#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

SELECT (COUNT(?disease) AS ?numberOfDiseases)
WHERE {
  ?disease rdf:type :Disease .
}
```
2. Que doenças estão associadas ao sintoma "yellowish_skin"?
```
PREFIX : <http://www.example.org/disease-ontology#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

SELECT ?disease
WHERE {
  ?disease rdf:type :Disease .
  ?disease :hasSymptom :yellowish_skin .
}
```
3. Que doenças estão associadas ao tratamento "exercise"?
```
PREFIX : <http://www.example.org/disease-ontology#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

SELECT ?disease
WHERE {
  ?disease rdf:type :Disease .
  ?disease :hasTreatment :Exercise .
}
```
4. Produz uma lista ordenada alfabeticamente com o nome dos doentes.
```
PREFIX : <http://www.example.org/disease-ontology#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

SELECT ?name
WHERE {
  ?patient rdf:type :Patient .
  ?patient :name ?name .
}
ORDER BY ASC(?name)
```

## Alínea 12

Para esta alínea foi criado o ficheiro ex12.py dentro da pasta ex2 que, através da query CONSTRUCT vai adicionar os triplos à ontologia e guardar esta nova ontologia com o nome med_doentes2.ttl.

A query CONSTRUCT é a seguinte:

```
PREFIX : <http://www.example.org/disease-ontology#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

CONSTRUCT {
  ?patient :hasDisease ?disease .
}
WHERE {
  ?patient rdf:type :Patient .
  ?disease rdf:type :Disease .
  
  ?disease :hasSymptom ?symptom .
  
  FILTER NOT EXISTS {
    ?disease :hasSymptom ?diseaseSymptom .
    FILTER NOT EXISTS {
      ?patient :exhibitsSymptom ?diseaseSymptom .
    }
  }
}
```

## Alínea 13 a 15

Para estas alíneas foram criadas várias queries que também se encontram no ficheiro queries.txt da pasta ex2. As queries criadas foram as seguintes:

* Alínea 13
```
PREFIX : <http://www.example.org/disease-ontology#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

SELECT ?disease (COUNT(?patient) AS ?numberOfPatients)
WHERE {
  ?patient rdf:type :Patient .
  ?patient :exhibitsSymptom ?symptom .
  ?disease :hasSymptom ?symptom .
}
GROUP BY ?disease
ORDER BY DESC(?numberOfPatients)
```
* Alínea 14
```
PREFIX : <http://www.example.org/disease-ontology#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

SELECT ?symptom (COUNT(?disease) AS ?numberOfDiseases)
WHERE {
  ?disease rdf:type :Disease .
  ?disease :hasSymptom ?symptom .
}
GROUP BY ?symptom
ORDER BY DESC(?numberOfDiseases)
```
* Alínea 15
```
PREFIX : <http://www.example.org/disease-ontology#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

SELECT ?treatment (COUNT(?disease) AS ?numberOfDiseases)
WHERE {
  ?disease rdf:type :Disease .
  ?disease :hasTreatment ?treatment .
}
GROUP BY ?treatment
ORDER BY DESC(?numberOfDiseases)
```