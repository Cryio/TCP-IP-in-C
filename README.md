# TCP-IP-in-C

**TCP-IP-in-C** is a custom implementation of a TCP/IP stack written entirely in C. It is designed to deepen your understanding of network protocols by building the core functionalities from scratch. This project focuses on critical aspects of TCP, including header parsing, the connection state machine, congestion control mechanisms, and the retransmission timeout strategy.



## Features

- **TCP Header Parsing**  
  Efficiently parses TCP headers to extract and interpret vital information from network packets.

- **State Machine Implementation**  
  Manages TCP connection states such as `LISTEN`, `SYN-SENT`, `ESTABLISHED`, `FIN-WAIT`, and others according to the TCP state transition diagram.

- **Congestion Control**  
  Implements algorithms to dynamically adjust the transmission rate based on network congestion.

- **Retransmission Timeout**  
  Dynamically calculates and manages retransmission timeouts to ensure reliable data delivery.

The TCP connection state machine is a central component in ensuring a reliable and orderly exchange of data. This implementation follows the principles set out in [RFC 793](https://datatracker.ietf.org/doc/html/rfc793), while incorporating improvements from later updates.

### Key TCP States

| **State**       | **Description**                                                                                                                                                         |
|-----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **CLOSED**      | - The initial state where no connection exists.<br>- No data can be transmitted or received.                                                                            |
| **LISTEN**      | - The server waits for an incoming connection request (`SYN`).<br>- On receiving a `SYN`, transitions to `SYN-RECEIVED`.                                                |
| **SYN-SENT**    | - The client, having sent a `SYN`, awaits a `SYN-ACK` from the server.<br>- Critical for initiating a connection.                                                       |
| **SYN-RECEIVED**| - After a server receives a `SYN`, it responds with a `SYN-ACK` and transitions to this state.<br>- Awaits the final `ACK` from the client to move to `ESTABLISHED`.     |
| **ESTABLISHED** | - Indicates that the connection is fully open.<br>- Both parties can send and receive data.                                                                             |
| **FIN-WAIT-1**  | - Initiated when a connection shutdown begins.<br>- The endpoint sends a `FIN` and waits for an acknowledgment.                                                         |
| **FIN-WAIT-2**  | - The endpoint moves here after receiving an `ACK` for its `FIN`.<br>- Awaits a `FIN` from the remote side.                                                              |
| **CLOSE-WAIT**  | - Occurs when an endpoint receives a `FIN` while in `ESTABLISHED`.<br>- The local side can still send remaining data before closing.                                    |
| **LAST-ACK**    | - The endpoint that initiated closure waits for an `ACK` of its `FIN`.<br>- Once the `ACK` is received, the connection can be fully terminated.                         |
| **TIME-WAIT**   | - After both sides have closed, the connection enters `TIME-WAIT`.<br>- Ensures all packets are flushed from the network.<br>- Lasts for a duration of `2 * MSL`.       |


### Simplified State Diagram

Below is a diagram representing a simplified version of the TCP connection state machine.

```mermaid
flowchart TD
    CLOSED((CLOSED))
    LISTEN((LISTEN))
    SYN_SENT((SYN-SENT))
    SYN_RCVD((SYN-RECEIVED))
    ESTABLISHED((ESTABLISHED))
    FIN_WAIT_1((FIN-WAIT-1))
    FIN_WAIT_2((FIN-WAIT-2))
    CLOSE_WAIT((CLOSE-WAIT))
    LAST_ACK((LAST-ACK))
    TIME_WAIT((TIME-WAIT))
    
    CLOSED -->|Active Open| SYN_SENT
    CLOSED -->|Passive Open| LISTEN
    LISTEN -->|Receive SYN| SYN_RCVD
    SYN_SENT -->|Receive SYN+ACK| ESTABLISHED
    SYN_RCVD -->|Send ACK| ESTABLISHED
    ESTABLISHED -->|Close| FIN_WAIT_1
    ESTABLISHED -->|Receive FIN| CLOSE_WAIT
    FIN_WAIT_1 -->|Receive ACK| FIN_WAIT_2
    FIN_WAIT_2 -->|Receive FIN| TIME_WAIT
    TIME_WAIT -->|Timeout| CLOSED
    CLOSE_WAIT -->|Application Close| LAST_ACK
    LAST_ACK -->|Receive ACK| CLOSED
```
