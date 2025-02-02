# Credit Card - Single Payment
```mermaid
%%{init: {'theme': 'default'} }%%
sequenceDiagram
    participant Invoicing
    participant MCF
    participant Payments
    participant SAP
    Invoicing->>MCF: Invoice Created 100
    MCF->>Payments: Charge 100
    Note over MCF: Cash1: 100<br/>PaidAmount: 100
    MCF->>SAP: Post 100   
```
<br/>
<br/>
<br/>

# Check/Wire - Single Payment
```mermaid
%%{init: {'theme': 'default'} }%%
sequenceDiagram
    participant Invoicing
    participant MCF
    participant SAP
    Invoicing->>MCF: Invoice Created 100    
    SAP->>MCF: Post 100
    Note over MCF: Cash1: 100<br/>PaidAmount:100
```

<br/>
<br/>
<br/>
 
# Check/Wire - Multiple Payments
```mermaid
%%{init: {'theme': 'default'} }%%
sequenceDiagram
    participant Invoicing
    participant MCF
    participant SAP
    Invoicing->>MCF: Invoice Created 100
    SAP->>MCF: Post 50
    Note over MCF: Cash1: 50<br/>PaidAmount:50
    SAP->>MCF: Post -50
    Note over MCF: Cash1: 50<br/>Cash2: -50<br/>PaidAmount:0
    SAP->>MCF: Post 100
    Note over MCF: Cash1: 50<br/>Cash2: -50<br/>Cash3: 100<br/>PaidAmount:100
```
<br/>
<br/>
<br/>

# Credit Card - Rebill of Paid Invoice - Same Amount
```mermaid
%%{init: {'theme': 'default'} }%%
sequenceDiagram
    participant Invoicing
    participant MCF
    participant Payments
    participant SAP
    Invoicing->>MCF: Invoice1 Created 100
    MCF->>Payments: Charge 100
    Note over MCF: Invoice1.Cash1: 100<br/>Invoice1.PaidAmount: 100
    MCF->>SAP: Post 100 on Invoice1
    Invoicing->>MCF: Rebill Invoice2 Created 100
    Note over MCF: Invoice1 voided
    MCF->>MCF: Move 100 from Invoice1 to Invoice2
    Note over MCF: Invoice1.PaidAmount: 0
    Note over MCF: Invoice2.Cash1: 100<br/>Invoice2.PaidAmount: 100
```

<br/>
<br/>
<br/>
 
# Credit Card - Rebill of Unpaid invoice
```mermaid
%%{init: {'theme': 'default'} }%%
sequenceDiagram
    participant Invoicing
    participant MCF
    participant Payments
    participant SAP
    Invoicing->>MCF: Invoice1 Created 100
    Invoicing->>MCF: Rebill Invoice2 Created 80
    Note over MCF: Invoice1 voided
    MCF->>Payments: Charge 80 on Invoice2
    Note over MCF: Invoice2.Cash1: 80<br/>Invoice2.PaidAmount: 80
    MCF->>SAP: Post 80
```

<br/>
<br/>
<br/>
 
# Check/Wire - Rebill of Paid Invoice - Same Amount
```mermaid
%%{init: {'theme': 'default'} }%%
sequenceDiagram
    participant Invoicing
    participant MCF
    participant SAP
    Invoicing->>MCF: Invoice1 Created 100    
    SAP->>MCF: Post 100
    Note over MCF: Invoice1.Cash1: 100<br/>Invoice1.PaidAmount:100
    Invoicing->>MCF: Rebill Invoice2 Created 100
    Note over MCF: Invoice1 voided
    Note over MCF: Invoice2.PaidAmount: 0
    SAP->>MCF: Post -100 on Invoice1
    Note over MCF: Invoice1.Cash1: 100<br/>Invoice1.Cash2: -100<br/>Invoice1.PaidAmount:0
    SAP->>MCF: Post 100 on Invoice2
    Note over MCF: Invoice2.Cash1: 100<br/>Invoice2.PaidAmount:100
```

<br/>
<br/>
<br/>

