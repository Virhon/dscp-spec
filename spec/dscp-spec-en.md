![DSCP-logo](./assets/dscp-logo-full.jpeg)
## Abstract

@startuml
autonumber
actor "Solids App (Donor)" as App
participant "Bank / OpenBanking" as Bank
participant "UBP Gateway" as UBP
participant "DSCP Node" as Node

note over Node: CGR decomposed into PGR\nTimer starts (e.g. 3h window)

Node -> App: PGR issued (pgr_id, amount, marker)
App -> Bank: Initiate payment with marker in purpose
Bank --> UBP: Statement / payment event in cache
UBP -> Node: Signed callback: PaymentConfirmed(pgr_id, tx_ref)

alt Marker & amount valid within window
Node -> Node: Mark PGR fulfilled, update Circle state
Node --> App: Ack / status update
else Timeout or unfulfilled PGR
Node -> Node: Collapse balances (Dr. DEPOSIT, Cr. CREDIT)
loop 3 attempts in Circle
Node -> App: Re-issue PGR to next +PL donor
end
end
@enduml