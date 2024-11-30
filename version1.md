# System Design: SME-Guided Customer Support Classification

## 1. Input Channels Aggregator
### Technical Implementation
- **Integration Protocols**:
  - Email: IMAP/SMTP with OAuth authentication
  - Live Chat: WebSocket/REST API integration
  - Fallback: Web form submission endpoint


## 2. Domain Expertise Mapping Engine
### Knowledge Capture Strategy
- **Structured Interview Process**:
  1. Workshop with SME to map support request categories
  2. Create detailed taxonomy of issue types
  3. Document decision trees and classification logic

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
            +---------------------------+
            | Request Intake Module     |
            | (email, live chat, etc.)  |
            +---------------------------+
                            |
                            v
    +---------------------------------------------------+
    |                Preprocessing Module               |
    | (Text cleaning, tokenization, normalization, etc.)|
    +---------------------------------------------------+
                            |
                            v
        +-----------------------------------------+
        |           Rule-Based Classifier         |
        |  (SME-defined rules, regex patterns)    |
        +-----------------------------------------+
                            |
                            v
    +------------------------------------------------------------+
    |                          Ticket Generator                  |
    | (Predefined templates + dynamic fields like order ID)      |
    +------------------------------------------------------------+
                            |
                            v
        +-------------------------------------------+
        |           Jira Integration Layer          |
        | (Push tickets via Jira REST API)          |
        +-------------------------------------------+
