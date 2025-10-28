# TableSyncAI Integration with NLWeb

This directory contains the integration plan, configuration files, and examples for integrating Microsoft's NLWeb natural language interface technology into TableSyncAI.

## 📚 Documentation

### Main Integration Plan
- **[tablesyncai-integration-plan.md](tablesyncai-integration-plan.md)** - Comprehensive integration plan covering:
  - Phase 1: Natural Language Text Interface (12 weeks)
  - Phase 2: Voice Input Integration (5 months)
  - Architecture diagrams and technical details
  - Implementation roadmaps and success metrics
  - Cost estimation and risk analysis

### Configuration Files
- **[config_tablesyncai_example.yaml](../config/config_tablesyncai_example.yaml)** - Complete configuration example showing:
  - LLM provider settings (Azure OpenAI, Anthropic)
  - Vector database configuration (Azure AI Search, Qdrant)
  - Authentication and authorization setup
  - Voice interface configuration (Phase 2)
  - Security and compliance settings

- **[tablesyncai_tools.xml](../config/tablesyncai_tools.xml)** - Custom tools definition including:
  - sync_status_search - Query sync job statuses
  - sync_configuration_query - Retrieve configuration details
  - error_analysis - Analyze and explain errors
  - performance_metrics - Get performance statistics
  - data_lineage - Trace data dependencies
  - sync_job_management - Start/stop/retry jobs
  - query_optimization - Get performance suggestions
  - alert_query - Query active alerts

## 🚀 Quick Start

### Prerequisites
- Python 3.8+
- Azure subscription (for Azure OpenAI and Azure AI Search)
- TableSyncAI instance with API access
- NLWeb repository cloned

### Setup Steps

1. **Install NLWeb Dependencies**
   ```bash
   cd code/python
   pip install -r requirements.txt
   ```

2. **Configure Environment Variables**
   ```bash
   # Copy the example configuration
   cp config/config_tablesyncai_example.yaml config/config_tablesyncai.yaml
   
   # Set environment variables
   export AZURE_OPENAI_KEY="your-azure-openai-key"
   export AZURE_SEARCH_SERVICE="your-search-service"
   export AZURE_SEARCH_API_KEY="your-search-api-key"
   export JWT_SECRET_KEY="your-jwt-secret"
   ```

3. **Prepare TableSyncAI Data**
   ```python
   # Extract sync job data from TableSyncAI
   from tablesyncai_integration import extract_sync_data
   
   sync_jobs = extract_sync_data(
       connection_string="your-tablesyncai-db",
       include_configs=True,
       include_errors=True
   )
   
   # Transform to Schema.org format
   from tablesyncai_integration import transform_to_schema
   
   schema_objects = transform_to_schema(sync_jobs)
   
   # Load into vector database
   from data_loading import load_to_vector_store
   
   load_to_vector_store(
       schema_objects,
       backend="azure_ai_search",
       collection="tablesyncai-index"
   )
   ```

4. **Start NLWeb Server**
   ```bash
   # Start with TableSyncAI configuration
   python -m webserver.aiohttp_server --config config/config_tablesyncai.yaml
   ```

5. **Test the Integration**
   ```bash
   # Test a natural language query
   curl -X POST http://localhost:8000/api/nlweb/ask \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer YOUR_TOKEN" \
     -d '{
       "query": "Show me failed syncs from yesterday",
       "site": "tablesyncai",
       "mode": "list"
     }'
   ```

## 💡 Example Queries

Once integrated, users can ask questions like:

### Status Queries
- "Show me all failed syncs from yesterday"
- "What's the status of the customer database sync?"
- "Which jobs are currently running?"
- "Has the orders table synced today?"

### Configuration Queries
- "How is the orders table mapped to Snowflake?"
- "What's my sync schedule for the products database?"
- "Show me connection settings for the production database"
- "What transformation rules are applied to customer data?"

### Error Analysis
- "Why did sync job #12345 fail?"
- "Show me all constraint violations from last week"
- "Explain the error code ERR_TIMEOUT_001"
- "What caused the customer sync to fail?"

### Performance Queries
- "What's the average sync time for the customer table?"
- "Show me throughput trends for the last month"
- "Which syncs take the longest?"
- "How has performance changed over time?"

