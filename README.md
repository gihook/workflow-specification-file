# Workflow specification file (WSF)

WSF is a [YAML](https://yaml.org/) based [DSL](https://en.wikipedia.org/wiki/Domain-specific_language) used to describe workflow process as a [Finite State Machine (FSM)](https://en.wikipedia.org/wiki/Finite-state_machine).

## Idea

Use FSM to describe workflow process and use _Event Sourcing_ to store events that happened in a process. Current state of FSM is not stored directly, but it is derived from stream of stored events, by applying related reducers.

FSM state is a label describing current state of the system (entity). Besides the FSM state, system (entity) contains state data, which contains data related to current FSM state.

WSF has three main sections: **states**, **events** and **eventsTransitions**.

## Syntax

All properties are writen in _camelCase_. Events, states and stateTransitions should be writen in _PascalCase_. System generated variables are writen in _UPPER_SNAKE_CASE_.

### States

Respresent FSM available states. This is used to simplify conditional logic inside workflows. Each state has a limited set of available events that can be applied to it in order to transition to the new state.

**CURRENT_STATE_DATA** - _state data_ calculated from stored events  
**CALCULATED_STATE_DATA** - _state data_ calculated from stored events + applied event before it is stored

### Events

Respresent FSM available events. Events are used to move system from one state to another. Each event can define a set of input parameters as a dictionary, where key represents parameter name and value is parameter type. Events also define _validators_ and (only one) _reducer_

**EVENT_INPUTS** - variable containing values of event inputs  
**EXECUTOR_DATA** - variable containing values related to the user dispatching event, e.g. userId, roles, hierarchy...

#### Inputs

Each event has a set of input parameters described in input property as key-value pars _paramName:paramType_
By default all parameters are mandatory if they are not suffixed with a question mark.

example:

```yaml
inputs:
  title: string
  description?: string # not mandatory
```

#### Reducers

Reducers are (**static**) functions that describe how _CURRENT_STATE_DATA_ is transformed when an event is applied. Each event has exactly one reducer.
Reducers signature can be defined as C# delegate:

```csharp
public static object Reduce(object previousStateData, object eventData, EventExecutor executor);
```

#### Validators

Validators are _simple_ (**static**) functions used to prevent events with invalid data to be applied. Function parameters are passed as a list in _params_ property. Validators are executed sequentaly

Validators signature can be defined as C# delegate:

```csharp
public static bool IsValid(params object[] parameters);
```

example:

```yaml
validators:
  - type: IsGreaterThan
    params: [EVENT_INPUTS.title, 5]
```

### Event Transitions

Event Transitions define available state chenges on certain events. System can only change state through defined state transition.

**NULL_STATE** - initial state (before workflow instance existed)  
**ANY_STATE** - matches any state
