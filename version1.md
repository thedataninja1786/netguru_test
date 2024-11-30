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
### SME-defined rules to map requests to the corresponding support team
Implement a rule-based classifier that assigns the incoming (processed) query to a category.
For more robust assignment, word-embeddings can be also implemented for retrieving other keywords that resemble the current query.
The classifier can take a tuple of (embedding,query) -> prediction. The runtime complexity of this approach is of O(1) ensuring high-processing.

## 4. SME FEEDBACK LOOP
A fedback loop mechanism for tracking missclassifications and continous improvment of the framework.
The SMEs review flagged messages and adjust rules accordingly.

## 5. TICKET GENERATION
### Technical Implementation
Generate tickets using predefined templates with dynamic fields populated by customer-specific data. Assign priority and support team based on classification. 
Integrate with support systems like Jira via REST API to ensure tickets contain all necessary information for resolution.

- **Example**:
    ```json
    {
        "template": "order_tracking",
        "fields": {
            "customer_name": "Chris Solomou",
            "order_id": "123456",
            "issue_description": "Customer wants to track their order status."
        },
        "priority": 2,
        "team": "logistics"
    }
    ```

## Flowchart
    +--------------------------------------------------------+
    |              INPUT CHANNELS INGESTION                  |
    |  Ingest and aggregate data from available channhels    |
    |           (email, live chat, etc.)                     |
    +--------------------------------------------------------+
                            |
                            ↓
        +---------------------------------------------------+  -----------------------------------------
        |                DATA PROCESSING                    |                                           |
        | (Text cleaning, tokenization, normalization, etc.)|                                           |
        +---------------------------------------------------+                                           |
                            |                                                                           |
                            ↓                                                                           ↓
        +-----------------------------------------+                                 +-----------------------------------------+
        |           RULE-BASED CLASSIFIER         |  ←----------------------------→ |             WORD EMBEDDINGS             |
        |  SME-defined rules, regex patterns      |  ←----------------------------→ |  Entities that resemble the user-query  |
        +-----------------------------------------+                                 +-----------------------------------------+
                            |     ↑             
                            ↓     |-----------------------------------------        +-----------------------------------------+
    +------------------------------------------------------------+          |       |          SME FEEDBACK LOOP              |
    |                  TICKET GENERATION                         |           -----→ |    SME reviews flagged messages and     |
    | (Predefined templates + dynamic fields like order ID)      |                  |        adjust rules accordingly         |
    +------------------------------------------------------------+                  +-----------------------------------------+
                            |
                            ↓
        +-------------------------------------------+
        |           JIRA INTEGRATION LAYER          |
        |    Deliver tickets via Jira REST API      |
        |           to customer support             |
        +-------------------------------------------+


## QUESTIONS 
### What technologies would you use? 
- **Backend**: Python 
- **ML Framework**: scikit-learn spaCy
- **Integration**: Jira Python Library
- **Deployment**: Docker, Kubernetes

### What machine learning techniques? 
- **Data Preprocessing**: NLP techniques like tokenization, lemmatization, and stopword removal.
- **Word Embeddings**: Word2Vec or custom embeddings for capturing semantic similarities.
- **Text Classification**: Deterministic (lookup table) and stochastic (Logistic Regression)
- **Evaluation**: Precision, Recall, F1-Score for assessing model performance.

### What are the prospects for the system's evolution over time, how to handle it? 

### What questions would you ask the buisiness partner