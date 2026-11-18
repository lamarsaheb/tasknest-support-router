# TaskNest Support Ticket Router specification

Build the AI Agents course homework as a Google Colab notebook. Given a support ticket, classify it once, route it to one team, and return a fixed customer acknowledgment. Deliver this specification, `tasknest_support_router.ipynb`, and a concise `README.md` with setup and usage instructions.

## State and graph

Use a `TypedDict` with exactly four keys.

| Key | Type |
| --- | --- |
| `ticket` | `str` |
| `category` | `Literal["billing", "technical", "general"]` |
| `team` | `str` |
| `reply` | `str` |

Build and compile a `StateGraph`. Add an edge from `START` to `classify`. Use exactly one `add_conditional_edges` call to map `billing` to `billing_team`, `technical` to `tech_team`, and `general` to `general_team`. The body of `route(state)` is only `return state["category"]`. Each team node sets a fixed team name and short customer reply in plain Python, then goes directly to `END`. The graph has no tools, loops, or memory.

## Classification

Use `ChatOpenAI` with `temperature=0`. Define `MODEL_NAME = "gpt-4.1-nano"` once in the notebook. This low-cost OpenAI text model suits short classification requests with three allowed labels. Use `with_structured_output` with a Pydantic `BaseModel` whose only field is `category: Literal["billing", "technical", "general"]`. The `classify` node makes the sole model call per ticket and returns only `{"category": prediction.category}`.

- Billing covers payments, invoices, refunds, subscriptions, and charges.
- Technical covers crashes, bugs, errors, login problems, and malfunctioning app features.
- General covers feature requests, general questions, and thank-you notes.
- If the app crashes while the customer is attempting payment, prioritize technical. For other mixed tickets, use the main issue and customer intent.

## Colab workflow

Make the first cell `!pip -q install langgraph langchain-openai`. Import `os` and `userdata` from `google.colab`, then load the key only with `os.environ["OPENAI_API_KEY"] = userdata.get("OPENAI_API_KEY")`. Keep actual keys, hardcoded credentials, and interactive password prompts out of the project. Render the compiled graph with `draw_mermaid_png()` and `IPython.display.Image`. Run `result = graph.invoke({"ticket": "I was charged twice this month"})` and print all four returned fields.

## Tests and reflection

Include at least nine labeled tickets, with at least two billing cases, two technical cases, and two general cases. Include the ambiguous ticket “The app crashes every time I try to pay.” with an expected category of technical. Cover tricky cases such as a customer whose main goal is canceling a subscription despite login trouble and a request for a new payment feature. Print the expected category and the observed ticket, category, team, and reply for every case. Print PASS on an expected-category match and REVIEW on a mismatch.

Include a clearly marked draft reflection of three to five sentences that discusses the ambiguous case, expected versus actual routing, how to decide whether the model or the expectation was right, and LLM versus deterministic rule tradeoffs. Leave the actual result pending until a real model run supplies it.

Validate notebook JSON and Python syntax. Report live test outcomes only if actually observed. Commit all three files and push to `main` when available.

## Implementation decisions and change history

The initial implementation uses strict JSON Schema structured output and disables SDK retries to keep each ticket to a single model attempt. The team names are Billing, Technical Support, and General Support. After the cloud runtime could not reach the external Mermaid renderer, the diagram cell was updated to report rendering errors so the example and tests can still run.
