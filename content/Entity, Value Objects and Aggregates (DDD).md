---
title: 
draft: true
tags: 
  - software-engineer
  - domain-driven-design
  - status/🪴
  - literature
source: 
authors:
  - Júnior, Elemar Rodrigues
  - Fowler, M.
aliases: 
description: 
date: ""
---
> [!NOTE]
> This is my personal resume of the **Elimar**'s live on **Entities, Value Objects and Aggregates** [^3].

## What is DDD about

First of all, we need to understand what **Domain Driven Design (DDD)** is about and what this tries to solve properly.

 **DDD** is not about the technical part, this is about reducing the complexity of the domain model via ubiquitous language. The implementation may change from one language to other and this also can be applied to multiple architectures.

DDD changes our way of thinking about the system, going against the old CRUD model, which is strictly based on the Database model. DDD is not a evolutionary thing, but revolutionary, you can't introduce DDD without change the whole system.

Having this in mind, this is very useful to know what are the main concepts and what they imply to our codebase, such as: [[#Entity]], [[#Value Objects]] and [[#Aggregates & Roots]]. 

### Entity

An **Entity** in DDD is an object that may change its attributes without being a new thing, we do comparisons by ID. For instance, think about an object *Person* whom have an *Address*, this address may change, and this change does not implies a new *Person*, this is the same *Person* with different attribute through the time.

> (FOWLER, 2005) Objects that have a distinct identity that runs through time and different representations. [^2]

```ruby
interface Entity[T]
	id: T
end

class User << Entity[UUID]
	id: UUID
	name: String
	address: Address
end
```

> [!NOTE] 
> As I said before, this is not about technical implementation, so your ID may be anything, DDD won't force you to use one or other. Use what is needed, String, UUID (any version), Hash, Integers... 

### Value Objects

By other hand, an address may never change its street, if this change, we are not referring to the same address, but to a new one. This is, in fact, a **Value Object**. Thus way, we can affirm that every Value Object is mandatorily immutable, if a single attribute is different, this is a new instance, not the same thing never.

> (FOWLER, 2005) Objects that matter only as the combination of their attributes. Two value objects with the same values for all their attributes are considered equal. [^2]

```ruby
record Address
	state: String
	city: String
	street: String
	number: Integer
end
```

## Aggregates & Roots

**Aggregates** are about **Transactional Blocks**. For instance, this doesn't make sense to open the client's orders every time he logs-in. So, here, we have two **Transactional Blocks**: the User transactional block and the order.

This is also important to know the concept of **Aggregate Root**, basically this is an **Entity** responsible for guarantee the integrity of the **Aggregate** as a whole. 

> (FOWLER, 2013) A DDD aggregate is a cluster of domain objects that can be treated as a single unit. [...] Aggregates are the basic element of transfer of data storage - you request to load or save whole aggregates. Transactions should not cross aggregate boundaries.[^1]

```rb
interface AggregateRoot[T] << Entity[T]
end

class OrderItem << Entity[BarCode]
	id: BarCode
	name: String
	price: Cash
end

class Order << AggregateRoot[String]
	id: String
	items: List[OrderItem]
	
	company: Company
	buyer: Customer
	ship_to: Address
	ship_via: ShipWay
	...
end
```

This is also valid to know that some times this doesn't make sense to link objects via ID, but copying their values. 

Example: we have a customer that buys a product. Product has an ID, name, and price. When I (customer) buy a product, I can't create a bill that refers to that product, since its price, name may change over time, the same for the address of the customer at that time. So, we must copy the client's information, the product's information and the all the transaction stuff.

If we decided to refer to the user itself, not only we would be introducing logical inconsistencies to our data, but also violating the concept of Aggregate, since they should never pass their boundaries. 

[^1]: FOWLER, M. 2013. **DDD Aggregate.** https://martinfowler.com/bliki/DDD_Aggregate.html
[^2]: FOWLER, M. 2005. **Evans Classification**. https://martinfowler.com/bliki/EvansClassification.html
[^3]: JÚNIOR, E. 2023. **Entidades, Value Objects e Agregados: conceitos fundamentais para representar domínio em código.** https://youtu.be/EuNHj8r1ADM
