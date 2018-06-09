Event-Driven Architecture

Events - Some change in the system, change in current state.

Events or commands - Basically both are doing changes  on the system. In case of commands caller waits for its state and completion, whereas events processed asynchronously.

Advantages : 

a. Decoupling - Decoupled different components which can handle events on their own without affecting caller.

b. Extensibility - Any other system can hook up to the system and process events very easily. No modifications require in existing workflow.


# Different models :

## 1. Event Notification

System emits events to some well-defined place(queue). Other components first registers for events and gets notified(upon receiving events) via notifications.

Biggest disadvantage is to debug as there is no defined flow. One need to trace events on all components and identify the issue.
Most of the companies using this approcah.

# 2. Event-carried State Transfer

Events usually carry less data, so components need to go and fetch data to apply change from main system. This can generate singnificant amount of traffic. Issue can be solved either by putting all the necessary info in events(Can't decide as each registered component has its own requirement) or maintains another set of original data.

Maintaining another set of data provides more decoupling from the main system, but require data syncing, consistency.

# 3. Event Sourcing

Maintains audit/historic state for all the events, Provides a way to rebuild state. Any application state can be rebuild by audit logs.
Version control systems are another example of event sourcing. Real life example - Ledgers.

# 4. CQRS

Command Querying Responsbility Segregation
Separate components for read and write.
