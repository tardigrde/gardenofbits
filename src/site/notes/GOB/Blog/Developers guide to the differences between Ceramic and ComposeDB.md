---
{"dg-publish":true,"dg-path":"Blog/Developers guide to the differences between Ceramic and ComposeDB.md","permalink":"/blog/developers-guide-to-the-differences-between-ceramic-and-compose-db/","title":"Developers guide to the differences between Ceramic and ComposeDB","tags":["web3","post"],"noteIcon":"default","created":"2022-10-23","updated":"2022-10-23"}
---


# Developers guide to the differences between [Ceramic](https://ceramic.network/) and ComposeDB

If you are a web3 developer, chances are that you have already deployed your first DApp (Decentralized Application). If you came from web2, probably you noticed an important difference to your normal web2 app, there was no backend or database setup needed. To be more clear, you used a blockchain as your database.

But what if you want to make a complex web app, like a decentralized social media platform. You need dynamic data for serving all your users. But using a locked-away database sitting in some remote data center goes against the idea of decentralization. A truly decentralized DApp should store no data and no asset in a siloed environment (i.e. hosting a file from a single file server). 

We also know that storing large amounts of data on blockchains can be expensive, as usually all full nodes of a given chain must store every bit of data. This is not what blockchains are designed for anyway.

Using some decentralized file system, like IPFS can be a solution, but it lacks proper persistence. Filecoin or Arweave can help with persistence, but these systems too are dealing with files, they have no capabilities of a database. Developers need a database-like tool.

Where do you store your data then? Enter decentralized data storage and Ceramic.

## What is Ceramic and how does it work
It's a composable data network built on top of [[GOB/Blog/IPFS  Uncovered\|IPFS]].

