# Stateless Gherkin RFC

> Stateless Gherkin is like gherkin, but with the possibility to return a value 

## Motivation

We thought that the actual Gherkin specification can be improved by adding the possibility to have a return value in their specification. Please see this README in order to understand better the proposed improvement, solution and the functional specification. We are open to any comments, suggestion, review, and critics, you can subscribe as an issue.

## Gherkin improvement

A good BDD implementation should let the user to reuse and to compose of sets Gherkin instruction with ease. However, in reality, this is difficult due to the stateful implementation that is *hidden* from the specification.

Given an example of a Gherkin scenario:

```
Scenario: Search Business Flight

Given search of a flight from "PARIS" to "NEW YORK"
And filter with "BUSINESS" class
When search is executed
Then search results contains flights from "PARIS" to "NEW YORK"
And all flights has "BUSINESS" class
```

For a specification perspective, this should be easy to understand. We want to test that search of flights from Paris to New York with business class only should return the right arrival-destination with the right class. However, from a programming perspective, we need to tell the program what is the relation between phrases. A relation in this example can be that the third phrase needs to know that the search context such as arrival (Paris), destination (New York) and class (business). Another relation is that the fourth and fifth phrase need to have the flight results which come from the third phrase. Those relations force us to save the needed information between phrases (context).

Because of this need, most likely when we write a BDD implementation we write stateful programming. And unfortunately, stateful programming is hard, hard to maintain and harder to scale. This is why an improved Gherkin is interesting, we need a new set of Gherkin instruction that let us return one/multiple values. So with this mechanism, we don't need to store any context by yourself. That should be managed by the implementer library. When this becomes possible, we should be able to keep our BDD stateless. Therefore, easier to maintain. In the next part, we will see how we can achieve this.

## Gherkin return instruction

Gherkin in the actual state is defined as statements, they don't support the return value. We need then to transform these statements into expressions, we need to add returning value mechanism in Gherkin language. Let's take the previous example and try to transform them into expressions.

### Using 'return' keyword

Like most of the programming language, we are going to introduce the return keyword to the example. We also modify the phrases to use the returning value in their arguments.

```
Scenario: Search Business Flight

Given search of a flight from "PARIS" to "NEW YORK", return "SEARCH CONTEXT"
Given existing "SEARCH CONTEXT" use "BUSINESS" class, return "SEARCH CONTEXT"
When search with "SEARCH CONTEXT" is executed, return "FLIGHTS"
Then "FLIGHTS" contains flights from "PARIS" to "NEW YORK" return "FLIGHTS"
And all "FLIGHTS" has "BUSINESS" class return "FLIGHTS"

```

In this example, we transform all Gherkin statements into expressions. Now we don't need to store information between phrases. The context is transferred using the return value from the precedent phrase to the argument in the next phrase. We manage then transform our stateful implementation to a stateless one. However, that comes with a cost, Gherkin specifications should be written in a *spoken language*, by adding the return keyword, we lose this feature.

The workaround for this issue would be to separate the return in another column.

```
Scenario: Search Business Flight

Given search of a flight from "PARIS" to "NEW YORK"           return "SEARCH CONTEXT"
Given existing "SEARCH CONTEXT" use "BUSINESS" class          return "SEARCH CONTEXT"
When search with "SEARCH CONTEXT" is executed                 return "FLIGHTS"
Then "FLIGHTS" contains flights from "PARIS" to "NEW YORK"    return "FLIGHTS"
And all "FLIGHTS" has "BUSINESS" class                        return "FLIGHTS"

```
This way we can separate between the usual Gherkin with the return value.

### Using implicit return

Instead of using an explicit return, we will define the return value implicitly. We remove the 'return' keyword in the previous example and declare them differently.

```
Scenario: Search Business Flight

Given "SEARCH" of a flight from "PARIS" to "NEW YORK"
And filter "SEARCH" with "BUSINESS" class
When run "SEARCH" to get "SEARCH RESULTS"
Then "SEARCH RESULTS" contains flights from "PARIS" to "NEW YORK"
And all "SEARCH RESULTS" has "BUSINESS" class
```

In this example, we are using SEARCH and SEARCH RESULT as the returning values. We see that there is no distinction between argument and return. However, technically we still can declare which of the arguments is an input or output, some of the argument are both input and output. This is called the procedural style.

The inconvenient of this solution is that even this is better than the explicit return solution, some of the phrases are still awkward. This is especially true for ```When run "SEARCH" to get "SEARCH RESULTS"```. The phrase can be less natural. Another issue with this solution is that the input and output can be confused, when the BDD has a lot of phrases, this can be a problem. Prefixing the argument can help this issue.

```
Scenario: Search Business Flight

Given ">SEARCH" of a flight from "@PARIS" to "@NEW YORK"
And filter "@>SEARCH" with "@BUSINESS" class
When run "@SEARCH" to get ">SEARCH RESULTS"
Then "@>SEARCH RESULTS" contains flights from "PARIS" to "NEW YORK"
And all "@>SEARCH RESULTS" has "BUSINESS" class
```

In this example, we prefix the input with @ and the output with >.

### Explicit vs Implicit

Both solutions have inconvenient, but we see that adding return keyword break the spoken language requirement. This is a blocking issue for us. Forward we will use the implicit solution.

## Functional specifications

We try to implement as much as possible the functional style for this stateless Gherkin. These are the list of functional specifications of stateless gherkin. Note: Specification ~ 1 phrase

- Specification is defined as expression
- Specification should have one output
- Specifications can have input
- Specifications can have no input 
- Specifications are stateless
- The output of *n* phrase can be used for all *n+* phrases in the same scenario
- A specification can override an output argument if they are using the same name
- Output and input can be mutable.
- Input can be provided in the phrase as a primitive (String, Number, Boolean) and Enum.
- Input can be provided by an output of the preceding specification
- The scope of input and output is scenario
- Passing value between phrases is done by the implementer framework
- Assume that the last specification is successfull before run the specification


## Reference
1. https://cucumber.io/docs/gherkin/reference/ 
