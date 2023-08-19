### spring data elasticsearch 활용을 학습 repository

- docs<br>
[join](https://github.com/spring-projects/spring-data-elasticsearch/blob/main/src/main/asciidoc/reference/elasticsearch-routing.adoc#elasticsearch.jointype) : spring의 parent-child

- convention
  [참고1](https://github.com/SpringDataElasticsearchDevs/spring-data-elasticsearch-sample-application/blob/master/src/main/java/org/springframework/data/elasticsearch/entities/OperationDocument.java)<br>
  - document는 domain을 앞에 놓고 뒤에 Document를 붙인다. ex) OperationDocument
  - repository는 domain을 앞에 놓고 뒤에 DocumentRepository를 붙인다. ex)operationDocumentRepsitory


- spring data elasticsearch 적용기 공유
  1. [spring-elasticsearch,kibana사용 with criteria ](https://velog.io/@sujin-create/Spring-ELK-1-Spring-3.0.X-%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C%EC%9D%98-Docker%EB%A5%BC-%ED%99%9C%EC%9A%A9%ED%95%9C-Elastic-Search-Kibana-%EC%82%AC%EC%9A%A9)