Quote from a Ceramic [blog post ](https://blog.ceramic.network/what-is-ceramic/): 
> Ceramic is a public, permissionless, open source protocol that provides computation, state transformations, and consensus for all types of data structures stored on the decentralized web.

Ceramic's vision can be framed like so: bring decentralized, dynamic, verifiable and composable data to the web.

If you are interested in how does it work  under the hood, I'd highly encourage that you read the [Technical Overview](https://developers.ceramic.network/learn/advanced/overview).

TL;DR: from Ceramic [docs](https://developers.ceramic.network/learn/advanced/overview/):
> **Ceramic** enables static files to be composed into higher-order mutable data structures, programmed to behave in any desired manner, and whose resulting state is stored and replicated across a decentralized network of nodes.

Ceramic empowers developers with a set of tools and [features](https://developers.ceramic.network/learn/features/) that help build scalable DApps.

### A mutable, verifiable data storage

Ceramic is permissionless, which means everybody can interact with the network in order to query, create or update files. It provides open standards, and APIs for interacting with the data. Every piece of data is verifiable in Ceramic, so you do not have to worry about the correctness of the data you retrieve.

### Data composability

Since the network is open, and Ceramic uses data models to describe data, you can build upon 3rd party models, without their permission or without the need to trust data availability. 

### Model marketplace
Everybody can create a model, and publish it in the marketplace, which in turn can be used by anyone. You just pick a model, plug it in your app and you will instantly get access to all data stored that satisfies the model. This is significantly improving developer experience.

### Decentralized identifiers

Ceramic uses the [Decentralized Identifiers](https://www.w3.org/TR/did-core/) standard for accounts. DIDs by nature, are compatible with web3 wallets. Ceramic currently supports [Sign-in with Ethereum](https://github.com/ethereum/EIPs/blob/5e9b0fe0728e160f56dd1e4cbf7dc0a0b1772f82/EIPS/eip-4361.md) (SIWE) and Sign-in With Solana, but will add support for more blockchains gradually. 

### Infinitely scalable
Ceramic easily scales horizontally. You could plug every device in the world into the network which is capable of running a Ceramic node. Since the network is permissionless, everybody can [run a node](https://developers.ceramic.network/run/).


## Intro to Ceramic stack

The focus of this article is mainly on Ceramic's new product called [ComposeDB](https://github.com/ceramicstudio/js-composedb). It still makes sense that you get a high-level overview on the original Ceramic stack.

Ceramic has developed a number of different tools that help developers start using Ceramic. I, myself, find these tools a bit hard to work with, mainly because I do not see a single, preferred way for doing certain tasks. Anyway, let's talk about it briefly.

Ceramic has a framework called [Self.ID SDK](https://developers.ceramic.network/reference/self-id/). This is the highest-level tool focusing on getting started with Ceramic the easiest way possible. It has most of the needed libraries, and it also deploys a Ceramic node locally. If you are forced to use the Ceramic stack over ComposeDB, probably this should be your entry-point.

The [Glaze suite](https://developers.ceramic.network/reference/glaze/) with its handful of modules and APIs offers developers a set of very useful tools. You can use glaze to deploy models with CLI or with JavaScript. It can be used to store in the Ceramic account's personal data store, but also to optimize loading time of data.

Ceramic also has 3 different kind of libraries for Ceramic Accounts: [3ID DID](https://developers.ceramic.network/reference/accounts/3id-did/), [Key DID](https://developers.ceramic.network/reference/accounts/key-did/) and [DID Session](https://developers.ceramic.network/reference/accounts/did-session/).

You can see a list of all Ceramic packages in the [reference](https://developers.ceramic.network/reference/javascript/packages/#javascript-api-references).

## Intro to ComposeDB

[ComposeDB](https://github.com/ceramicstudio/js-composedb) is a graph database sitting on top of Ceramic network. A quote from their [blog post](https://blog.ceramic.network/composedb-using-ceramic-as-a-graph-database/) explains it best:
> ComposeDB provides a complete data solution that supports multi-player use cases—like those demanded by social and DAO applications.

As the quote suggests, the Ceramic team designed this product to allow developers to build complex web apps that need to store user-related or even user-generated content.

> **NOTE:** Currently, ComposeDB is in developer preview. The production-ready version will be out early 2023.

### ComposeDB Overview

In the following section, I will describe some of the core concepts around ComposeDB with some examples.

#### The graph database

ComposeDB is a graph database, which means it's structure consists of nodes and edges. It uses the well-known [GraphQL](https://graphql.org/) data query and manipulation language.

In ComposeDB's graph nodes are either a `document` or an `account`. Documents contain arbitrary shaped data, while accounts represent entities who are able to manipulate data in the graph. Edges between these nodes describe the relation between the connected nodes.

So let's dig a bit deeper and understand these concepts.

##### Accounts

The account is an abstraction around [DIDs](https://w3c.github.io/did-core/). It represents anyone (admin or end-user) or anything (e.g. a service account) who is allowed to mutate data on Ceramic. It basically extends the functionality of [DID scalars](https://composedb.js.org/docs/0.3.x/guides/creating-composites/scalars#did), and it gets rendered into a [CeramicAccount object](https://composedb.js.org/docs/0.3.x/guides/interacting/queries#ceramicaccount-object) during ComposeDB runtime.

##### Documents & models

Documents are [Ceramic Streams](https://developers.ceramic.network/learn/advanced/overview/#streams) storing arbitrarily shaped data. The documents' structure is defined by a model. Developers are able to create, update and query documents with the help of GraphQL. Documents can be indexed on nodes, and this makes their retrieval blazing fast.

Models are also Ceramic Streams. A model stores metadata about a document including its structure, validation rules and relations to other nodes in the graph among other things. A model must be associated to every document. As of late 2022 Ceramic uses [JSON Schema](https://json-schema.org/specification-links.html#2020-12) for data models.

**Validation**

Every Ceramic Network node automatically validates documents against their model. This allows developers to trust certain aspects of the data retrieved from ComposeDB.

##### Relations

Edges in the graph represent the relation between accounts and documents. As of late 2022 ComposeDB supports 3 types of relations:

1. account -> document, with two variants:
	- account -> single document,
	- account -> multiple documents,
2. document -> account,
3. document -> document.

#### Real-world example - a ride-sharing app

Consider a ride-sharing app that uses ComposeDB as a means of storing data. An app like this has two main entities: the users and the trips they offer or take.

You start using the app using your web3 wallet, your DID, which gets translated into an `account` in ComposeDB. DIDs only represent an identity, not more. But, the app uses a UserProfile data `model` to make interaction with other people more user-friendly. The profile might include your nickname, your contact info or your app-specific preferences. 

Once you submit the profile form, the app saves it into a structured `document` on your behalf. Since you own this document (as in: you created it and you are the only one who can make modifications to it), a relation has emerged: `account -> single document`. Single, because it does not really make sense if you have more than one UserProfile document of the same data model.

You make a trip to the closest city, and decide you advertise this trip as an offer for other people. You fill in the trip creation form, and the web app saves it into another document that conforms to the Trip data model the app uses.

You realize, you will also come back home from the city, and decide to create another advertisement for the return trip. Another document is saved. You see where I am going with this, right? Multiple trip documents can be tied to an account, which is an example of `account -> multiple documents` relation.

Without going too much into the details, a trip schema could also reference another trip (e.g. a trip also has a return trip). This is an example of the `document -> document` relation.

Okay, now that you understood the basics, let's dive a bit more deeper.

#### Composite - the primary data structure

Composite is a new abstraction introduced in ComposeDB. It encapsulates the data, its schema and metadata. A composite is composed of 1 or more data models, which allows developers to use them as Lego blocks. The cool thing about composites, is that you can merge multiple simple composites together, as well as extract models from existing composites. This is where ComposeDB really shines compared to Ceramic features.

Following with the ride-sharing app example, the creators of such an app, could look at similar, already existing apps built on ComposeDB, and reuse some of their composites. But hey have other options too. If they are not 100% satisfied with what they found, they can decide to extract the best models from multiple composites, and merge them into their brand-new composite. This way they can still reuse the underlying data models, without the need of using the exact same, and maybe limited composite that a competitor has. Of course, they also have the option to create their new composites from their own data models.

#### Data Model Reusability and Marketplace

Another ride-sharing app could reuse your composites, but why is this beneficial for them? ComposeDB's intro [blog post](https://blog.ceramic.network/composedb-using-ceramic-as-a-graph-database/) frames it like this:
> Apps that reuse each other's Composites create instant interoperability, without any integrations needed.

If you start using an existing composite, you get access to all the data which implements the same composite. This would mean that a user would be able to create their trips once and reuse it across multiple ride-sharing applications. 

Another example would be two social media platforms, where the posts are described with the same composite. When you create a post on platform A, it could be mirrored to platform B.

#### One client to rule them all

The first tools introduced by Ceramic, like all those libraries that were mentioned earlier in this post, were not so developer-friendly. There were multiple ways to do a task. It was often hard to decide upfront which tooling you should be using. 

This is remedied with ComposeDB. The only client library developers need is the ComposeDB Client (JavaScript only, for now) library, which also has a GraphQL client built in it. Clients loaded with composites are able to easily execute queries and mutate data on the Ceramic node.

#### Capability-based access control

A core UX challenge in web3 is authorization. How does an app know if an owner of a DID is authorized to perform certain tasks. 

The Ceramic team did a great job on solving this issue. They introduced a [new standard](https://github.com/ChainAgnostic/CAIPs/pull/74), which enables to store the result of `Sign-in with Ethereum` (it is actually chain-agnostic, not tied to Ethereum) as an [IPLD](https://github.com/ipld/ipld) object. The object contains information on what resources can the capability-owner operate on. These capabilities can be passed downstream to other users or services.

Ceramic implemented a library called [did-sessions](https://developers.ceramic.network/reference/accounts/did-session/) that uses this standard and helps developers achieve fine-tuned, time-bound access control on their apps. They also introduced a new DID method, called [did:pkh](https://developers.ceramic.network/docs/advanced/standards/accounts/pkh-did/), that supports blockchain wallets out-of-box.


## Benefits of using ComposeDB over the original Ceramic Stack

Finally, let's talk about what are the differences between ComposeDB and the Ceramic stack, and why you should consider start using ComposeDB.

I'd start with the technical differences, from which the first notable difference is the graph database. Without the graph, Ceramic is only a key-value store. Key-value stores are nice, because they scale well, and they are speedy. On the other hand, Graph databases has the advantage of highlighting relationship between data points, which make them suitable for complex, multi-user applications.

In Ceramic there was no such thing as indexing, with ComposeDB you have data indexing out-of-box. This enables swift data retrieval.

Queries in ComposeDB are done with GraphQL, as opposed to chained JavaScript method calls in Ceramic.

Improved tech is nice, but what is the purpose of tech, if it does not help developers do great things with it, or users who would use these great things. Ceramic has a mesh of client libraries, some of them with overlapping functionalities. This leads to suboptimal developer experience. ComposeDB solves this with a single client and much better tooling.  

ComposeDB also introduced Composites, which enables better data modelling and supports one-to-many relationships. There is also a marketplace for Composites, which allows reusability. If you ever wondered how data composability will be solved in web3, the answer is: Composites. 

ComposeDB's new `did:pkh` DID method is web3 native, it supports blockchain wallets. It also allows granular access-control, and the access can be transferred to infinite number of entities.

## Wrap up

Although ComposeDB is still in development, it has great promises for web3 developers and the world too. What will YOU build with it?

I hope this blogpost helped to clarify the differences between Ceramic, and ComposeDB on Ceramic.
