System is composed of several componenets:
CQRS API:
  defines commands and queries
Event Sourcing handler:
  stores executed events and defines projections

CQRS API defines executable actions: Commands and Queries
  responability of a command is to VALIDATE and DISPATCH event
  responability of a query is to READ projections


# Event Sourcing concepts

State
- label marking certain position in a workflow

StateData
- complete data of a workflow in a certain point of time. This is derived from all executed events

Event
- label describing transition between two states. It includes EventData and Executor

EventData
- data related to the Event

Executor
- user id of user dispatching event (via command)
        
Reducer
- code (class implementing reducer interface) that is executed applying event when StateData is calculated. Reducer should not have external dependencies, i.e. StateData is calculated as series of in memory transformations

NullState
- initial state of every aggregate. This State does not need to be explicitly defined in workflow definition

Projection
- code executed when certain event is dispatched (and stored). This code calculates data consumed by the Queries. Data calculated here is OPTIMIZED for read operations and it can be in multiple formatas as well as in meny different databases (even of different type)

ANY_STATE
- placeholder for any state in event transitions

File specification

# File has three main sections: states, events and eventTransitions

## States

Respresents FSM available states. This is used to simplify conditional logic inside workflows. Each state has a limited set of available events that can be applied to it in order to transition to new state.

CURRENT_STATE_DATA is state data calculated from stored events
CALCULATED_STATE_DATA is state data calculated from stored events + applied event before it is stored

## Events

Respresents FSM available events. Events are used to move system from one state to another. Each event can define set of input parameters as a dictionary, where key represents parameter name and value is parameter type. Events also define *validators* and (only one) *reducer*

## Event Transitions

Event Transitions define available state chenges on certain events. System can only change state through defined state transition.
