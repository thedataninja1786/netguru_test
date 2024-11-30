# System Design: SME-Guided Customer Support Classification

## 1. INPUT CHANNELS INGESTION 
### Use APIs or connectors to gather incoming requests
-   Integration Protocols:
    - Email: IMAP/SMTP with OAuth authentication
    - Live Chat: WebSocket/REST API integration
-   Normalize data into a common format, such as JSON, with fields like:
    - Channel: Email/Live chat
    - Message content: The text body of the message
    - Customer details: Name, email, order ID


## 2. DATA PROCESSING 
### Knowledge Capture Strategy
Utilize SME past data to map customer queries to request categories.
Clean and normalize the data by applying lowercase conversion, stopword removal, and lemmatization to unify variations of the same word.
Finally the clean data should be tokenized for mapping and rule-matching with the corresponding customer support team.
Create detailed taxonomy of issue types with the following sample mapping structure

    ```json
    SUPPORT_CATEGORIES = {
        "order_tracking": {
        "keywords": ["track", "shipping", "delivery", "order status"],
        "priority": 2,
        "team": "logistics"
        },
        "payment_errors": {
        "keywords": ["payment", "charge", "declined", "refund"],
        "priority": 3,
        "team": "finance"
        },
        # Additional categories...
    }
    ```
## 3. RULE-BASED CLASSIFIER
### SME-defined rules to map requests to the corresponding support team
Implement a rule-based classifier that assigns the incoming (processed) query to a category.
Enhance classification robustness by using word embeddings to identify keywords semantically similar to the query.
The classifier can take a tuple of (embedding,query) -> prediction. This approach ensures O(1) runtime complexity, enabling high processing efficiency. 

## 4. SME FEEDBACK LOOP
A fedback loop mechanism for tracking missclassifications and continous improvment of the framework.
The SMEs review flagged messages and adjust rules accordingly.

## 5-6. JIRA TICKET GENERATION AND INTEGRATION
### Technical Implementation
Generate tickets using predefined templates with dynamic fields populated by customer-specific data. Assign priority and support team based on classification. 
Integrate with support systems like Jira via REST API to ensure tickets contain all necessary information for resolution.

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
- **ML Framework**: scikit-learn, spaCy
- **Integration**: Jira REST API - Python libraries
- **Deployment**: Microservice Architecute: Docker (for containerization of each component), Kubernetes (for scalable orchestration)

### What machine learning techniques? 
- **Data Processing**: NLP techniques like tokenization, lemmatization, and stopword removal
- **Word Embeddings**: Word2Vec or custom embeddings for capturing semantic similarities
- **Classification**: Deterministic (lookup table) and stochastic (Logistic Regression, DNN etc.)
- **Evaluation**: Precision, Recall, F1-Score for assessing model performance

### What are the prospects for the system's evolution over time, how to handle it? 
- Transition from rule-based methods to ML-based models as historical data accumulates
- Expand support for multilingual queries and integrate additional communication channels
- Introduce automated active learning pipelines to continuously refine classification rules with SME feedback
- Add message queues to ensure robust data delivery during ingestion since the system handles multiple channels concurrently
- Incorporate a load balancer to evenly distribute incoming traffic across the microservices preventing bottlenecks during peak usage

### What questions would you ask the buisiness partner
- A list of supported channels, along with their expected (and current) resolution time 
- Past data for how SMEs handle cases, and how they dealt with more ambigous requests
- What does the current infrastructure look like and what is the allocated budget and timeline for the project
- Are there are any preffered technologies that the project should be based on