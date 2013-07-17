---
layout: post
title: "[번역] Solr vs ElasticSearch - Part 1 : Overview"
date: 2013-07-15T09:17:35+09:00
comments: true
external-url: 
categories: [ translation, solr, elasticsearch ]
---

#Solr vs. ElasticSearch: Part 1 – Overview

August 23, 2012 by [Rafał Kuć](http://blog.sematext.com/author/kucrafal/)

2012/08/23 원저자 Rafał Kuć

A good Solr vs. ElasticSearch coverage is long overdue.  We make good use of our own Search Analytics and pay attention to what people search for.  Not surprisingly, lots of people are wondering when to choose Solr and when ElasticSearch, and this SolrCloud vs. ElasticSearch question is something we regularly address in our search consulting engagements.

Solr 와 ElasticSearch 에 대한 좋은 비교기사를 오래도록 기다려왔습니다. 우리는 스스로 검색분석을 활용해 온 사람들이 무엇을 검색하고 싶은 지 주목했습니다. 대개, 많은 사람들이 언제 Solr 를 선택하고, 언제 ElasticSearch 를 선택해야 하는 가에 어려워하고 있었습니다. 그리고 이 SolrCloud vs. ElasticSearch 문제는 우리가 검색 컨설팅 계약 중에 항상 겪어온 것입니다.

As the Apache Lucene 4.0 release approaches and with it Solr 4.0 release as well, we thought it would be beneficial to take a deeper look and compare the two leading open source search engines built on top of Lucene – Apache Solr and ElasticSearch. Because the topic is very wide and can go deep, we are publishing our research as a series of blog posts starting with this post, which provides the general overview of the functionality provided by both search engines.

Apache Lucene 4.0 의 릴리즈가 가까워 짐에 따라, 그와 함께 Solr 4.0 릴리즈도 가까워지고 있습니다. Apache Solr 와 ElasticSearch 라는 Lucene 위에 구축된 2가지 검색엔진을 비교해서, 깊은 고찰을 얻으려고 하는 것은 매우 큰 의미가 있다고 생각합니다. 이 토픽은 매우 범위가 깊어서, 우리들의 연구결과를 이 기사를 시작으로 하는 일련의 블로그 포스트로 공개합니다. 이 기사에서는 2가지 검색 엔진이 제공하는 기능의 전체적인 개요를 나타냅니다.

- [1. Solr vs. ElasticSearch: Part 1 – Overview](http://jeen.github.io/2013/07/15/solr-vs-elasticsearch-part-1/)
- [2. Solr vs. ElasticSearch: Part 2 – Data Handling](http://jeen.github.io/2013/07/16/solr-vs-elasticsearch-part-2-indexing-and-language-handling/)
- [3. Solr vs. ElasticSearch: Part 3 – Searching](http://jeen.github.io/2013/07/16/solr-vs-elasticsearch-part-3-searching/)
- [4. Solr vs. ElasticSearch: Part 4 – Faceting](http://jeen.github.io/2013/07/16/solr-vs-elasticsearch-part-4-faceting/)
- [5. Solr vs. ElasticSearch: Part 5 – Management API Capabilities](http://jeen.github.io/2013/07/17/solr-vs-elasticsearch-part-5-management-api-capabilities/)
- [6. Solr vs. ElasticSearch: Part 6 – User & Dev Communities Compared](http://jeen.github.io/2013/07/17/solr-vs-elasticsearch-part-6-user-and-dev-communities-compared/)

## 시작하기 전에

This post is based on released versions of Solr and ElasticSearch. For Solr, all the functionality description is based on version 4.0 beta and all of the ElasticSearch functionality is based on 0.19.8. Because we are comparing ElasticSearch and Solr, on the Solr side the focus is on Solr 4.0 (aka SolrCloud) functionality functionality and not Solr 3.*, so we could call this series as SolrCloud vs. ElasticSearch, too.

이 포스트는 다음과 같은 버젼을 기반으로 하고 있습니다. Solr 의 기능에 대한 서술은 버젼 4.0β를 기준으로 하며, ElasticSearch 은 버젼 0.19.8를 기준으로 합니다. ElasticSearch 와 Solr 를 비교하는 것이기에 Solr 는 Solr 3.* 이 아닌, SolrCloud 로 알려진 Solr 4.0 에 초점을 맞춥니다. 이 때문에 우리는 이 일련의 기사를 SolrCloud vs. ElasticSearch 로 부르고 있습니다.

## 검색 엔진의 뒷편

For indexing and searching both Solr and ElasticSearch use Lucene. As you may suspect, Solr 4.0 beta uses the 4.0 version of Lucene, while ElasticSearch 0.19.8 still uses version 3.6.  Of course, that doesn’t mean much when it comes to future versions of ElasticSearch because you can be sure that ElasticSearch will start using Lucene 4.0 once it’s GA release is ready, or maybe even before that.

인덱싱과 검색에는 Solr 와 ElasticSearch 모두 Lucene 을 사용합니다. 눈치채셨겠지만 Solr4.0β는 Lucene 4.0을 사용하지만 ElasticSearch 0.19.8은 3.6 을 사용하고 있습니다. 물론 그것은 ElasticSearch 의 장래 버젼에 대해서는 많은 의미를 가지고 있을 것입니다. Lucene 4.0 의 GA 릴리즈의 준비가 끝났다면 당장(또는 그 이전부터) ElasticSearch 도 그것을 사용하기 시작할 것은 확실하기 때문입니다.

## 기본

There are a few differences in the way Solr and ElasticSearch name certain concepts. Let’s start with the basics – many servers connected together forms a cluster for both ElasticSearch and Solr. A single instance of Solr or ElasticSearch is called a node. That’s about it for nomenclature overlap.

Solr 와 ElasticSearch에는 몇가지 컨셉에 대한 네이밍에 약간 차이가 있습니다. 기초부터 시작해봅시다.

ElasticSearch 와 Solr 모두, 많은 서버를 모아 클러스터를 구성할 수 있습니다. Solr 또는 ElasticSearch 의 하나의 인스턴스는 노드라고 부릅니다. 양쪽의 명명법이 겹치는 것은 그것뿐입니다.

The main logical data structure for Solr is called the Collection. A Collection is composed of Shards that are really Lucene indices. A single Collection can have multiple Shards and Shards can live on different Nodes. Because a Collection is composed of one or more Shards, a single Collection can be spread across multiple Nodes giving you a distributed environment. In addition to that, a Collection can have Replicas – basically an exact copy of the Shard whose main purpose is to enable scaling and data duplication in case of Node failures (i.e., High Availability).

Solr 의 메인 논리데이터구조는 콜렉션이라고 부릅니다. 콜렉션은 실제로는 Lucene 의 인덱스인 Shard 로 구성됩니다. 하나의 콜렉션은 여러 Shard 를 가질 수 있으며, Shard 는 다른 노드 위에 놓을 수 있습니다. 콜렉션이 하나 이상의 Shard 에 의해 구성되기에 하나의 컬렉션은 여러 노드에 걸칠 수 있기 에 분산시스템이 됩니다. 덧붙여, 콜렉션은 레플리카를 가질 수 있습니다. 레플리카는 기본적으로 Shard 의 완전한 카피로 주요한 목적은 스케일러빌리티와 노드의 장애에 대비하기 위한 데이터의 복제입니다 (즉 고가용성입니다).

On the other hand we have ElasticSearch where the top logical data structure is called an Index. Similar to a Collection in Solr, ElasticSearch Index can have multiple Shards and Replicas. And here, too, Shards and Replicas are small Lucene indices, that can be spread across the Cluster in order to create a distributed environment. But that’s not all – in ElasticSearch you can have multiple Types of documents in a single Index. This means that you can index documents of different index structure (for example users and their documents) in a single Index. ElasticSearch is able to distinguish those Types during indexing as well as querying. In order to achieve the same with Solr you would have to simulate that inside your application or develop a custom search component.

한편, ElasticSearch는 인덱스라고 부르는 논리적인 데이터 구조 위에 있습니다. Solr 의 콜렉션과 비슷해서, ElasticSearch 의 인덱스는 여러 Shard 와 인덱스를 가질 수 있습니다. 그리고 Shard 와 레플리칸는 작은 Lucene 의 인덱스이며 클러스터 위에 분산뒤어 분산환경을 구축합니다. 그러나 그것뿐이 아닙니다. ElasticSearch 에서는 하나의 인덱스 위에 여러 형태의 도큐먼트를 가질 수 있습니다. 이것은 다른 인덱스 구조의 여러 도큐먼트(예를들어 유저와 그들의 문서) 를 단일 인스턴스에 색인할 수 있다는 것을 의미합니다. ElasticSearch 는 인덱스 작성시는 물론 검색시에도 그런 형태들을 구분지을 수 있습니다. Solr 로 같은 일을 하기 위해서는 어플리케이션 안에서 그것을 시뮬레이트하거나, 커스텀 검색 컴포넌트를 개발할 필요가 있을 것입니다.

## 설정

Lets take a quick look at how Solr and ElasticSearch are configured. Let’s start with the index structure.

Solr 와 ElasticSearch 가 어떻게 설정되는지 간단하게 보도록 합니다. 우선은 인덱스의 구조부터 시작하겠습니다.

In Solr you need the schema.xml file in order to define how your index structure, to define fields and their types. Of course, you can have all fields defined as dynamic fields and create them on the fly, but you still need at least some degree of index configuration. In most cases though, you’ll create a schema.xml to match your data structure.

Solr 에서는 schema.xml 파일을 인덱스 구조의 필드와 그 형태의 정의를 위해서 필요로 합니다. 물론 모든 필드의 동적인 필드로 정의하고 동적생성을 할 수도 있습니다. 그러나 그것만으로도 어느정도는 인덱스의 정의를 필요로 할 것입니다. 대부분의 경우 데이터 구조에 매치하는 schema.xml 을 작성하게 됩니다.

ElasticSearch is a bit different – it can be called schemaless. What exactly does this mean, you may ask. In short, it means one can launch ElasticSearch and start sending documents to it in order to have them indexed without creating any sort of index schema and ElasticSearch will try to guess field types. It is not always 100% accurate, at least when comparing to the manual creation of the index mappings, but it works quite well. Of course, you can also define the index structure (so called mappings) and then create the index with those mappings, or even create the mappings files for each type that will exist in the index and let ElasticSearch use it when a new index is created. Sounds pretty cool, right? In addition to than, when a new, previously unseen field is found in a document being indexed, ElasticSearch will try to create that field and will try to guess its type.  As you may imagine, this behavior can be turned off.

ElasticSearch 는 조금 다릅니다. Schemaless 라고 부릅니다. 그것이 무엇인지 궁금할 수 있겠죠. 짧게 말하면 누구라도 ElasticSearch 를 기동해서 그대로 문서를 그 안에 보내는 것으로 하나의 인덱스 스키마를 정의하는 일도 없이 인덱스 작성을 할 수 있습니다. ElasticSearch 가 필드 형태를 추론해줍니다. 언제라도 100% 정확하다고 할 수는 없습니다. 적어도 수동으로 스키마를 정의한 경우에 비례해서는 당연히 그렇습니다. 그러나 매우 잘 동작합니다. 물론, 인덱스 정의(즉 맵핑)을 정의하는 것은 가능하고, 맵핑에 따라 인덱스를 작성할 수도 있스빈다. 또 여러 맵핑파일을 인덱스에 존재할 여러 형태에 대해서 작성하는 것도 가능하고, ElasticSearch 에 새로운 인덱스가 작성될 때에 그것을 사용하는 것도 가능합니다. 매우 매력적이죠? 덧붙여서 인덱스 작성중의 문서에는 아직 발견되지 않은 새로운 필드가 발견된 경우, ElasticSearch 는 그 형태를 추론해서 새로운 필드를 작성하려고 합니다. 물론 그 기능은 끌 수도 있습니다.

Let’s talk about the actual configuration of Solr and ElasticSearch for a bit. In Solr, the configuration of all components, search handlers, index specific things such as merge factor or buffers, caches, etc. are defined in the solrconfig.xml file. After each change you need to restart Solr node or reload it. All  configs in ElasticSearch are written to elasticsearch.yml file, which is just another configuration file. However, that’s not the only way to store and change ElasticSearch settings. Many settings exposed by ElasticSearch (not all though) can be changed on the live cluster – for example you can change how your shards and replicas are placed inside your cluster and ElasticSearch nodes don’t need to be restarted.  Learn more about this in ElasticSearch Shard Placement Control.

Solr 와 ElasticSearch 의 실제 설정에 대해서도 조금 더 이야기를 이어가겠습니다. Solr 에서는 모든 컴포넌트, 검색 핸들러, 인덱스를 특정하기 위한 다양한 일(예를들어 머지팩터와 버퍼, 캐쉬 등)의 정의는, solrconfig.xml 파일 안에 정의됩니다. 모든 변경 뒤에는 Solr 노드의 재기동이나 리로드가 필요합니다. ElasticSearch 의 모든 설정은 elasticsearch.yml 파일에 적혀져 있습니다. 그것은 또한 다른 설정파일에 지나지 않습니다. 그러나 ElasticSearch 의 설정을 정의하거나 변경하는 방법은 그것뿐만이 아닙니다. ElasticSearch 에 있어서 많은 설정(그러나 모든 것이 아닌)은 운용중인 클러스터 위에서 변경가능합니다. 예를들어 Shard 와 레플리카를 클러스터 내부의 어디에 놓을 것인가입니다. 또 ElasticSearch 노드는 재기동할 필요가 없습니다. 이에 대해서는 ElasticSearch 의 Shard 의 배치 컨트롤에에서 보다 자세히 배우도록 합니다.

## 검색과 클러스터의 관리

Solr and ElasticSearch have a different approach to cluster node discovery and cluster management in general.  The main purpose of discovery is to monitor nodes’ states, choose master nodes, and in some cases also store shared configuration files.

Solr 와 ElasticSearch는 클러스터 노드의 탐색과 클러스터 관리 전반에 있어서 다른 접근을 취합니다. 탐색의 주요한 목적은 노드의 상태 감시, 마스터 노드의 선택, 또 Shard 정의 파일의 격납을 따라가는 몇가지 케이스가 있습니다.

By default ElasticSearch uses the so called Zen Discovery, which has two methods of node discovery: multicast and unicast.  With multicast a single node sends a multicast request and all nodes that receive that request respond to it. So if your nodes can see each other at the network layer with the use of multicast method your nodes will be able to form a cluster. On the other hand, unicast depends on the list of hosts that should be pinged in order to form the cluster. In addition to that, the Zen Discovery  module is also responsible for detecting the master node for the cluster and for fault discovery. The fault discovery is done in two ways – the master node pings all the other nodes to see if they are healthy and the nodes ping the master in order to see if the master is still working as it should.  We should note that there is an ElasticSearch plugin that makes ElasticSearch use Apache Zookeeper instead of its own Zen Discovery.

기본적으로는 ElasticSearch는 선탐색(Zen Discovery)이라고 불리는 방법을 사용하는데, 이는 멀티캐스트와 유니캐스트 라는 2가지 노드의 탐색수법이 존재합니다. 멀티캐스트에는 하나의 노드는 멀티캐스트 쿼리를 송신하고 그 리퀘스트를 받은 모든 노드는 그에 대해 응답을 보냅니다. 만약 노드가 멀티캐스트 메소드를 사용하고 네트워크 층에 대해서 서로 인식할 수 있으면, 노드는 클러스터를 구성할 수 있습니다. 한편, 유니캐스트는 호스트의 리스트에 따라, 클러스터를 구성하기 위해 ping 을 보낼 필요가 있습니다. 덧붙여 선탐색 모듈은 클러스터의 마스터 노드의 검지와 장애탐색의 책임을 가집니다. 장애탐색에는 두가지 방법을 취할 수 있습니다. 마스터 노드는 다른 모든 노드에 대해 ping 을 보내 건강한지를 확인하고, 다른 노드는 마스터가 움직이는 지를 확인하기 위해서 ping 을 보냅니다. 주목할 점으로는 선탐색 대신에 Apache ZooKeeper 를 사용하는 ElasticSearch 플러그인이 존재합니다.

Apache Solr uses a different approach for handling search cluster. Solr uses Apache Zookeeper ensemble – which is basically one or more Zookeeper instances running together. Zookeeper is used to store the configuration files and monitoring – for keeping track of the status of all nodes and of the overall cluster state. In order for a new node to join an existing cluster Solr needs to know which Zookeeper ensemble to connect to.

Apache Solr 는 클러스터 탐색을 다루는 것에 있어서 다른 접근방법을 가집니다. Solr 는 Apache Zookeeper ensemble 을 이용합니다. 그것은 기본적으로 하나이상의 ZooKeeper 의 인스턴스가 동시에 실행하는 것으로 구성됩니다. ZooKeeper 는 설정파일의 격납과 감시에 이용됩니다. 모든 노드와 클러스터 전체의 상태를 추적합니다. 기존 클러스터에 새로운 노드를 추가하는 경우에는 Solr 는 어느  ZooKeeper ensemble 에 접속할 것인가 알아야 할 필요가 있습니다.

There is one thing worth noting when it comes to cluster handling – the split brain situation. Imagine a situation, where you cluster is divided into half, so half of your nodes don’t see the other half, for example because of the network failure. In such cases ElasticSearch will try to elect a new master in the cluster part that doesn’t have one and this will lead to creation of two independent clusters running at the same time. This can be limited with a small degree of configuration, but it can still happen. On the other hand, Solr 4.0 is immune to split brain situations, because it uses Zookeeper, which prevents such ill situations. If half of your Solr cluster is disconnected, it wouldn’t be visible by Zookeeper and thus data and queries wouldn’t be forwarded there.

하나 주의할 점이 클러스터를 다루는 것에 있습니다. 스플릿플레인이라고 하는 상태입니다. 클러스터가 분쪽으로 나눠져 노드의 반쪽이 다른 한쪽의 반쪽을 알수 없는 상태를 상상해보세요. 예를들어 네트워크의 장애입니다. 그런 케이스에서는 ElasticSearch는 마스터 노드를 가지지 않는 쪽의 부분 클러스터에 새로운 마스터를 선택하려고 합니다. 이것이 두개의 독립한 클러스터 도잇에 실행되는 상황으로 인도합니다. 이것은 적은 양의 정의로 제한할 수 있지만, 그래고 발생할 수 있습니다. 반면 Solr 4.0 은 스플릿플레인 상태에 면역이 있습니다. ZooKeeper 를 사용하고 있기 때문에, 그런 상태를 방지할 수 있습니다. 만약 반쪽의 Solr 클러서가 접속불가능한 경우, ZooKeeper 에는 보여지지 않고, 따라서 데이터와 쿼리는 그쪽에는 전송되지 않습니다.

##API

If you know Apache Solr or ElasticSearch you know that they expose an HTTP API.

Apache Solr 가 ElasticSearch 를 알고 있다면 HTTP API 를 공개했다는 것도 알고 있을 겁니다.

Those of you familiar with Solr know that in order to get search results from it you need to query one of the defined request handlers and pass in the parameters that define your query criteria. Depending on which query parser you choose to use, these parameters will be different, but the method is still the same – an HTTP GET request is sent to Solr in order to fetch search results. The good thing is that you are not limited to a single response format – you may choose to get results in XML, in JSON in JavaBin format and several other formats that have response writers developed for them.  You can thus choose the format that is the most convenient for you and your search application.  Of course, Solr API is not only about querying as you can also get some statistics about different search components or control Solr behavior, such as collection creation for example.

Solr 를 잘 아는 사람은 검색결과를 얻기 위해서 정의가 끝는 리퀘스트 핸들러를 요구하고, 쿼리의 기준을 정의하는 파라메터의 안에서 넘길 필요가 있는 것은 알고 있을 겁니다. 어느 쿼리 파서를 선택할 것인가에 따라 그것들의 파라메터는 달라집니다. 그러나 방법은 같습니다. HTTP GET 리퀘스트가 검색결괄르 얻기위해서 Solr 에 보내집니다. 좋은 점은 단일 응답형식에 제한되지 않는 다는 것입니다. 결과를 얻기위해서는 XML, JavaBin 형식의 JSON 이나 다른 몇가지 그것들을 위해 개발된 응답을 써보내줄 녀석을 가지는 형식을 선택할 수 있습니다. 따라서, 스스로 검색과 검색 어플리케이션에 가장 편리한 형식을 선택할 수 있습니다. 물론 Solr API 는 쿼리를 위한 것 뿐만 아니라, 다른 검색컴포넌트의 통계를 얻고, Solr 의 동작, 예를들어 콜렉션의 작성등을 컨트롤할 수도 있습니다.

And what about ElasticSearch?  ElasticSearch exposes a REST API which can be accessed using HTTP GET, DELETE, POST and PUT methods. Its API allows one not only to query or delete documents, but also to create indices, manage them, control analysis and get all the metrics describing current state and configuration of ElasticSearch. If you need to know anything about ElasticSearch, you can get it through the REST API (we use it in our Scalable Performance Monitoring for ElasticSearch, too!). If you are used to Solr there is one thing that may be strange for you in the beginning – the only format ElasticSearch can respond in JSON – there is no XML response for example. Another big difference between ElasticSearch and Solr is querying. While with Solr all query parameters are passed in as URL parameters, in ElasticSearch queries are structured in JSON representation. Queries structured as JSON objects give one a lot of control over how ElasticSearch should understand the query and thus what results to return.

ElasticSearch 는 어떨까요? ElasticSearch는 HTTP GET, DELETE, POST, PUT 메소드를 사용해서 접근할 수 있는 REST API 를 공개하고 있습니다. 도큐먼트에 쿼리를 날리거나 삭제할 수 있을 뿐만 아니라, 인덱스를 작성하거나 관리하거나 분석을 컨트롤해서 현재의 상태를 나타내는 메트릭스를 모두 얻거나, ElasticSearch 설정을 뽑아낼 수도 있습니다. 만약 ElasticSearch 에 대해서 뭔가 알고 싶은 것이 있다면 REST API 를 통해서 얻어낼 수 있습니다. (우리는 그것을 자사 "Scalable Performance Monitoring for ElasticSearch"에서도 사용하고 있습니다) 만약 Solr 에 익숙해져 있다면 가장 먼저 이상하게 느끼는 것중 하나 일 것입니다. ElasticSearch 의 응답에는 JSON 포맷밖에 존재하지 않습니다. XML 은 없습니다. ElasticSearch 와 Solr 의 다른 큰 차이점ㅇ은 쿼리입니다. Solr 에서는 모든 쿼리파라메터가 URL 파라메터로 넘겨지는 것에 반해, ElasticSearch 쿼리에서는 JSON 표현으로 구성됩니다. JSON 오브젝트로 쿼리를 구성하는 것으로 ElasticSearch 가 어떻게 쿼리를 이해하고, 어떻게 결과를 보내주는 지에 대한 많은 컨트롤을 제공합니다.

## 데이터의 취급

Of course, both Solr and ElasticSearch leverage Lucene near real-time capabilities.  This makes it possible for queries to match documents right after they’ve been indexed. In addition to that, both Solr (since 4.0) and ElasticSearch (since 0.15) allow versioning of documents in the index.  This feature allows them to support optimistic locking and thus enable prevention of overwriting updates. Let’s look at how distributed indexing is done in Solr vs. ElastiSearch.

물론, Solr 와 ElasticSearch 양쪽이 Lucene 의 거의 리얼타임 특성을 이용하고 있습니다. 그것이 쿼리가 도큐먼트에 대해서 인덱스가 작성된 다음에 바르게 매치하는 것을 가능하게 합니다. 덧붙여 Solr(4.0부터) 와 ElasticSearch(0.15부터) 는 인덱스 안에서 도큐먼트의 버져닝도 가능합니다. 이 기능은 낙관적인 로직의 지원을 가능하게 하고, 변경에 대한 덮어쓰기를 방지할 수도 있습니다. Solr 와 ElasticSearch 에서 분산 인덱스가 어떻게 수행되는 가를 보도록 합니다.

Let’s start with ElasticSearch this time. In order to add a document to the index in a distributed environment ElasticSearch needs to choose which shard each document should be sent to. By default a document is placed in a shard that is calculated as a hash from the documents identifier. Because this default behavior is not always desired, one can control and alter this behavior by using a feature called routing.  This is controlled via the routing parameter, which can take any value you would like it to have. Imagine that you have a single logical index divided into multiple shards and you index multiple users’ data in it.  On the search side you know queries are narrowed mostly to a single user’s data. With the use of the routing parameter you can index all documents belonging to a single user within a single shard by using the same routing value for all his/her documents.  On the search side you can then use the same routing value when querying. This would result in a single shard being queried instead of the query being spread across all shards in the index, which would be more expensive and slower. In case each index shard contains multiple users’ data we could additionally use a filter to limit matches to only one user’s documents.  In cases like this, routing functionality allows one to think of some nice optimization for both indexing and querying. If you want to hear some more about distributed indexing capabilities of ElasticSearch please take a look at my Berlin Buzzwords 2012 talk – Scaling Massive ElasticSearch Clusters (video).

이번에는 ElasticSearch부터 시작합니다. 분산환경에 있어서 ElasticSearch 의 인덱스에 도큐먼트를 추가하기 위해서는 여러 도큐먼트가 어느 Shard 로 보내졌는가를 선택할 필요가 있습니다. 기본적으로는 도큐먼트는 도큐먼트ID에서 계산된 해쉬키에 의해서 결정된 Shard 로 놓여집니다. 기본적인 동작은 항상 이상적이지 않기 때문에, 라우팅이라고 불리는 기능을 사용해서 변경할 수 있습니다. 이것은 라우팅 파라메터를 통해서 조정할 수 있고, 이 값은 임의의 값을 설정할 수 있습니다. 여러 Shard 로 분할된 하나의 논리 인덱스를 가지고 있고, 여러 유저의 데이터를 그곳에 색인하도록 해본다고 생각해봅시다. 검색 사이드에서는 쿼리는 대부분의 경우에 단일 유저의 데이터에 묶여 있다는 것을 알고 있습니다. 라우팅 파라메터를 사용하는 것으로 단일 유저에 소속된 모든 도큐먼트를 단일 Shard 에서 색인할 수 있다는 것이 모든 유저의 도큐먼트에 대해서 같은 라우팅 값을 사용할 수 있습니다. 검색 사이드에서는 따라서 쿼리 시에 같은 라우팅 값을 사용할 수 있습니다. 이에 의해 결과는 쿼리가 실행된 단일 Shard 안에서만 존재하고 쿼리가 인덱스 안에서 모든 Shard 에 대해서 넓어지지 않습니다. 그 경웅는 보다 높은 비용으로 느려지겠죠. 개개의 인덱스의  Shard 가 여러 유저의 데이터를 가지고 있는 경우에는 추가하는 것으로 필터를 이용해서 단 한 사람의 유저의 도큐먼트에 적합하게 제한할 수 있습니다. ElasticSearch 의 분산인덱스의 기능에 대해서 더 자세히 듣고 싶다면 저자의 Berlin Buzzwords 2012의 강연, "[Scaling Massive ElasticSearch Clusters](http://blog.sematext.com/2012/06/05/slides-scaling-massive-elasticsearch-clusters/ "http://blog.sematext.com/2012/06/05/slides-scaling-massive-elasticsearch-clusters/")" 비디오 (https://vimeo.com/44718089)를 참조해주세요.

Details of Solr’s implementation of distributed indexing (and searching) capabilities can be found in our The New SolrCloud: Overview post. But let’s recall some of those details. In order to forward a document to a proper shard Solr uses Murmur hashing algorithm which calculates the hash for the given document on the basis of its unique identifier. This part is similar to default ElasticSearch behavior.  However, Solr doesn’t yet let you specify explicitly to which shard the document should be sent  - there is no document and query routing equivalent in Solr yet.

Solr 의 분산 인덱스(와 검색) 에 관한 구현의 자세한 내용은 우리의 <a href="http://blog.sematext.com/2012/02/01/solrcloud-distributed-realtime-search/">"The New Solrcloud: Overview"</a>의 기사를 참조해주세요. 그리고 그 자세한 내용의 몇가지를 복습해보도록 합니다. 도큐먼트를 적절하게 Shard 에 보내기 위해서 Solr 는 Murmur hashing 이라는 알고리즘을 사용해서 부여된 도큐먼트의 해쉬값을 고유한 식별자로 삼아서 계산하고 있습니다. 이 부분은 ElasticSearch 의 기본동작과 비슷합니다. 그러나 Solr 는 명시적으로 어느 Shard 에 도큐먼트를 보낼 것인가를 지정하는 것은 아직 불가능합니다. 도큐먼트와 쿼리의 라우팅에 비견되는 것은 Solr 에는 아직 없습니다.

Of course, both Solr and ElasticSearch allow one to configure replicas of indices (ElasticSearch) or collections (Solr). This is crucial because replicas enable creation of highly available clusters – even if some of nodes are down, for example because of hardware failure or maintenance, the cluster and data within it can remain available. Without replicas if one nodes is lost, you lose (access to) the data that were on the missing node. If you have replicas present in your configuration both search engines will automatically copy documents to replicas, so that you don’t need to worry about data loss.

물론, Solr 와 ElasticSearch 모두 인덱스（ElasticSearch)、 또는 콜렉션（Solr）의 레플리카 설정을 가능하게 합니다. 이것은 레플리카 클러스터의 고가용성을 실현하는 것이기 때문에 매우 중요합니다. 예를들어 몇가지 노드가 하드웨어장애나 메인테넌스로 다운되었어도, 클러스터와 그 안의 데이터는 계속 이용가능합니다. 레플리카 없이는 하나의 노드를 잃어버리면 잃어버린 노드 위에 존재한 데이터(에 대한 접근)을 잃어버립니다. 만약 설정에서 레플리카가 존재하는 경우 두 검색엔진은 자동적으로 도큐먼트를 레플리카로 복사하기 때문에, 데이터 소실에 대해서 걱정할 필요는 없습니다.

## 결론


We hope that after reading this post you have the basic understanding of what you can expect from both Solr 4.0 and ElasticSearch 0.19.* and you can start to get the feeling for differences and similarities between them.  Of course,  both Solr and ElasticSearch have very strong and active user and development communities and are constantly evolving and improving, and are doing that rather fast. In pre-Solr 4.0 (aka SolrCloud) world the difference between Solr and ElasticSearch was quite stark.  Since then, and under the pressure from ElasticSearch, the gap has narrowed and both projects are moving forward quite quickly.  At Sematext our clients often ask us to recommend the search engine for their use and we recommend both of them.  Which one we recommend for a particular project depends on project requirements, which we always go through at the beginning of every engagement.  If you need help deciding, let us know.

이 기사를 읽은 다음에 Solr 4.0 과 ElasticSearch 0.19.* 양쪽에서 무엇을 기대할 수 있는가, 그 기초를 이해했기를 바랍니다. 그리고 둘 사이의 차이와 유사점에 대해서도 이해되었기를. 물론, Solr 와 ElasticSearch 양쪽모두 매우 강력하고 활발한 유저와 개발자의 커뮤니티가 존재하며, 정기적으로 진화하고 세련되며, 그리고 보다 빨라지고 있습니다. Solr Cloud 로 알려진 Solr 4.0 이전에는 Solr 와 ElasticSearch 의 차이는 매우 컸습니다. ElasticSearch 의 압력의 근원으로 갭은 좁아져 두가지 프로젝트는 함께 빨리 진행되고 있습니다. 어느 프로젝트를 추천하는 지는 요건에 따라 달라집니다. 이것은 우리가 항상 계약시점에 넘어야 할 길입니다. 만약 당신이 결단에 도움을 필요로 한다면 꼭 알려주세요.

Also please keep in mind this post is not meant to be the most comprehensive guide to all the similarities and differences between ElasticSearch and Solr. We wanted to start with a general overview of how these two great search engines work and cover the big picture. In subsequent parts of the “Solr vs. ElasticSearch” series we’ll describe how the most frequently used features of both search engines work, what the differences between them are, and we’ll get into details of those features showing you pros and cons of Solr vs. ElasticSearch approach (for example approaches used in faceting or caching). Just as a sneak peak into the next post in the series – you can expect information about language handling capabilities, analysis configuration, and querying.

또 이 기사가 ElasticSearch와 Solr 사이의 유사점과 차이점에 대해서 가장 완벽한 가이드가 아니라는 점을 항상 주의해주세요. 우리는 이 두가지 대단한 검색 엔진이 어떻게 동작하는 가 하는 일반적인 개요와 외관부터 시작하고 싶었습니다. 이에 이어서 "Solr vs. ElasticSearch" 시리즈의의 파트에서는 두 검색엔진에서 가장 빈번하게 사용되는 기능이 어떻게 동작하는 지 해설합니다. 그리고 그 기능들의 자세한 내용들을 "ElasticSearch vs. Solr" 로 접근해서 그 이점과 결점을 보도록합니다. 예를들어 Facet 과 Cache 로 이용되는 접근방식에서) 시리즈의 다음회에 언어의 취급 기능과 분석설정, 그리고 쿼리에 대해서 기대해주세요.

[@kucrafal](http://twitter.com/kucrafal), [@sematext](http://twitter.com/sematext "sematext")