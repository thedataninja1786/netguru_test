# System Design: SME-Guided Customer Support Classification

## 1. Input Channels Aggregator
### Technical Implementation
- **Integration Protocols**:
  - Email: IMAP/SMTP with OAuth authentication
  - Live Chat: WebSocket/REST API integration
  - Fallback: Web form submission endpoint
  Normalize data into a common format, such as JSON, with fields like:
    - Channel: Email/Live chat
    - Message content: The text body of the message
    - Customer details: Name, email, order ID


## 2. DATA PREPROCESSING 
### Knowledge Capture Strategy
Utilize SME past data to map customer queries to request categories.
  2. Create detailed taxonomy of issue types


### Keyword Mapping Structure
SUPPORT_CATEGORIES = {
    'order_tracking': {
        'keywords': ['track', 'shipping', 'delivery', 'order status'],
        'priority': 2,
        'team': 'logistics'
    },
    'payment_errors': {
        'keywords': ['payment', 'charge', 'declined', 'refund'],
        'priority': 3,
        'team': 'finance'
    },
    # Additional categories...
}


## Flowchart
    +--------------------------------------------------------+
    |              INPUT CHANNELS INGESTION                  |
    |  Ingest and aggregate data from available channhels    |
    |           (email, live chat, etc.)                     |
    +--------------------------------------------------------+
                            |
                            V
        +---------------------------------------------------+  -----------------------------------------
        |                DATA PREPROCESSING                 |                                           |
        | (Text cleaning, tokenization, normalization, etc.)|                                           |
        +---------------------------------------------------+                                           |
                            |                                                                           |
                            V                                                                           V
        +-----------------------------------------+                                 +-----------------------------------------+
        |           RULE-BASED CLASSIFIER         |   --------------------------->  |             WORD EMBEDDINGS             |
        |  SME-defined rules, regex patterns      |   <---------------------------  |  Entities that resemble the user-query  |
        +-----------------------------------------+                                 +-----------------------------------------+
                            |
                            V
    +------------------------------------------------------------+
    |                  TICKET GENERATION                         |
    | (Predefined templates + dynamic fields like order ID)      |
    +------------------------------------------------------------+
                            |
                            V
        +-------------------------------------------+
        |           JIRA INTEGRATION LAYER          |
        |    Deliver tickets via Jira REST API      |
        |           to customer support             |
        +-------------------------------------------+
