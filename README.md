# Workflow specification file (WSF)

WSF is a [YAML](https://yaml.org/) based [DSL](https://en.wikipedia.org/wiki/Domain-specific_language) used to describe workflow process as a [Finite State Machine (FSM)](https://en.wikipedia.org/wiki/Finite-state_machine).

## Syntax

All properties are writen in _camelCase_. Events, states and stateTransitions should be writen in _PascalCase_. System generated variables are writen in _UPPER_SNAKE_CASE_.

## Idea

Use FSM to describe workflow process and use _Event Sourcing_ to store events that happened in a process. Current state of FSM is not stored directly, but it is derived from stream of stored events, by applying related reducers.

WSF has three main sections: **states**, **events** and **eventsTransitions**.

### States

Respresents FSM available states. This is used to simplify conditional logic inside workflows. Each state has a limited set of available events that can be applied to it in order to transition to the new state.

**CURRENT_STATE_DATA** - state data calculated from stored events  
**CALCULATED_STATE_DATA** - state data calculated from stored events + applied event before it is stored

### Events

Respresents FSM available events. Events are used to move system from one state to another. Each event can define a set of input parameters as a dictionary, where key represents parameter name and value is parameter type. Events also define _validators_ and (only one) _reducer_

**EVENT_INPUTS** - variable containing values of event inputs  
**EXECUTOR_DATA** - variable containing values related to the user dispatching event, e.g. userId, roles, hierarchy...

#### Reducers

Reducers calculate ONLY data necessary for validation and other workflow conditionals

### Event Transitions

Event Transitions define available state chenges on certain events. System can only change state through defined state transition.

NULL_STATE - initial state (before workflow instance existed)
ANY_STATE - matches any state
