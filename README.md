# Functional Gherkin

> Functional Gherkin is like gherkin but with the possibility to return a value 

## Gherkin inconvenient

A good BDD implementation should let the user to reuse and to compose of sets Gherkin instruction with ease.
But the reality is that most of the time the notion of state is hidden from the specification.
For example, given a Gherkin scenario :

Scenario: Search Business Flight

Given search of a flight from "PARIS" to "NEW YORK"
And filter with "BUSINESS" class
When search is executed
Then return flights from "PARIS" to "NEW YORK"
And all flights has "BUSINESS" class

For a specification prespective, this should be easy to understand.
We want to test that search of flights from Paris to New York with business class only should return the right arrival-destination with the right class.
But from a programmation prespective, we need to tell the program what is the relation between phrases.
A relation in this example can be that the third phrase need to know that the search properties are arrival : Paris, destination : New York and class : business. 
Another relation is that the fourth and fifth phrase need to have the flight results which come from the third phrase.
In order to manage these relations, we don't have the choice, we need to save those informations somewhere. we introduce what we called a state.

Because of this need, most likely when we write a BDD implementation we write a statefull programation.
And unfortunately statefull programmation is hard, hard to maintain and harder to scale.

This is why a functional gherkin is interesting, with a discipline and a set of new gherkin instruction we can keep our BDD stateless, therefor easier to maintain.


## Reference
1. https://cucumber.io/docs/gherkin/reference/ 
