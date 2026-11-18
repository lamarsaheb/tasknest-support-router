# TaskNest Support Ticket Router

AI Agents homework: route TaskNest support tickets with one structured OpenAI classification call in LangGraph.

## Run in Google Colab

1. Open `tasknest_support_router.ipynb` in Google Colab.
2. Add `OPENAI_API_KEY` in Colab Secrets and enable notebook access.
3. Run all cells. The notebook installs dependencies, builds and draws the graph, and runs nine labeled test cases.
4. Review actual outputs and update the draft reflection before submitting.

The graph routes tickets to Billing, Technical Support, or General Support. API keys are not stored in the repository.
