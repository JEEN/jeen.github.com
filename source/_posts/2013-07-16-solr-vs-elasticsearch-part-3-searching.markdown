---
layout: post
title: "[번역] Solr vs. ElasticSearch - Part 3 : Searching"
date: 2013-07-16T22:32:49+09:00
comments: true
external-url: 
categories: [ translation, solr, elasticsearch ] 
---

#Solr vs ElasticSearch: Part 3 – Searching

October 1, 2012 by Rafał Kuć

In the last two parts of the series we looked at the general architecture and how data can be handled in both Apache Solr 4 (aka SolrCloud) and ElasticSearch and what the language handling capabilities of both enterprise search engines are like. In today’s post we will discuss one of the key parts of any search engine – the ability to match queries to documents and retrieve them.

이 시리즈의 앞의 2회에서 Apache Solr 4 (SolrCloud라고도 불립니다）와 ElasticSearch 의 전체 아키텍쳐와 데이터가 어떻게 다뤄지는 지, 그리고 두 검색엔진의 언어 취급능력이 어떤 지를 알아봤습니다. 이번 글에서는 검색 엔진의 키가 되는 부분 중 하나인 도큐먼트에 대한 쿼리를 매치해서 결과를 받는 능력에 대해서 의논해보도록 합니다.

- [1. Solr vs. ElasticSearch: Part 1 – Overview](http://jeen.github.io/2013/07/15/solr-vs-elasticsearch-part-1/)
- [2. Solr vs. ElasticSearch: Part 2 – Data Handling](http://jeen.github.io/2013/07/16/solr-vs-elasticsearch-part-2-indexing-and-language-handling/)
- [3. Solr vs. ElasticSearch: Part 3 – Searching](http://jeen.github.io/2013/07/16/solr-vs-elasticsearch-part-3-searching/)
- [4. Solr vs. ElasticSearch: Part 4 – Faceting](http://jeen.github.io/2013/07/16/solr-vs-elasticsearch-part-4-faceting/)
- [5. Solr vs. ElasticSearch: Part 5 – Management API Capabilities](http://jeen.github.io/2013/07/17/solr-vs-elasticsearch-part-5-management-api-capabilities/)
- [6. Solr vs. ElasticSearch: Part 6 – User & Dev Communities Compared](http://jeen.github.io/2013/07/17/solr-vs-elasticsearch-part-6-user-and-dev-communities-compared/)

##전체적인 어프로치

Both search engines expose their search APIs via HTTP. If you are not familiar with Solr or ElasticSearch, here are a few simple examples of what Apache Solr and ElasticSearch queries look like:

두 검색엔진 모두 HTTP 경유로 검색 API 를 공개하고 있습니다. Solr 와 ElasticSearch 를 잘 모르는 분을 위해, 여기에서 몇가지 Apache Solr 와 ElasticSearch 의 쿼리가 어떤지를 간단한 예로 보여드립니다.

###Solr
	curl -XGET 'http://localhost:8983/solr/sematext/select?q=post_date:[2012-09-10T12:00:00Z+TO+2012-09-10T15:00:00Z]'

###ElasticSearch

	curl -XGET http://localhost:9200/sematext/_search?pretty=true -d '{
	    "query" : {
	        "range" : {
	            "post_date" : {
	                "from" : "2012-09-10T12:00:00",
	                "to" : "2012-09-10T15:00:00"
	            }
	        }
	    }
	}'

As you can see the ElasticSearch query is more structured allowing for more precise control of what you are trying to get – similar to Lucene queries. Solr on the other hand uses a query parser to parse your query out of the textual value of the “q” URL parameter (n.b. you can use query parser in ElasticSearch too). But as one can see on Solr mailing lists, many new users have problems because of such approach – they are overwhelmed with all the options and parameters. At the same time, Solr does make simple queries with boosting and extended dismax parser very easy to do, although that comes at a price. If you want to have a higher degree of control over your query, you are (in most cases) forced to use local params that, while powerful, can be quite hard for users not familiar with its cryptic syntax.

보시는 대로, ElasticSearch의 쿼리는 보다 구조화되어 원하는 결과를 얻기위해 정확한 컨트를을 가능하게 합니다. Lucene 쿼리와 비슷합니다. 반면 Solr 는 쿼리파서를 URL 파라메터의 `q` 값으로 넣은 텍스트 값을 쿼리로 파싱합니다. (주의: ElasticSearch 에서도 쿼리파서를 이용할 수 있습니다) 하지만, Solr 의 메일링리스트에서 확인할 수 있듯이 많은 유저가 그런 방법으로 인해 문제를 겪고 있습니다. 그들은 모든 옵션과 파라메터에 매우 난감해하고 있습니다. 동시에 Solr 는 간단한 쿼리를 boosting 과 확장된 dismax 파서로 간단하게 실행할 수 있습니다. 하지만 그에 상응하는 부담을 지고 있습니다. 만약 고도의 컨트롤을 쿼리로 할 경우에는 (대부분의 경우) 로컬 파라메터를 사용하지 않으면 안되며, (파워풀하지만) 그 암호같은 문법에 익숙하지 않은 유저에게는 매우 큰일이 아닐 수 없습니다.

To sum up our short introduction – both search engines give you a similar degree of control when it comes to querying, although if you want to create your queries from scratch and control every aspect of them, just as you would when using Lucene directly, ElasticSearch is the way to go, not because Solr doesn’t let you, but the structured JSON way of querying ElasticSearch is a better fit in that case and feels more intuitive.

우리들의 짧은 소개를 정리하면, 두 검색엔진이 같은 정도의 컨트롤을 쿼리로 제공합니다. 그러나 스크래치부터 쿼리를 만들고, Lucene 을 직접 이용하도록 모든 국면을 컨트롤하고 싶은 경우, ElasticSearch 를 선택해야 합니다. Solr 에서는 불가능한 것은 아니지만, ElasticSearch 가 제공하는 구조화된 JSON 이 그런 케이스에 보다 안성맞춤이고 보다 직감적으로 느낄 수 있기 때문입니다.

##전문검색

In this section we try to compare search capabilities of both both Apache Solr and ElasticSearch. This is by no means a comprehensive tutorial of all the features that both search engines expose, but rather  a simple comparison of similarities and difference of them.

이 섹션에서는 Apache Solr 와 ElasticSearch 모두 검색능력을 비교해 봅니다. 물론 포괄적으로 두 검색엔진이 가지는 모든 기능의 튜토리얼이 아닙니다. 콕 찝어서 말하자면 간단한 유사점과 차이점의 비교가 아닐까 하네요.

###검색

Of course, both Apache Solr and ElasticSearch enable you to run standard queries such as Boolean queries, phrase queries, fuzzy queries, wildcard queries, etc. You can combine them into multiple Boolean phrases using Boolean operators. In addition to that, both engine let one specify query-time boosts and control how score is calculated during search execution.

물론 Apahe Solr 와 ElasticSearch 양쪽 모두 표준적인 검색이 가능합니다. 불리언 쿼리, 프레이즈 쿼리, 퍼지 쿼리, 와일드 카드 쿼리 같은 것들 말이죠. 불리언연산자를 사용해서 이것들을 결합해서 여러 불리언 프레이즈에 결합할 수도 있습니다. 물론 두 검색엔진은 쿼리 마다 boost 를 지정하고, 검색실행 중에 어떻게 스코어가 계산되는 지를 컨트롤할 수 있습니다.

###Span Queries

If you are not familiar with span queries here is a one-sentence description: Lucene provides span queries in order to enable searching documents with position requirements, but not necessarily appearing one after another like in the phrase query. And now the comparison:

스팬쿼리를 모르는 사람을 위해 간단하게 설명하자면, Lucene 이 제공하는 스팬쿼리는 위치를 필요로하는 도큐먼트 검색을 가능하게 합니다. 그러나 프레이즈쿼리처럼 연속해서 나올 필요는 없습니다. 한번 비교해볼까요?

####Solr

Update: As Erik Hatcher noticed support for span queries is already there in Apache Solr (SOLR-2703). We can use span queries by using the surround query parser.

업데이트： Erik Hatcher에 의해서 스팬쿼리는 이미 Apache Solr 에 들어갔습니다.(SOLR-2703) 쿼리파서로 감싸서 스팬쿼리를 사용할 수 있습니다.

####ElasticSearch

ElasticSearch has the support for Lucene SpanNearQuery, SpanFirstQuery, SpanTermQuery, SpanOrQuery and SpanNotQuery. With these queries you can construct different span queries similar to what you can do with Lucene.

ElasticSearch는 Lucene 의 SpanNearQuery, SpanFristQuery, SpanTermQuery, SpanOrQuery, 그리고 SpanNotQuery를 지원합니다. 이 쿼리들을 사용해서 Lucene 에서 이뤄지는 다양한 스팬쿼리를 구성할 수 있습니다.

###More Like This

“More like this” (aka MLT) functionality lets you to get documents similar to a given query according to some assumptions and parameters used to find documents that are similar to one another. Both search engines have the ability to run MLT queries. In Solr, MLT  query is implemented as a search component. On the other hand there is ElasticSearch where more like this is just another type of query one can construct using JSON. When comparing parameters available in both search servers it seems that ElasticSearch provides slightly more control over more like this functionality with features like specifying a set of words that shouldn’t be taken into consideration and the percentage of terms to match on.

"More like this"(또는 MLT) 란, 쿼리에서 부여된 도큐먼트에 몇가지 파라메터를 이용해 비슷한 도큐먼트를 얻을 수 있는 기능입니다. 차이가 있는 도큐먼트를 찾기 위해서 파라메터가 사용됩니다. 두 검색엔진 모두 MLT 쿼리를 사용할 수 있습니다. Solr 에서는 MLT 쿼리는 검색 컴포넌트로 구현되어 있습니다. 반면, ElasticSearch 에서는 JSON 을 사용해서 구축하는 한 종류의 쿼리가 됩니다. 두 검색 서버에 존재하는 파라메터를 비교하면 ElasticSearch 는 조금 더 많은 컨트롤을 제공합니다. 예를들어 고려하지 않은 단어의 집합을 지정하거나, 매치하는 항목의 분할을 지정할 수 있습니다.

###Did You Mean

“Did you mean” (aka DYM) functionality makes it possible to correct users’ query typos and spelling mistakes and suggest corrected queries. For example, for a misspelled phrase “saerch problems” our Researcher module on http://search-lucene.com (which is a kind of a did you mean module) works like this:

"Did you mean"（또는 DYM） 이란 유저의 쿼리의 타이핑오류나 스펠링오류를 고치고 정정된 쿼리를 제안하는 기능입니다. 예를들어 "saerch problems" 라는 잘못된 스펠 구문에서 [http://search-lucene.com](http://search-lucene.com) 에 존재하는 우리 연구자 모듈(Did you mean 모듈과 같은 것입니다) 은 이런 동작을 하게 됩니다.

Let’s see what Solr and ElasticSearch have to offer here.

Solr 와 ElasticSearch 에 어떤 기능이 제공되는지 확인해봅시다.

####Solr

Solr exposes spell check component API, which is built on top of Lucene spell checker module. Before Solr 4.0 the spell checker required its own index that, while built automatically by Solr, was another moving piece and potential inconvenience.  Now there is a DirectSolrSpellchecker implementation available which can give spell checker suggestion based on the index you are using for search instead of relying on the side-car spell checker index. Solr spell checker supports distributed search and has numerous parameters which allow control over its behavior, like number of suggestion, collation properties, accuracy, etc.

Solr 는 스펠링체크 컴포넌트 API 를 제공합니다. 이는 Lucene 의 스펠체커 모듈 위에 구축되어 있습니다. Solr 4.0 이전에는 스펠체커 자체가 인덱스를 필요로 하고, Solr 에 의해 자동적으로 구축되었지만, 별도로 동작하는 부품으로는 조금 불편한 것이었습니다. 지금은 DirectSolrSpellchecker 구현이 존재하며, 검색에 사용하고 있는 인덱스에 기초하여 스펠체커가 제안할 수 있습니다. 스펠체커용의 인덱스를 필요로하지 않습니다. Solr 의 스펠체커는 분산검색을 지원하고, 많은 파라메터를 가진 것으로 그 동작을 컨트롤할 수 있습니다. 예를들어 제안 수나 조합 속성이나 정확성등입니다.

####ElasticSearch

Unfortunately, ElasticSearch doesn’t offer did you mean functionality out of the box. There is issue  #911 currently open, so we can expect that module in one of the future releases. Although we’ll be talking about plug-ins in the last part of the Solr vs ElasticSearch series, if you need did you mean functionality in ElasticSearch you can use the Suggest Plugin developed by @spinscale (https://github.com/spinscale/elasticsearch-suggest-plugin).

아쉽게도 ElasticSearch는 "Did you mean" 기능을 그대로 제공하지 않습니다. Issue#911이 현재 오픈되어 있고, 앞으로의 릴리즈에서 그 모듈을 기대할 수 있겠습니다. Solr ElasticSearch 의 마지막 파트에서 플러그인에 대해서 다룰 예정이지만, 만약 "Did you mean" 기능을 ElasticSearch 에서 필요로 한다면 @spinscale 이 개발한 Suggest 플러그인을 이용할 수 있습니다.([https://github.com/spinscale/elasticsearch-suggest-plugin](https://github.com/spinscale/elasticsearch-suggest-plugin)).

###Nested Queries

As we already wrote, ElasticSearch supports indexing of nested document which Solr doesn’t support. In order to query nested documents ElasticSearch exposes nested query type. This query is run against nested documents, but as the result we get the root documents. In addition to that, you can also set how scoring of the root document is affected.

이미 다룬 적이 있지만, ElasticSearch 는 중첩 도큐먼트의 인덱싱을 지원합니다. Solr 는 지원하지 않습니다. 중첩도큐먼트를 쿼리하기 위해서는 ElasticSearch 는 중첩쿼리타입을 공개하고 있습니다. 이 쿼리는 중첩도큐먼트에 대해서 실행되지만 결과로 루트 도큐먼트를 얻게 됩니다. 그리고 루트도큐먼트의 스코어링에 어떻게 영향받는지도 설정할 수 있습니다.


###Parent – Child Relationship Queries
####Solr

In Apache Solr there is no functionality called parent - child, instead of that we have the possibility to use joins. Solr joins are specified in local params format and look like this:

Apache Solr 에서는 부모자식이라고 불리는 기능은 없습니다. 대신에 join 을 사용하는 방법이 있습니다. Solr 의 join 은 로컬파라메터 형식으로 지정되어 아래처럼 사용할 수 있습니다.

	q={!join from=parent to=id}color:Yellow

The above query says that we want to get all parent documents that have child documents that have the Yellow term in the color field. The join should be done on parent field in the children to the id field in the parent document.

위 쿼리에서 color 필드에 Yellow 를 가진 자식 도큐먼트를 가진 모든 부모 도큐먼트를 얻습니다. join 은 자식 도큐먼트의 parent 필드에서 부모 도큐먼트의 id필드상에서 이뤄집니다.

####ElasticSearch

ElasticSearch lets you use two type of queries – has_children and top_children queries to operate on child documents. The first query accepts a query expressed in ElasticSearch Query DSL as well as the child type and it results in all parent documents that have children matching the given query. The second type of query is run against a set number of children documents and then they are aggregated into parent documents. We are also allowed to choose score calculation for the second query type.

ElasticSearch는 두가지 타입의 쿼리를 사용할 수 있습니다. has_children 과 top_children 쿼리는 자식 도큐먼트 위에서 명령됩니다. 최초의 쿼리는 ElasticSearch Query DSL 또는 child타입으로 표현된 쿼리를 받아서, 부여된 쿼리에 매치하는 도큐먼트를 가지는 모든 부모 도큐먼트를 결과로 합니다. 두번째 타입의 쿼리는 몇가지 자식 도큐먼트의 집합에 대해서 실행되고 그것들은 부모 도큐먼트 안에서 집약됩니다. 두번째의 쿼리타입에 대해서도 스코어 연산을 선택할 수 있습니다.

###Filtering And Caching Control
####Solr

Of course Solr lets you to narrow results of your query execution with filters. You can filter documents based on a single value, Boolean expression, query, field existence, geographical location and many, many more. In addition to that you can use local params and construct complicated queries like:

물론 Solr 는 쿼리의 실행결과를 필터로 제한할 수 있습니다. 단일 값, 불리언식, 필드의 존재, 지리상의 위치, 그리고 가장 많은 것을 기준으로 해서 도큐먼트를 필터링할 수 있습니다. 그리고 로컬파라메터를 사용해서 복잡한 쿼리를 구성할 수 있습니다.

예:

	fq={!frange l=10 u=30}if(exists(promotionPrice),sum(promotionPrice,dailyPrice),sum(price,dailyPrice))

####ElasticSearch

ElasticSearch, similar to Solr, lets you use many filter types, which are similar to filters, so we’ll skip mentioning them all. However, in addition to similarities with Solr, there are also some differences like supports for filters run against nested documents and children documents. ElasticSearch can also use scripts to filter documents with the script filter.

ElasticSearch는 Solr 와 비슷하게 많은 필터타입을 사용할 수 있습니다. 필터와 마찬가지이기에 따로 말할 필요도 없습니다. 그러나 Solr 에 대한 유사성 이외에 몇가지 차이점이 존재합니다. 예를들어 중첩 도큐먼트나 자식 도큐먼트에 대해 실행하는 필터를 지원하는 것입니다. 또한 ElasticSearch 는 스크립트 필터를 사용해서 도큐먼트의 필터링에 스크립트를 사용할 수도 있습니다.

###Filter Cache Control
Both ElasticSearch and Apache Solr can control if the filter should or shouldn’t be cached, but in addition to that Solr lets you control the order of filters execution (the non cached ones). Its a great feature of Solr, because if you know that one of your filters is a performance killer, you can set its execution after all other filters and that way it’ll only work on the subset of the original result set.

ElasticSearch 와 Apache Solr 양쪽 모두 필터가 캐쉬되어야하는지 그렇지 않는 지를 설정할 수 있습니다. Solr 는 필터의 실행순서를 조정할 수 있습니다(캐쉬되지 않은 것만). 이는 Solr 의 뛰어난 기능으로 만약 당신이 필터 하나가 퍼포먼스에 나쁜 영향을 주는 것을 이미 알고 있다면 그 필터의 실행의 순서를 뒤로 미루도록 설정할 수 있습니다. 그렇게 함으로써 그 필터는 오리지널의 실행결과의 부분집합만을 대상으로 실행됩니다.

###Score Calculation Control

In both engines we are more or less allowed to control how scores for documents are calculated. In Solr this is mostly done by using function queries and different boosts and queries made using local params. In ElasticSearch we can use different query types which allow us to give specific scores to some of the documents (for example ones matching a certain filter) or calculate score on the basis of used script.

두 엔진에 있어서 많든 적든 도큐먼트에 대해서 스코어가 어떻게 계산할 것인가를 컨트롤할 수 있습니다. Solr 에서는 대부분의 경우에 있어서 Function Queries 가 복수의 boost 를 사용해서 이뤄지고, 쿼리는 로컬파라메터를 사용해서 만들어집니다. ElasticSearch 에서는 다른 쿼리타입을 사용해서 어떤 도큐먼트에 대해서 특정 스코어를 줄 수도 있습니다(예를들어 몇가지 필터에 매치하는 도큐먼트). 또는 사용한 스크립트에 따라 스코어를 계산할 수도 있습니다.

###Real Time Get

Real time get allows us to retrieve a document using its identifier as soon as it was sent for indexing even if it hasn’t yet been hard committed. Both ElasticSearch and Apache Solr return the newest document, even if it wasn’t indexed. But lets go into specifics.

Real Time Get 은 도큐먼트 ID 를 사용해서 인덱싱을 위해서 보내졌을 때, 설령 하드커밋이 이뤄지지 않았어도 도큐먼트 회수를 가능하게 합니다. ElasticSearch 와 Apache Solr 모두 최신 도큐먼트를 인덱스되지 않아도 반환 할 수 있습니다. 이에 대해서 좀 더 자세히 볼까요.

####Solr

Introduction of so called transaction log in Solr 4.0 allowed for the real time get functionality. Basically, the real time get looks for the newest version of the document in the transaction log first and returns it as a result of such call (if it is found, of course). If it is not found the real time get handler gets the document using the latest opened searcher available. Keep in mind that in order to return the newest version of the document in near real time manner Solr doesn’t need to reopen the index after indexing, so this functionality is useful even if you don’t reopen your searcher every second.

Solr4.0에서 트랜잭션 로그라고 불리는 기능으로 Real Time Get 을 이용할 수 있게 되었습니다. 기본적으로는 Real Time Get 은 도큐먼트의 최신 버젼을 트랜잭션 로그에서 맨처음 찾아서 결과로 반환합니다(물론 찾은 경우에). 혹시라도 못 찾았다면 Real Time Get 핸들러는 가장 최근에 열린 searcher 가 존재하는 도큐먼트를 반환합니다. 기억해두었으면 하는 것은 거의 리얼타임 의 최신 버젼의 도큐먼트를 반환하기 위해 Solr 는 인덱싱 뒤에 인덱스를 다시 열 필요는 없습니다. 따라서 이 기능은 searcher 를 매초 다시 열지 않아도 유효합니다.

####ElasticSearch

ElasticSearch also uses transaction log and because of that the real time get is not affected by the refresh rate of your indices. In addition to returning the document itself ElasticSearch exposes a few other API parameter that allow you to specify if the request should go to the primary or local shard (or even a custom one). You can also use routing with real time get to route the request to one specific shard if you know which shard should have the appropriate document. The real time get API of ElasticSearch also allows to check if the document exists using HTTP HEAD method, for example:

ElasticSearch 도 트랜잭션로그를 사용하기 때문에 Real Time Get 은 인덱스의 리프레쉬율에 영향을 받지 않습니다. 도큐먼트를 반환하기 윙해 ElasticSearch 는 몇가지 다른 API 파라메터를 공개하고 그것을 사용 함으로써 질의가 프라이머리인지 또는 로컬의 Shard 로 (또는 커스텀 Shard 에만) 보낼지에 대한 것을 지정할 수 있습니다. 또 라우팅을 Real Time Get 과 함께 사용함으로 어느 shard 가 적절하게 도큐먼트를 가지고 있는지 알고 있는 경우에 질의를 하나의 특정 shard 로 유도할 수 있습니다. ElasticSearch 의 Real Time Get API 는 도큐먼트가 존재하는지 어떤지를 HTTP HEAD 메소드로 확인할 수 있습니다.

	curl -XHEAD 'http://localhost:9200/sematext/blog/123456789'

###Aliasing

One of the things introduced in Apache Solr 4.0 and no available in ElasticSearch right now is the ability to transform result documents. First of all Solr allows you to alias returned fields, so for example you can return field price_usd or price_eur as price depending on your needs. The second thing is the ability to return values returned by functions as a (pseudo) field in the result (or fields). Solr also has the ability to return fields which start with a given prefix (for example all fields starting with price). Apart from the ability to get a function value as a field added to matched documents on the fly other functionalities are not ground breaking, though they can be handy in some cases.

Apache Solr 4.0 에서 소개된 ElasticSearch 에 현시점에서 아직 존재하지 않는 기능으로 결과의 도큐먼트 변환이 있습니다. 맨처음 Solr 는 반환값의 필드에 별명을 붙일 수 있습니다. 그 때문에 필요에 따라서 price 필드로 price_usd 나 price_eur 을 반환할 수 있습니다. 두번째로 함수의 반환값을 (겉보기의) 필드로써 결과(또는 필드)로 반환할 수 있습니다. Solr 는 부여된 접두어로 시작하는 필드(예를들어 price 로 시작하는 모든 필드)를 반환하는 기능도 있습니다.
 
### 그 외

One of the things we always mention when talking about the differences between Apache Solr and ElasticSearch, at least when it comes to query handling, is the possibility of specifying the analyzer during query time. But lets start from the beginning. In Solr, you have to create the schema.xml file which holds the information about the index structure as well as query and index-time analyzers for fields. Similarly, in ElasticSearch, you can create mappings and define analyzers. At query-time Solr will choose the right analyzer for each field and use it.  ElasticSearch will do the same with one major difference. In ElasticSearch you can change the analyzer and specify the analyzer you want to use for analysis at query-time. For example, this is very useful when you know the language of the query because then you can choose the most language-appropriate analyzer on the fly.  We have made use of this in combination with our Language Identifier.

Apache Solr 와 ElasticSearch 의 차이에 대해서 우리들이 항상 전달하고 싶은 것중 하나는, 적어도 쿼리이 취급에 관해서는 쿼리하는 도중에 해석기를 지정할 가능성입니다. Solr 에서는 인덱스구조, 거기에 쿼리와 필드의 인덱싱의 해석기에 관한 정보를 가지는 schema.xml 파일을 만들 필요가 있습니다.  ElasticSearch 에서는 맵핑을 작성해서 해석기를 정의합니다. 쿼리시에 Solr 는 각 필드에 대해서 적절한 해석기를 선택해서 사용합니다. ElasticSearch 도 같은 일을 하지만 큰 차이가 하나 있습니다. ElasticSearch 에서는 해석기 변경과 해석에 필요한 해석기의 지정이 쿼리시에 이뤄집니다. 예를 들어 쿼리대상의 언어를 알고 있는 경우 매우 편리하겠죠. 언어에 가장 적절한 해석기를 동적으로 선택할 수 있기 때문입니다. 우리들은 이 기능을 직접 만든 언어특정기와 함께 사용하고 있습니다.。

## 정리

As you can see, both ElasticSearch and Apache Solr expose lots of functionality when it comes to handling your search queries, and we barely scratched the surface here. Of course, each of them has some features that the other one doesn’t have, but Solr and ElasticSearch are competing for mind and market share, and are both rapidly evolving and improving, so we can expect more features from both of them in the future. In the next, fourth part of the series we will concentrate on the faceting capabilities of Apache Solr and ElasticSearch.  Stay tuned.  In the mean time, you can follow @sematext and tell us what you want us to cover.

보시는 대로, ElasticSearch 와 Apache Solr 모두 많은 기능을 공개하고 있습니다. 그리고 이 글에서는 겉을 살짝 핥은 것에 지나지 않습니다. 물론 각각 다른 쪽이 가지지 않은 기능을 가지고 있고, Solr 와 ElasticSearch 는 사고방식과 시장 마켓셰어 에서 경쟁을 하고 있습니다. 그리고 양쪽모두 급속하게 진화하고 있지요. 따라서 앞으로 더욱 많은 기능을 기대할 수 있을 것입니다. 

다음편에서는 Apache Solr 와 ElasticSearch 의 Faceting 기능에 대해서 다루도록 합니다. 이어서 계속 지켜봐주세요. 그때까지는 @sematext 를 팔로해서 무엇을 다뤄주었으면 좋은 지 알려주세요.