# Check/Wire - Rebill of Paid Invoice - Lower Amount
```mermaid
%%{init: {'theme': 'default'} }%%
sequenceDiagram
    participant Invoicing
    participant MCF
    participant SAP
    Invoicing->>MCF: Invoice1 Created 100    
    SAP->>MCF: Post 100
    Note over MCF: Invoice1.Cash1: 100<br/>Invoice1.PaidAmount:100
    Invoicing->>MCF: Rebill Invoice2 Created 80
    Note over MCF: Invoice1 voided
    SAP->>MCF: Post -100 on Invoice1
    Note over MCF: Invoice1.Cash1: 100<br/>Invoice1.Cash2: -100<br/>Invoice1.PaidAmount:0
    Note over SAP: Move 20 to POA??
    SAP->>MCF: Post 80 on Invoice2
    Note over MCF: Invoice2.Cash1: 80<br/>Invoice2.PaidAmount:80
```

<br/>
<br/>
<br/>

# Chec/Wire - Rebill of Unpaid invoice
```mermaid
%%{init: {'theme': 'default'} }%%
sequenceDiagram
    participant Invoicing
    participant MCF
    participant SAP
    Invoicing->>MCF: Invoice1 Created 100    
    Invoicing->>MCF: Rebill Invoice2 Created 80
    Note over MCF: Invoice1 voided
    SAP-->>MCF: No reversal since there was no payment??
    SAP->>MCF: Post 80 on Invoice2
    Note over MCF: Invoice2.Cash1: 80<br/>Invoice2.PaidAmount:80
```

<br/>
<br/>
<br/>

# Mixed - Check payment received after invoice already paid using credit card
```mermaid
%%{init: {'theme': 'default'} }%%
sequenceDiagram
    participant Invoicing
    participant MCF
    participant Payments
    participant SAP
    Invoicing->>MCF: Invoice Created 100
    MCF->>Payments: Charge 100
    Note over MCF: Cash1: 100<br/>PaidAmount: 100
    MCF-->>SAP: Failed to Post Cash1 100
    SAP->>MCF: Post 100
    Note over MCF: Cash1: 100<br/>Cash2: 100<br/>PaidAmount: 200 !!
    MCF->>Payments: Refund -100
    Note over MCF: Cash1: 100<br/>Cash2: 100<br/>Cash3: -100<br/>PaidAmount: 100
    MCF-->>SAP: Post Cash1 100 (THIS WILL FAIL initially as invoice already cleared in SAP)
    MCF->>SAP: Post Cash3 -100
    MCF->>SAP: Post Cash1 100 (NOW THIS WILL SUCCEED)
```
<br/>
<br/>
<br/>

# Rebill of invoice which had both credit card and check payments
```mermaid
%%{init: {'theme': 'default'} }%%
sequenceDiagram
    participant Invoicing
    participant MCF
    participant Payments
    participant SAP
    Invoicing->>MCF: Invoice1 Created 100
    MCF->>Payments: Charge 30
    Note over MCF: Invoice1.Cash1: 30<br/>Invoice1.PaidAmount: 30
    MCF->>SAP: Post Cash1 30
    SAP->>MCF: Post Cash2 70
    Note over MCF: Invoice1.Cash1: 30<br/>Invoice1.Cash2: 70<br/>Invoice1.PaidAmount: 100
    Invoicing->>MCF: Invoice2 rebill of Invoice1 Created 100
    MCF->>MCF: Invoice1 voided
    MCF->>MCF: Move Cash1 (Credit Card) from Invoice1 to Invoice2
    Note over MCF: Invoice1.Cash2: 70<br/>Invoice1.PaidAmount: 70
    Note over MCF: Invoice2.Cash1: 30<br/>Invoice2.PaidAmount: 30<br/>Invoice2.Amount: 100
    SAP->>MCF: Post Cash3 -70 on Invoice1
    Note over MCF: Invoice1.Cash2: 70<br/>Invoice1.Cash3: -70<br/>Invoice1.PaidAmount: 0
    SAP->>MCF: Post Cash4 70 on Invoice2
    Note over MCF: Invoice2.Cash1: 30<br/>Invoice2.Cash4: 70<br/>Invoice2.PaidAmount: 100
```
<br/>
<br/>
<br/>

