# SyntronScope

**SyntronScope** is an open-source project dedicated to providing AI-powered blockchain intelligence, specializing in tracking early token movements on Solana. Our mission is to give users a clear edge with faster, deeper, and smarter insights into the blockchain world through an intuitive chat interface.

## Key Points

### Products
- **SyntronAnalytics**: Track early token movements, analyze liquidity changes, monitor new deployments, predict token trends, and visualize whale movements.  
- **SyntronSearch**: Discover trending tokens based on smart money activity, liquidity inflows, and early deployment patterns.  
- **SyntronNotifier**: Set up custom alerts for early liquidity additions, whale buys, token listings, and market anomalies.  
- **SyntronBlockchain**: Effortlessly interact with Solana blockchain operations using simple text commands.  
- **SyntronChat**: The unified chat interface that integrates all functionalities under one seamless experience, with each capability treated as a plugin.

### Open-Source
- **Libraries**: Build your own Solana-focused chatbot using [syntron-core](/libs/core/), or leverage community-implemented tools available in [syntron-community](/libs/community/).  
- **End to End Application**: Except for the core chat functionality, the whole application is open-sourced. You can run your own instance of SyntronScope and modify it according to your needs.  
- **Contribute**: Help diversify SyntronScope's tools and capabilities through community contributions.

### Platform
- **Empower Everyone**: Whether you're a user seeking early insights or a developer looking to build and earn from custom tools, SyntronScope makes it possible through its simple and powerful platform.

## How Does It Help?

- **Build Your Custom AI Chat**: Implement your own tracking and intelligence tools, and let [syntron-core](/libs/core/) manage everything else.
- **Use Implemented Blockchain Tools**: Access powerful Solana tracking tools or extend them with custom features using [syntron-community](/libs/community/).
- **Chat to Unlock SyntronScope's Potential**: Interact with all of SyntronScope's capabilities effortlessly—just by chatting.

## Technical Overview

The SyntronScope core is responsible for navigating user requests to relevant plugins, utilizing specific functionalities from each valid plugin. It facilitates easy plugin creation and supports connection with external systems using Redis. The core also generates a score for each chat interaction with users.

Key features:
- [Modular plugin architecture for easy extension](#plugin-structure-and-development)
- [Dynamic routing of requests to appropriate plugins](#put-everything-together)
- [Redis-based communication for external integrations](#application)
- [Scoring system for chat quality assessment](#scoring)

The core functionality of SyntronScope is built with a modular architecture, allowing for easy integration and extension through plugins.

### What are Plugins?

Plugins in SyntronScope are essentially LLM (Large Language Model) Agents that perform specific tasks using a set of Tools. Each plugin is designed to handle a particular domain or functionality.

- **Agents**: LLM-powered decision-makers that determine which Tools to use and in what order, based on the user's input and the task at hand.
- **Tools**: Functions that perform specific actions or retrieve information. They are wrappers around Utilities, adding functionality to make them compatible with LangChain's framework.
- **Utilities**: The underlying implementation of business logic and functionality that Tools utilize.

### Plugin Structure and Development

At [`libs/core/plugins`](libs/core/plugins), we've created an underlying structure of plugins, tools, and utilities to simplify plugin development. This structure allows users to extend base classes and implement only the business logic as utilities, while correctly setting parameters such as plugin name, utility description, examples, and so on.

The hierarchy is as follows:

1. **Plugins**: Extend [`BasePlugin`](libs/core/plugins/base.py)
2. **Tools**: Extend [`BaseTool`](libs/core/plugins/tools/base.py)
3. **Utilities**: Extend [`BaseUtility`](libs/core/plugins/utilities/base.py)

Developers can refer to the existing plugins in the [`libs/community/plugins/`](libs/community/plugins/) directory for examples of how to implement new plugins.

There are several `BaseUtility` extensions available, which can be used depending on the utility's specific requirements:

- [`BlockchainUtility`](libs/core/plugins/utilities/blockchain.py): For blockchain-related operations
- [`RemoteUtility`](libs/core/plugins/utilities/remote.py): For making remote API calls
- [`LLMUtility`](libs/core/plugins/utilities/llm.py): For language model interactions

By leveraging this structure, developers can focus on implementing the core functionality of their plugins without worrying about the underlying architecture.  
[Plugins docs](/libs/core/syntronscope_core/plugins/README.md).

### Put Everything Together

- **Graph**: Manages the conversation flow and agent interactions. Defined in [`libs/core/graph/`](libs/core/graph/), it uses LLM models to process user messages and delegate tasks to appropriate plugins. The Graph component is responsible for:
  - Dynamically building a network of LLM Agents, most of which are Plugins under the hood
  - Routing messages between agents
  - Managing states between agents
  - Creating chains with prompts and models
  - Parsing outputs
  - Automatically composing independently implemented plugins

The Graph utilizes `LangGraph` (from LangChain) for graph structure and flow management, and `LangChain` for chains, prompts, formatting, and other language model interactions. This architecture allows for a flexible and scalable system that can easily incorporate new plugins and functionalities.
![Graph](assets/graph.png)

### Application

- The application uses Redis extensively for message handling and communication:
  - Receiving messages from clients
  - Sending responses back to clients
  - Publishing updates and notifications
  - Inter-service communication

- You can see controller logic in `apps/syntron_brain/main.py` and service layer in `apps/syntron_brain/services`

- [Nest.js application](apps/syntron_backend/) serves as the primary backend for the entire system. It facilitates communication between the front-end and Syntron Brain using Redis. Additionally, it implements various auxiliary functions, including: blockchain transaction generation, signing, sending, waiting for confirmation and so on.

### Scoring

SyntronScope implements a scoring system to incentivize user engagement and provide feedback on the quality of interactions. Here's how it works:

- After each user question or command is processed and answered, the system assesses the dialogue.
- The assessment focuses primarily on the latest question and answer pair.
- A score is generated based on this assessment, which reflects the quality and effectiveness of the interaction.
- This score is then sent to the API repository (separate from this core repository).
- The API stores the score and notifies the user of their earned points.

The exact scoring criteria and implementation details can be found in the relevant scoring module within the codebase. [Scoring Module](libs/internals/scoring)

## Getting Started

**Use syntron-core as a library**
```bash
pip install syntronai-core