### Data Lineage
- "Where does the customer email field come from?"
- "What tables depend on the products table?"
- "Show me the complete pipeline for orders data"
- "What's the impact if I change the users table?"

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                  TableSyncAI Application                    │
│                                                             │
│  ┌────────────┐  ┌─────────────┐  ┌──────────────────┐   │
│  │ TableSync  │  │   NLWeb     │  │  Vector Store    │   │
│  │   Engine   │─▶│ Integration │◀─│  (User Data)     │   │
│  └────────────┘  └─────────────┘  └──────────────────┘   │
│                         │                                   │
└─────────────────────────┼───────────────────────────────────┘
                          ▼
                  ┌───────────────┐
                  │ User Interface│
                  │ - Web Chat    │
                  │ - Mobile App  │
                  │ - Voice (P2)  │
                  └───────────────┘
```

## 📊 Data Schema

TableSyncAI data is represented using Schema.org extensions:

```json
{
  "@context": "https://schema.org",
  "@type": "SyncJob",
  "name": "Customer Database Sync",
  "identifier": "sync-job-12345",
  "status": "completed",
  "startTime": "2025-10-28T10:00:00Z",
  "endTime": "2025-10-28T10:15:00Z",
  "recordsProcessed": 10000,
  "recordsFailed": 3,
  "sourceDatabase": {
    "@type": "Database",
    "name": "CustomerDB_Production",
    "url": "postgres://prod.example.com/customers"
  },
  "targetDatabase": {
    "@type": "Database",
    "name": "CustomerDB_Analytics",
    "url": "snowflake://analytics.example.com/customers"
  },
  "errors": [...]
}
```

## 🎯 Implementation Phases

### Phase 1: Natural Language Text Interface (12 weeks)

**Weeks 1-2: Foundation**
- Set up NLWeb environment
- Configure vector database
- Define data schema

**Weeks 3-4: Data Pipeline**
- Extract TableSyncAI data
- Transform to Schema.org
- Load into vector store

**Weeks 5-6: Custom Tools**
- Implement 8 custom tools
- Test tool selection

**Weeks 7-8: API Integration**
- Create REST endpoints
- Add authentication
- Implement security

**Weeks 9-10: UI Integration**
- Embed chat widget
- Customize styling
- User testing

**Weeks 11-12: Testing & Optimization**
- Performance tuning
- Quality evaluation
- Documentation

### Phase 2: Voice Input Integration (5 months)

**Month 1: Voice Foundation**
- Azure Speech Services setup
- Basic push-to-talk implementation

**Month 2: Integration**
- Voice-optimized prompts
- Audio feedback
- End-to-end testing

**Month 3: Platform Support**
- Web browser voice
- iOS/Android apps

**Month 4: Advanced Features**
- Continuous conversation
- Multi-language support

**Month 5: Testing & Optimization**
- User acceptance testing
- Performance optimization

## 💰 Cost Estimation

### Phase 1: Text-Only (Monthly)
- Vector Database (Azure AI Search S1): $250
- LLM Calls (Azure OpenAI GPT-4o): $500
- Embeddings (text-embedding-3-large): $6.50
- Hosting (Azure App Service P1v2): $73
- **Total: ~$830/month**

### Phase 2: Text + Voice (Monthly)
- Phase 1 costs: $830
- Speech-to-Text (Azure Speech): $10,000
- Text-to-Speech (Azure Neural TTS): $16
- Additional compute: $100
- **Total: ~$10,946/month**

## 🔒 Security Considerations

- **Authentication**: JWT tokens from TableSyncAI
- **Authorization**: Row-level security by organization
- **Data Privacy**: PII redaction and encryption
- **Compliance**: GDPR compliant, audit logging
- **Rate Limiting**: 60 requests/minute per user

## 📈 Success Metrics

### Technical Metrics
- Query response time: < 2 seconds (90th percentile)
- Query accuracy: > 90%
- System uptime: > 99.9%

### User Metrics
- User adoption: 60% try within 30 days
- Weekly usage: 40% use weekly
- Query success: 80% reach resolution

### Business Metrics
- Support ticket reduction: 30%
- User satisfaction: NPS > 50
- Feature utilization: Increased usage

## 🤝 Contributing

To contribute to the TableSyncAI integration:

1. Review the integration plan
2. Follow the implementation roadmap
3. Test with realistic TableSyncAI data
4. Document any customizations
5. Share feedback and improvements

## 📞 Support

For questions about the integration:
- Review the [Integration Plan](tablesyncai-integration-plan.md)
- Check [NLWeb Documentation](../README.md)
- Refer to [Azure OpenAI Docs](https://learn.microsoft.com/azure/ai-services/openai/)

## 📝 License

This integration follows the NLWeb MIT License. See [LICENSE](../LICENSE) for details.

## 🔄 Version History

- **v1.0** (2025-10-28): Initial integration plan
  - Phase 1 design complete
  - Phase 2 voice integration planned
  - Configuration examples provided
  - Custom tools defined
