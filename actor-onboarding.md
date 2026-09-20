# Actor Onboarding & Verification

```mermaid
flowchart TB

    %% ==================================================
    %% MAIN ENTRY
    %% ==================================================

    START["Platform Onboarding"]

    START --> ROLE{"Select Actor Type"}

    ROLE --> FPO_START
    ROLE --> FARMER_START
    ROLE --> CONSUMER_START
    ROLE --> BULK_START
    ROLE --> ADMIN_START
    ROLE --> DRIVER_START
    ROLE --> COMPANY_START


    %% ==================================================
    %% FPO
    %% ==================================================

    subgraph FPO["FPO"]
        direction LR

        FPO_START["Register FPO"]
        FPO_DATA["Enter FPO Details"]
        FPO_VALIDATE["Validate Registration Number"]
        FPO_PENDING["Pending Review"]
        FPO_ADMIN["Admin Review"]
        FPO_DECISION{"Approved?"}
        FPO_ACTIVE["Verified / Active"]
        FPO_REJECT["Rejected"]
        FPO_READ["Read Only"]

        FPO_START --> FPO_DATA
        FPO_DATA --> FPO_VALIDATE
        FPO_VALIDATE --> FPO_PENDING
        FPO_PENDING --> FPO_ADMIN
        FPO_ADMIN --> FPO_DECISION

        FPO_DECISION -->|Yes| FPO_ACTIVE
        FPO_DECISION -->|No| FPO_REJECT

        FPO_PENDING --> FPO_READ

        FPO_ACTIVE --> FPO_LIST["Can List Produce"]
        FPO_ACTIVE --> FPO_TRANS["Can Transact"]

        FPO_READ --> FPO_BROWSE["Browse Listings"]
        FPO_READ --> FPO_FORECAST["View Demand Forecast"]
    end


    %% ==================================================
    %% FARMER
    %% ==================================================

    subgraph FARMER["Farmer"]
        direction LR

        FARMER_START["Register Farmer"]
        FARMER_DATA["Enter Farmer Details"]
        AADHAAR["Aadhaar Format + Checksum"]
        OTP_F["Phone OTP"]
        FARMER_CHECK{"Existing Aadhaar<br/>with Another Phone?"}
        FARMER_REVIEW["Manual Admin Review"]
        FARMER_ACTIVE["Farmer Account Active"]
        NEW_BADGE["New / Low Trust Seller Badge"]

        FARMER_START --> FARMER_DATA
        FARMER_DATA --> AADHAAR
        FARMER_DATA --> OTP_F

        AADHAAR --> FARMER_CHECK
        OTP_F --> FARMER_CHECK

        FARMER_CHECK -->|Yes| FARMER_REVIEW
        FARMER_CHECK -->|No| FARMER_ACTIVE

        FARMER_ACTIVE --> FARMER_SELL["Can Sell Directly"]
        FARMER_ACTIVE --> NEW_BADGE
    end


    %% ==================================================
    %% CONSUMER
    %% ==================================================

    subgraph CONSUMER["Consumer / Retail Buyer"]
        direction LR

        CONSUMER_START["Register Consumer"]
        CONSUMER_DATA["Name + Phone + Address"]
        CONSUMER_OTP["Phone OTP"]
        PAYMENT["Payment Gateway"]
        TOKEN["Tokenized UPI / Card"]
        CONSUMER_ACTIVE["Consumer Account Active"]
        BUY["Can Purchase"]

        CONSUMER_START --> CONSUMER_DATA
        CONSUMER_DATA --> CONSUMER_OTP
        CONSUMER_DATA --> PAYMENT
        PAYMENT --> TOKEN
        CONSUMER_OTP --> CONSUMER_ACTIVE
        TOKEN --> CONSUMER_ACTIVE
        CONSUMER_ACTIVE --> BUY
    end


    %% ==================================================
    %% BULK BUYER
    %% ==================================================

    subgraph BULK["Bulk Buyer"]
        direction LR

        BULK_START["Restaurant / Retailer / Hotel"]
        BULK_DATA["Business Details"]
        GST["GSTIN Format + Checksum"]
        BULK_PENDING["Pending Review"]
        BULK_ADMIN["Admin Verification"]
        BULK_DECISION{"Approved?"}
        BULK_ACTIVE["Verified / Active"]
        BULK_REJECT["Rejected"]
        BULK_READ["Read Only"]

        BULK_START --> BULK_DATA
        BULK_DATA --> GST
        GST --> BULK_PENDING
        BULK_PENDING --> BULK_ADMIN
        BULK_ADMIN --> BULK_DECISION

        BULK_DECISION -->|Yes| BULK_ACTIVE
        BULK_DECISION -->|No| BULK_REJECT

        BULK_PENDING --> BULK_READ

        BULK_READ --> BULK_BROWSE["Browse Listings"]
        BULK_ACTIVE --> BULK_ORDER["Place Bulk Orders"]
        BULK_ACTIVE --> UPFRONT["MVP: Upfront Payment"]
    end


    %% ==================================================
    %% ADMIN
    %% ==================================================

    subgraph ADMIN["Admin"]
        direction LR

        ADMIN_START["Admin Account"]
        ADMIN_DB["Provisioned Directly in Database"]
        ADMIN_ACTIVE["Admin Access"]

        ADMIN_START --> ADMIN_DB
        ADMIN_DB --> ADMIN_ACTIVE

        ADMIN_ACTIVE --> REVIEW_FPO["Review FPO"]
        ADMIN_ACTIVE --> REVIEW_FARMER["Review Farmer Exceptions"]
        ADMIN_ACTIVE --> REVIEW_BULK["Verify Bulk Buyers"]
        ADMIN_ACTIVE --> REVIEW_LOGISTICS["Review Logistics"]
    end


    %% ==================================================
    %% INDIVIDUAL DRIVER
    %% ==================================================

    subgraph DRIVER["Logistics Partner - Individual Driver"]
        direction LR

        DRIVER_START["Register Driver"]
        DRIVER_DATA["Driver + Vehicle Details"]
        DRIVER_VALIDATE["Validate License + RC Format"]
        DRIVER_LICENSE{"Duplicate License?"}
        DRIVER_VEHICLE{"Vehicle Already Active?"}
        DRIVER_PENDING["Pending Review"]
        DRIVER_ADMIN["Admin Review"]
        DRIVER_DECISION{"Approved?"}
        DRIVER_ACTIVE["Verified / Active"]
        DRIVER_REJECT["Rejected"]
        DRIVER_REVIEW["Manual Review"]
        SUSPEND_CHECK{"Failures / Complaints<br/>Above Threshold?"}
        DRIVER_SUSPEND["Suspended → Review"]

        DRIVER_START --> DRIVER_DATA
        DRIVER_DATA --> DRIVER_VALIDATE
        DRIVER_VALIDATE --> DRIVER_LICENSE

        DRIVER_LICENSE -->|Yes| DRIVER_REVIEW
        DRIVER_LICENSE -->|No| DRIVER_VEHICLE

        DRIVER_VEHICLE -->|Yes| DRIVER_REJECT
        DRIVER_VEHICLE -->|No| DRIVER_PENDING

        DRIVER_PENDING --> DRIVER_ADMIN
        DRIVER_ADMIN --> DRIVER_DECISION

        DRIVER_DECISION -->|Yes| DRIVER_ACTIVE
        DRIVER_DECISION -->|No| DRIVER_REJECT

        DRIVER_ACTIVE --> DRIVER_JOBS["Can Accept Jobs"]
        DRIVER_ACTIVE --> SUSPEND_CHECK

        SUSPEND_CHECK -->|Yes| DRIVER_SUSPEND
        SUSPEND_CHECK -->|No| DRIVER_JOBS
    end


    %% ==================================================
    %% TRANSPORT COMPANY
    %% ==================================================

    subgraph COMPANY["Logistics Partner - Transport Company / Aggregator"]
        direction LR

        COMPANY_START["Register Company"]
        COMPANY_DATA["Company + Fleet Details"]
        COMPANY_GST["GSTIN Validation"]
        COMPANY_PENDING["Pending Review"]
        COMPANY_ADMIN["Admin Review"]
        COMPANY_DECISION{"Approved?"}
        COMPANY_ACTIVE["Verified / Active"]
        COMPANY_REJECT["Rejected"]

        COMPANY_START --> COMPANY_DATA
        COMPANY_DATA --> COMPANY_GST
        COMPANY_GST --> COMPANY_PENDING
        COMPANY_PENDING --> COMPANY_ADMIN
        COMPANY_ADMIN --> COMPANY_DECISION

        COMPANY_DECISION -->|Yes| COMPANY_ACTIVE
        COMPANY_DECISION -->|No| COMPANY_REJECT

        COMPANY_ACTIVE --> API_CHECK{"API Integration?"}

        API_CHECK -->|Yes| API_ASSIGN["API Job Assignment"]
        API_CHECK -->|No| MANUAL_ASSIGN["Manual Dispatch"]

        COMPANY_ACTIVE --> COMPANY_JOBS["Can Accept Jobs"]
    end


    %% ==================================================
    %% AUTHENTICATION
    %% ==================================================

    subgraph AUTH["Authentication & Identity"]
        direction LR

        PHONE["Phone Number"]
        OTP["OTP Verification<br/>5 min expiry"]
        RESEND["Max 3 Resends"]
        LOCK["Temporary Lockout"]
        LOGIN["Login"]
        JWT["JWT Authentication"]
        ACCESS["Access Token<br/>~24h"]
        REFRESH["Refresh Token"]
        MULTI_ROLE["One Phone → Multiple Roles"]
        SWITCH["Role / Account Switch"]

        PHONE --> OTP
        OTP --> RESEND
        RESEND --> LOCK
        OTP --> LOGIN
        LOGIN --> JWT
        JWT --> ACCESS
        JWT --> REFRESH
        PHONE --> MULTI_ROLE
        MULTI_ROLE --> SWITCH
    end


    %% ==================================================
    %% TRUST SCORE
    %% ==================================================

    subgraph TRUST["Trust & Reputation"]
        direction LR

        BASE["Initial Trust Score<br/>Neutral Baseline"]
        HISTORY["Transaction / Delivery History"]
        SUCCESS["Successful History"]
        FAILURE["Failures / Complaints"]
        INCREASE["Trust Score ↑"]
        DECREASE["Trust Score ↓"]

        BASE --> HISTORY
        HISTORY --> SUCCESS
        HISTORY --> FAILURE
        SUCCESS --> INCREASE
        FAILURE --> DECREASE
    end


    %% ==================================================
    %% VERIFICATION PRINCIPLE
    %% ==================================================

    subgraph VERIFICATION["Verification Strategy"]
        direction LR

        PRINCIPLE["Verification Asymmetry"]

        SELLERS["Farmers + FPOs"]
        SELLER_RISK["Higher Verification<br/>Goods / Seller Risk"]

        BUYERS["Consumers + Buyers"]
        BUYER_RISK["Lighter Verification<br/>Payment Gateway Handles Risk"]

        PRINCIPLE --> SELLERS
        PRINCIPLE --> BUYERS
        SELLERS --> SELLER_RISK
        BUYERS --> BUYER_RISK
    end


    %% ==================================================
    %% CROSS CONNECTIONS
    %% ==================================================

    FPO_DATA -.-> PHONE
    FARMER_DATA -.-> PHONE
    CONSUMER_DATA -.-> PHONE
    BULK_DATA -.-> PHONE
    DRIVER_DATA -.-> PHONE
    COMPANY_DATA -.-> PHONE

    FPO_ACTIVE -.-> HISTORY
    FARMER_ACTIVE -.-> HISTORY
    DRIVER_ACTIVE -.-> HISTORY
    COMPANY_ACTIVE -.-> HISTORY

    HISTORY -.-> FPO
    HISTORY -.-> FARMER
    HISTORY -.-> DRIVER
    HISTORY -.-> COMPANY

    ADMIN_ACTIVE -.-> FPO_ADMIN
    ADMIN_ACTIVE -.-> FARMER_REVIEW
    ADMIN_ACTIVE -.-> BULK_ADMIN
    ADMIN_ACTIVE -.-> DRIVER_ADMIN
    ADMIN_ACTIVE -.-> COMPANY_ADMIN
