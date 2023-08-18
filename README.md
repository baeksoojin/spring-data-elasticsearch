### spring data elasticsearch 활용을 학습 repository

- docs<br>
[join](https://github.com/spring-projects/spring-data-elasticsearch/blob/main/src/main/asciidoc/reference/elasticsearch-routing.adoc#elasticsearch.jointype) : spring의 parent-child

- convention

[참고1](https://github.com/SpringDataElasticsearchDevs/spring-data-elasticsearch-sample-application/blob/master/src/main/java/org/springframework/data/elasticsearch/entities/OperationDocument.java)<br>

- document는 domain을 앞에 놓고 뒤에 Document를 붙인다. ex) OperationDocument
- repository는 domain을 앞에 놓고 뒤에 DocumentRepository를 붙인다. ex)operationDocumentRepsitory


