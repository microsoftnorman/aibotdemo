# GitHub Copilot Instructions for Mini-RAG-Quickstart

## Project Overview

This is a Retrieval-Augmented Generation (RAG) implementation that integrates **Azure OpenAI**, **CosmosDB**, and **Microsoft Teams** to enable conversational AI capabilities. The project demonstrates how to build a RAG model that augments LLM responses with custom data stored in CosmosDB, accessible through a Teams channel interface.

### Architecture
- **Teams**: User interface for chat interactions
- **Logic App**: Orchestrates message flow between Teams and Azure Functions
- **Azure Functions**: Core RAG implementation (Python 3.10/3.11)
- **CosmosDB**: Storage for domain-specific facts/data
- **Azure OpenAI**: LLM for generating responses

## Technology Stack

- **Language**: Python 3.10 or 3.11
- **Cloud Platform**: Microsoft Azure
- **Key Services**: 
  - Azure Functions (serverless compute)
  - Azure OpenAI (LLM integration)
  - Azure CosmosDB (NoSQL database)
  - Azure Logic Apps (workflow orchestration)
  - Microsoft Teams (chat interface)
- **Key Libraries**: 
  - `azure-functions`
  - `openai` (Azure OpenAI SDK)

## Coding Standards and Conventions

### Python Style
- Follow PEP 8 style guidelines for Python code
- Use meaningful variable names that describe their purpose
- Keep functions focused and single-purpose
- Use type hints where appropriate

### Azure Functions Best Practices
- Use environment variables for configuration (never hardcode secrets)
- Leverage Azure Functions bindings (e.g., CosmosDB input bindings)
- Use proper logging with `logging` module for debugging and monitoring
- Set appropriate authentication levels for HTTP endpoints
- Handle errors gracefully with proper HTTP status codes

### Security Practices
- **Never commit secrets or API keys** to the repository
- Use Azure Key Vault or environment variables for sensitive data
- Sanitize user inputs (e.g., remove HTML tags from user questions)
- Use proper authentication and authorization mechanisms

### Project-Specific Conventions
- Environment variables for configuration:
  - `AOAI_ENDPOINT`: Azure OpenAI endpoint
  - `AOAI_KEY`: Azure OpenAI API key
  - `MyAccount_COSMOSDB`: CosmosDB connection string
  - `MODEL`: OpenAI model deployment name (default: "gpt35")
  - `TEMPERATURE`, `MAX_TOKENS`, `TOP_P`, etc.: Model parameters
- HTML tags should be stripped from user inputs using regex
- CosmosDB facts are stored in the "aoaidb" database, "facts" container

## Code Organization

```
/
├── bin/                    # Setup and deployment scripts
│   ├── setup.sh           # Environment configuration
│   ├── createDB.sh        # CosmosDB setup
│   ├── insertCosmos.py    # Data ingestion script
│   ├── updateFNConfig.sh  # Azure Function configuration
│   └── deployFunc.sh      # Deployment script
├── data/                   # Sample data and prompts
│   ├── cosmosdb-facts.txt # Facts for RAG augmentation
│   └── datagen_prompt.txt # Prompt template for data generation
├── src/
│   └── azureFunction/     # Azure Function application
│       ├── function_app.py    # Main function handler
│       ├── host.json         # Function app configuration
│       └── requirements.txt  # Python dependencies
└── README.md              # Project documentation
```

## Development Workflow

### Before Making Changes
1. **Ask clarifying questions** if requirements are ambiguous
2. Understand the impact on Azure services and costs
3. Consider backward compatibility with existing deployments
4. Check if changes require environment variable updates

### Testing Locally
- Use Azure Functions Core Tools for local development
- Test with VS Code Azure Functions extension
- Verify CosmosDB connectivity before deployment
- Test OpenAI integration with sample queries

### Deployment Process
1. Deploy Azure Function from VS Code (Ctrl-Shift-P → "Azure Functions: Deploy to Function App")
2. Update function configuration with `bin/updateFNConfig.sh`
3. Test through Logic App and Teams integration
4. Monitor function logs in Azure Portal

## Documentation Expectations

### Code Documentation
- Add docstrings to functions explaining purpose, parameters, and return values
- Comment complex logic, especially prompt engineering and data processing
- Document any Azure-specific configurations or bindings
- Update README.md when adding new features or changing architecture

### Configuration Documentation
- Document all environment variables and their purpose
- Provide example values (non-sensitive) in comments
- Update setup scripts with clear instructions
- Document API version requirements for Azure services

## Architecture Patterns

### RAG Implementation Pattern
1. **Data Ingestion**: Facts stored in CosmosDB as structured data
2. **Query Processing**: User question received via HTTP trigger
3. **Context Retrieval**: All facts retrieved using CosmosDB input binding
4. **Prompt Engineering**: Facts prepended to user question as system context
5. **LLM Generation**: Azure OpenAI generates augmented response
6. **Response Delivery**: Result returned to Logic App and posted in Teams

### Key Design Decisions
- **Simplified RAG**: No vector embeddings or similarity search (all facts retrieved)
- **Stateless Functions**: Each request is independent
- **Serverless Architecture**: Auto-scaling with Azure Functions
- **Declarative Bindings**: Using Azure Functions bindings for CosmosDB access

## Common Tasks and Patterns

### Adding New Facts to CosmosDB
- Edit `data/cosmosdb-facts.txt`
- Run `bin/insertCosmos.py` to upload
- Facts format: One fact per line, stored with "fact" field in CosmosDB

### Modifying OpenAI Behavior
- Adjust parameters via environment variables (TEMPERATURE, MAX_TOKENS, etc.)
- Update system prompt in `function_app.py` for different response styles
- Change model deployment by updating MODEL environment variable

### Error Handling
- Return appropriate HTTP status codes (400 for bad requests, 200 for success)
- Log errors with `logging.error()` for debugging in Azure Portal
- Provide helpful error messages to users

## Testing Guidelines

Currently, there is no formal test infrastructure in this project. When adding tests:
- Use `pytest` for Python testing
- Mock Azure Functions context and bindings
- Mock Azure OpenAI API calls to avoid costs during testing
- Test input sanitization (HTML tag removal)
- Test error handling for missing parameters

## Performance Considerations

- Minimize CosmosDB queries (current design retrieves all facts - consider filtering for larger datasets)
- Set appropriate timeout values for Azure Functions
- Monitor token usage for OpenAI API calls
- Consider caching frequently accessed data
- Be mindful of cold start times for Azure Functions

## Clarifying Questions Policy

When working on new features or modifications:
- **Always ask clarifying questions** before making significant architectural changes
- Confirm Azure resource requirements and associated costs
- Verify intended user experience and integration points
- Understand data privacy and security requirements
- Ask about testing and validation expectations

## Additional Resources

- [Azure Functions Python Developer Guide](https://learn.microsoft.com/en-us/azure/azure-functions/functions-reference-python)
- [Azure OpenAI Service Documentation](https://learn.microsoft.com/en-us/azure/ai-services/openai/)
- [RAG Pattern Documentation](https://learn.microsoft.com/en-us/azure/search/retrieval-augmented-generation-overview)
- [CosmosDB Python SDK](https://learn.microsoft.com/en-us/azure/cosmos-db/nosql/sdk-python)
