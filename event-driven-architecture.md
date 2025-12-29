flowchart LR
    %% Clients
    Client[Client / UI]

    %% CQRS
    Client -->|Commands| CommandAPI[Command API]
    Client -->|Queries| QueryAPI[Query API]

    %% Command Side
    CommandAPI --> CommandService[Command Service]
    CommandService -->|State Change Events| EventStore[(Event Store)]

    %% Event Sourcing
    EventStore -->|Replay Events| CommandService

    %% Event Broker (Pub-Sub)
    EventStore -->|Publish Events| EventBroker[(Event Broker)]

    %% Microservices as Subscribers
    EventBroker --> InventoryService[Inventory Service]
    EventBroker --> PaymentService[Payment Service]
    EventBroker --> ShippingService[Shipping Service]

    %% Saga Orchestration
    InventoryService -->|Success/Failure Event| Saga[Saga Orchestrator]
    PaymentService -->|Success/Failure Event| Saga
    ShippingService -->|Success/Failure Event| Saga

    Saga -->|Compensating Events| EventBroker
    Saga -->|Next Step Event| EventBroker

    %% Read Side (CQRS)
    EventBroker --> ReadModelUpdater[Read Model Updater]
    ReadModelUpdater --> ReadDB[(Read Database)]
    QueryAPI --> ReadDB
