# Introduction to Rules

EMQX provides rules based on SQL syntax for processing and converting messages or events, such as converting data types, encoding or decoding messages, conditional branch judgment, etc.
Rules are built into EMQS, and there is no overhead of message serialization and network transmission, so it runs very efficiently.

## Composition of Rules


The rules describe **data source**, **data processing process**, and **processing result destination**:

- **data source**: the data source of a rule can be a message or event, or an external data system. Rules specify the source of data through the FROM clause of SQL;

- **data processing**: rules describe the data processing process through SQL statements and functions. The WHERE clause of SQL is used to filter data, the SELECT clause and SQL function are used to extract and transform data;

- **processing result destination**: a rule can define one or more actions to process SQL output results. If the SQL execution passes, the rules will perform corresponding actions in sequence, such as storing the processing results in the database or republishing them to another mqtt topic.

```
┌─────────────────┐           ┌────────────────────┐           ┌───────────────────────┐
│                 │           │                    │           │                       │
│   DATA SOURCE   ├──────────►│    DATA PROCESS    ├──────────►│ HANDLE PROCESS RESULT │
│                 │           │       (SQL)        │           │       (Actions)       │
└─────────────────┘           └────────────────────┘           └───────────────────────┘
```

### Introduction to rule SQL statements

SQL statements are used to specify the data source of rules, define data processing procedures, and so on. An example of an SQL statement is given below:

```SQL
SELECT
    payload.data as d
FROM
    "t/#"
WHERE
    clientid = "foo"
```

In the above SQL:

- Data Source: the messages with topic `t/#`;
- Data Processing Process: If the client ID of the message is `foo`, select the `data` field from the message content and assign it to the new variable `d`

::: warning
The dot (".") syntax requires that the data must be of JSON or Map type. If it is of other data types, SQL functions must be used for data type conversion.
:::

For the SQL statement format and usage of rules, see [SQL syntax](./rule-sql-grammar-and-examples.md).

### Actions

Actions are components used to process the output results of rules and determine the final destination of data. 详见 [actions](./rule-actions.md)

## Examples of Typical Use Cases of Rules

- Action monitoring: in the development of smart home smart door locks, the door locks will be offline due to network, power failure, man-made damage and other reasons, resulting in abnormal functions. Use rule configuration to monitor offline events and push the fault information to the application service, so as to realize the ability of fault detection at the access layer at the first time;

- Data filtering: in the truck fleet management of the Internet of vehicles, vehicle sensors collect and report a large amount of operation data. The application platform only focuses on the data when the vehicle speed is greater than 40 km/h. In this scenario, rules can be used to filter messages conditionally and write data that meets the conditions to the business message queue;

- Message routing: in the intelligent billing application, the terminal equipment distinguishes the service types through different topics. The charging service messages can be connected to the charging message queue through the configuration rules and send a confirmation notice to the service system after the messages arrive at the device end. The non charging information is connected to other message queues to realize the service message routing configuration;

- Message encoding and decoding: in other public/private TCP protocol access, industrial control industry and other application scenarios, the encoding and decoding of binary/special format message bodies can be done through the regular local processing functions (which can be customized and developed on emqx); It can also flow relevant messages to external computing resources such as function calculation for processing through regular message routing (the processing logic can be developed by the user), and turn the messages into JSON format that is easy to handle, simplifying the difficulty of project integration and improving the ability of rapid application development and delivery.