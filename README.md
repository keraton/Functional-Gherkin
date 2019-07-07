# Functional Gherkin

> Functional Gherkin is like gherkin but with the possibility to return a value 

## Gherkin improvement

A good BDD implementation should let the user to reuse and to compose of sets Gherkin instruction with ease. But in reality, this is difficult due to the statefull implementation which is *hidden* from the specification.
Given an example of a Gherkin scenario :

```
Scenario: Search Business Flight

Given search of a flight from "PARIS" to "NEW YORK"
And filter with "BUSINESS" class
When search is executed
Then search results contains flights from "PARIS" to "NEW YORK"
And all flights has "BUSINESS" class
```

For a specification prespective, this should be easy to understand. We want to test that search of flights from Paris to New York with business class only should return the right arrival-destination with the right class. But from a programmation prespective, we need to tell the program what is the relation between phrases. A relation in this example can be that the third phrase need to know that the search context such as arrival (Paris), destination (New York) and class (business). Another relation is that the fourth and fifth phrase need to have the flight results which come from the third phrase. Those relations force us to save the needed informations between phrases (context).

Because of this need, most likely when we write a BDD implementation we write a statefull programation. And unfortunately statefull programmation is hard, hard to maintain and harder to scale. This is why an improved Gherkin is interesting, we need a new set of Gherkin instruction that let us to return one/multiple values. So with this mechanism, we don't need to store any context by ourself. That should be managed by the implementor library. When this become possible, we should be able to keep our BDD stateless, therefore easier to maintain. In the next part we will see how we can achieve this.

## Gherkin new instruction

Gherkin in actual state are defined as statements, they doesn't support the return value. We need then to transform these statements to expressions, we need to add returning value mechanism in Gherkin language. Lets take previous example and try to transform them to expressions.

### Using 'return' keyword

Like most of programming language we are going to introduce the return keyword to the example. We also modify the phrases to use the returning value in their arguments.

```
Scenario: Search Business Flight

Given search of a flight from "PARIS" to "NEW YORK", return "SEARCH CONTEXT"
Given existing "SEARCH CONTEXT" use "BUSINESS" class, return "SEARCH CONTEXT"
When search with "SEARCH CONTEXT" is executed, return "FLIGHTS"
Then "FLIGHTS" contains flights from "PARIS" to "NEW YORK" return "FLIGHTS"
And all "FLIGHTS" has "BUSINESS" class return "FLIGHTS"

```

In this example we transform all Gherkin statements into expressions. Now we don't need to store information between phrases. The context is transfered using return value from the precedent phrase to argument in the next phrase. We manage then transform our statefull implementation to a stateless one. But that comes with a cost, Gherkin specifications should be written in a *spoken language*, by adding the return keyword, we lose this feature. 

### Using implicit return


## Reference
1. https://cucumber.io/docs/gherkin/reference/ 
