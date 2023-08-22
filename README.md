### spring data elasticsearch 활용을 학습 repository

- docs<br>
[join](https://github.com/spring-projects/spring-data-elasticsearch/blob/main/src/main/asciidoc/reference/elasticsearch-routing.adoc#elasticsearch.jointype) : spring의 parent-child

- convention
  [참고1](https://github.com/SpringDataElasticsearchDevs/spring-data-elasticsearch-sample-application/blob/master/src/main/java/org/springframework/data/elasticsearch/entities/OperationDocument.java)<br>
  - document는 domain을 앞에 놓고 뒤에 Document를 붙인다. ex) OperationDocument
  - repository는 domain을 앞에 놓고 뒤에 DocumentRepository를 붙인다. ex)operationDocumentRepsitory


- spring data elasticsearch 적용기 공유
  1. [spring-elasticsearch,kibana사용 with criteria ](https://velog.io/@sujin-create/Spring-ELK-1-Spring-3.0.X-%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C%EC%9D%98-Docker%EB%A5%BC-%ED%99%9C%EC%9A%A9%ED%95%9C-Elastic-Search-Kibana-%EC%82%AC%EC%9A%A9)


-------

### ES에서의 관계형 데이터베이스 모델링 기법

### parent - child 모델링

join data type을 바탕으로 설계하는 방법이다. 하나의 인덱스에서 parent, child 도큐먼트 간에 join type을 의미한다.

[Parent and Child joins with ElasticSearch 7](https://medium.com/swlh/parent-and-child-joins-with-elasticsearch-7-381f6cca73fe)

예를 들어서 content를 저장하는 index에서 그 word_cloud data를 저장할 child document가 있을 수 있다면 이때, Join type으록 구분한다.

[엘라스틱서치[Elasticsearch]에서 관계형 데이터 모델링하기 | 인사이트리포트 | 삼성SDS](https://www.samsungsds.com/kr/insights/elastic_data_modeling.html)

Nested Join 보다 느리다. 

이때, 빠른 검색을 위해서 부모와 자식의 document는 같은 라우팅 id를 키로 사용하며 “같은 shard”에 위치하도록 설계해야 ES의 특징인 빠른 속도를 가지고 간다.

⇒ 해당 방식은 전체 도큐먼트에서 업데이트와 삭제가 빈번하고 1:N 관계의 구조를 가진다면 고려해볼만하다.라고 표현한다.

~~그러나, 이게 맞는 표현인지 의문이 든다.~~

~~음,, 도큐먼트에서 업데이트나 삭제를 할 경우에 재색인이 발생하는데 만약 부모-자식 관계가 깊다면 ex)부모1개에 자식 30개. 부모를 1번 업데이트할 때 자식까지 싹 새로운 segment에 저장하기 때문에 이 경우에는 업데이트가 부모쪽에서 자주일어난다면 비효율적인게 아닌가..?라는 생각이든다.~~

~~하지만 만약에 이러한 부몬쪽에서의 update가 자주 발생하지 않고 자식의 document에서 update가 주로 발생한다면 맞는 말일 것 같다!~~ 

~~아닌가…?~~

이거를 알아보기 위해서는 “parent-child” join에서 parent와 child가 각각 다른 document로 저장되는가를 체크하면 된다.

[6-3. Parent-Child Relationship](https://drscg.tistory.com/62)

** => 부모는 자식을 재색인하지 않고 update한다고한다.!!!**


### nested 모델링

이것은 하나의 인덱스에서 Nested data type을 선언하고 오브젝트 속성을 정의하여 연속하게 배치하는 방식으로 두개이상의 속성을 가진 오브젝트 배열구조로 표현할 때 고려할 수 있다.

다만, parent-child와 다르게 같은 shard안에서 다른 document로 저장되기 때문에 query, aggregation, update시에 유의해야한다.

Nested Type은 내부적으로 Nested Object 별로 저장하기 때문에 Nested가 포함된 문서 하나를 저장할 때 실제 내부적으로는 Nested Array Size + 1만큼 저장하게 됩니다. 따라서 별도의 Nested Query를 작성해야 하고 업데이트 시 이러한 유의 사항을 고려해 디자인 해야 합니다.

⇒ nested join의 문제점과 성능향상

[실시간 인덱싱을 위한 Elasticsearch 구조를 찾아서 | 우아한형제들 기술블로그](https://techblog.woowahan.com/7425/)

### Application Side Join 모델링

기본 키를 기준으로 관계 있는 엔티티들을 서로 다른 도큐먼트에 배치하고 application side에서 키를 기준으로 조인하여 처리하는 방식을 의미한다. 1:1관게의 구조로 기존 쿼리와 집계를 그대로 사용해야할 때 고려해볼 수 있다.

엘라스틱 서치 쿼리를 그대로 사용한다는 장점이 있지만, application side에서 키 값을 기준으로 2회 이상 조회하여 가공해야하기에 페이징 및 정렬 처리를 유의해야한다. 따라서 일반적으로 관계가 있는 단순 조회의 목적일 때만 고려해볼만하다.

### Denormalization 모델링

데이터 전체를 비정규화하여 인덱싱을 진행하는 방법으로 각 데이터의 중복을 허용해서 조인이 필요하지 않도록 설계합니다. 해당 기법은 조인 연산 자체가 필요없기에 쿼리 시점에서 최고의 성능을 낼 수 있다. 그러나 데이터의 크기가 기하급수적으로 증가할 수 있기에 공통 필드의 변경이 잦거나 서버의 저장공간이 적을 때 주의해야한다.
