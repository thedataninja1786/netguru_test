# System Design: SME-Guided Customer Support Classification

## 1. INPUT CHANNELS INGESTION 
### Technical Implementation
- **Integration Protocols**:
  - Email: IMAP/SMTP with OAuth authentication
  - Live Chat: WebSocket/REST API integration
  - Fallback: Web form submission endpoint
  Normalize data into a common format, such as JSON, with fields like:
    - Channel: Email/Live chat
    - Message content: The text body of the message
    - Customer details: Name, email, order ID


## 2. DATA PROCESSING 
### Knowledge Capture Strategy
Utilize SME past data to map customer queries to request categories.
Clean the data for consistent processing and storage. The text should be normalized by lowercasing,
removing stopwords and applying lemmatization for grouping together different forms of the same word.
Finally the clean data should be tokenized for mapping and rule-matching with the corresponding customer support team.

    Create detailed taxonomy of issue types with the following sample mapping structure
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
## 3. RULE-BASED CLASSIFIER

## Flowchart
    +--------------------------------------------------------+
    |              INPUT CHANNELS INGESTION                  |
    |  Ingest and aggregate data from available channhels    |
    |           (email, live chat, etc.)                     |
    +--------------------------------------------------------+
                            |
                            V
        +---------------------------------------------------+  -----------------------------------------
        |                DATA PROCESSING                    |                                           |
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