# Rebill of invoice which had both credit card and check payments - Partial Payment
```mermaid
%%{init: {'theme': 'default'} }%%
sequenceDiagram
    participant Invoicing
    participant MCF
    participant Payments
    participant SAP
    Invoicing->>MCF: Invoice1 Created 100
    MCF->>Payments: Charge 30
    Note over MCF: Invoice1.Cash1: 30<br/>Invoice1.PaidAmount: 30
    SAP->>MCF: Post Cash2 20
    Note over MCF: Invoice1.Cash1: 30<br/>Invoice1.Cash2: 20<br/>Invoice1.PaidAmount: 50
    Invoicing->>MCF: Invoice2 rebill of Invoice1 Created 100
    MCF->>MCF: Invoice1 voided
    MCF->>MCF: Move Cash1 (Credit Card) from Invoice1 to Invoice2
    Note over MCF: Invoice1.Cash2: 20<br/>Invoice1.PaidAmount: 20
    Note over MCF: Invoice2.Cash1: 30<br/>Invoice2.PaidAmount: 30<br/>Invoice2.Amount: 100
    SAP->>MCF: Post Cash3 -20 on Invoice1
    Note over MCF: Invoice1.Cash2: 20<br/>Invoice1.Cash3: -20<br/>Invoice1.PaidAmount: 0
    SAP->>MCF: Post Cash4 20 on Invoice2
    Note over MCF: Invoice2.Cash1: 30<br/>Invoice2.Cash4: 20<br/>Invoice2.PaidAmount: 50<br/>Invoice2.Amount: 100
    MCF->>Payments: Charge 50
    Note over MCF: Invoice2.Cash1: 30<br/>Invoice2.Cash4: 20<br/>Invoice2.Cash5: 50<br/>Invoice2.PaidAmount: 100
```
<br/>
<br/>
<br/>

# Credit Card - Rebill of invoice with both credit card and check payments but check payments not yet posted to MCF
```mermaid
%%{init: {'theme': 'default'} }%%
sequenceDiagram
    participant Invoicing
    participant MCF
    participant Payments
    participant SAP
    Invoicing->>MCF: Invoice1 Created 100
    MCF->>Payments: Charge 30
    Note over MCF: Invoice1.Cash1: 30<br/>Invoice1.PaidAmount: 30
    MCF->>SAP: Post 30
    SAP->>SAP: Check payment for 70
    SAP-->>MCF: Failed to post check payment
    Note over MCF: Invoice1.Cash1: 30<br/>Invoice1.PaidAmount: 30
    Invoicing->>MCF: Invoice2 rebill of Invoice1 Created 100
    MCF->>MCF: Invoice1 voided
    MCF->>MCF: Move Cash1 (Credit Card) from Invoice1 to Invoice2
    Note over MCF: Invoice1.PaidAmount: 0
    Note over MCF: Invoice2.Cash1: 30<br/>Invoice2.PaidAmount: 30<br/>Invoice2.Amount: 100
    MCF->>Payments: Charge 70
    Note over MCF: Invoice2.Cash1: 30<br/>Invoice2.Cash4: 70<br/>Invoice2.PaidAmount: 100
    MCF-->>SAP: Post 70 (THIS WILL FAIL as invoice already cleared in SAP)
    SAP->>MCF: Post 70 on Invoice1
    Note over MCF: Invoice1.Cash2: 70<br/>Invoice1.PaidAmount: 70
    SAP->>MCF: Post -70 on Invoice1
    Note over MCF: Invoice1.Cash2: 70<br/>Invoice1.Cash5: -70<br/>Invoice1.PaidAmount: 0
    SAP->>MCF: Post 70 on Invoice2
    Note over MCF: Invoice2.Cash1: 30<br/>Invoice2.Cash4: 70<br/>Invoice2.Cash6: 70<br/>Invoice2.PaidAmount: 170 !!
    MCF->>Payments: Refund -70
    Note over MCF: Invoice2.Cash1: 30<br/>Invoice2.Cash4: 70<br/>Invoice2.Cash6: 70<br/>Invoice2.Cash7: -70<br/>Invoice2.PaidAmount: 100
    MCF->>SAP: Post -70
    MCF->>SAP: Post Cash4 70 (THIS WILL SUCCEED NOW)
    
```
