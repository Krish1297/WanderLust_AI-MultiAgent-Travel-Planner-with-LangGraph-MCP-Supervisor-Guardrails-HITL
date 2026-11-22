# ✈️ WanderLust AI — Agentic Multi-Agent Travel Planner with LangGraph, MCP, Supervisor, Guardrails & HITL

WanderLust AI is an AI-powered travel planning application that transforms natural-language travel requests into structured and personalized travel plans.

Built using LangGraph, LangChain, Model Context Protocol (MCP), and FastAPI, the application coordinates multiple specialized AI agents through a Supervisor Agent to research flights, hotels, weather, and travel budgets, and generate practical itineraries.

The application also incorporates input guardrails and Human-in-the-Loop (HITL) approval workflows, allowing users to review generated itineraries and provide feedback before receiving the final travel plan.

## Why this project?

Planning a trip often requires switching between multiple websites, tools, and spreadsheets to research flights, accommodation, weather conditions, and expenses.

WanderLust AI brings these activities into one application through an agentic workflow where specialized agents collaborate to simplify travel planning.

Instead of relying on a single LLM response, the application uses a Supervisor Agent to coordinate specialized agents, retrieve relevant information through MCP tools, and generate an integrated travel plan.

## Key Features

- **Multi-Agent Orchestration:** Coordinates specialized travel agents using LangGraph.
- **Supervisor Agent:** Analyzes travel requests, extracts trip constraints, and dynamically selects the relevant agents.
- **Input Guardrails:** Validates incoming requests to ensure they are relevant to travel planning.
- **Flight Research:** Retrieves airport and airline information through AviationStack MCP.
- **Hotel Research:** Researches accommodation options using Tavily Remote MCP.
- **Weather Information:** Retrieves current weather and short-range forecast information using a custom OpenWeather MCP server.
- **Budget Analysis:** Evaluates trip affordability, estimated expenses, budget risks, and cost-saving opportunities.
- **Itinerary Generation:** Combines available specialist results into a structured travel itinerary.
- **Human-in-the-Loop (HITL):** Pauses workflow execution for human review, approval, or revision feedback.
- **Workflow Persistence:** Uses PostgreSQL checkpointing to preserve workflow state and resume interrupted executions.
- **Final Response Generation:** Produces a polished travel plan incorporating human feedback.
- **Web Interface:** Provides an interactive frontend powered by FastAPI, Jinja2, HTML, CSS, and JavaScript.

## System Architecture

The application follows a Supervisor-based multi-agent architecture.

1. **User Request:** The user submits a travel request through the web interface.
2. **Input Guardrails:** The application validates whether the request belongs to the travel-planning domain.
3. **Supervisor Agent:** Analyzes the request, extracts travel constraints, and determines which specialist agents are required.
4. **Specialized Agents:** The selected agents execute sequentially to gather relevant information and perform their respective tasks.
5. **Itinerary Agent:** Integrates the available results into a draft travel itinerary.
6. **Human-in-the-Loop:** The workflow pauses and presents the draft for human review.
7. **Final Agent:** Generates the final travel response using the approval decision or revision feedback.

### Agent Responsibilities

| Agent | Responsibility |
|---|---|
| Supervisor Agent | Agent selection, routing, and trip constraint extraction |
| Flight Agent | Airport and airline information and flight guidance |
| Hotel Agent | Accommodation research |
| Weather Agent | Current weather and forecast retrieval |
| Budget Agent | Budget feasibility and estimated expenses |
| Itinerary Agent | Integrated itinerary generation |
| Human Approval | Review, approval, or revision feedback |
| Final Agent | Final travel response generation |

### MCP Integration

WanderLust AI uses MCP to connect specialized agents with external tools and services.

| MCP Server | Transport | Purpose |
|---|---|---|
| AviationStack MCP | Local stdio | Airport and airline information |
| Tavily MCP | Remote Streamable HTTP | Hotel and web research |
| Custom Weather MCP | Local stdio | Current weather and forecast information |

The MCP client manages server connections and loads the required tools when they are requested by the application.

## How the Workflow Works

1. The user submits a natural-language travel request.
2. Input guardrails validate the request.
3. The Supervisor Agent analyzes the request and selects the required specialist agents.
4. Selected agents execute sequentially:
   - Flight Agent retrieves airport and airline information.
   - Hotel Agent researches accommodation options.
   - Weather Agent retrieves current weather and forecast information.
   - Budget Agent analyzes estimated travel expenses and budget feasibility.
5. The Itinerary Agent generates an integrated draft itinerary.
6. LangGraph pauses execution using its interrupt mechanism and presents the draft for human review.
7. The user approves the itinerary or provides feedback requesting revisions.
8. LangGraph resumes execution using the approval decision and human feedback.
9. The Final Agent generates the final travel response.

## Tech Stack

**Backend & Orchestration**
- Python 3.10+
- FastAPI
- LangGraph
- LangChain
- Model Context Protocol (MCP)

**AI & LLM**
- Groq
- LLM-powered Supervisor and specialized agents

**MCP & External Integrations**
- AviationStack API
- Tavily API
- OpenWeather API
- Custom MCP server using FastMCP

**Database & Persistence**
- PostgreSQL
- LangGraph PostgreSQL Checkpointer
- Psycopg

**Frontend**
- Jinja2
- HTML
- CSS
- JavaScript

## Project Structure

```text
.
├── app.py                         # FastAPI application and API endpoints
├── backend.py                     # LangGraph multi-agent workflow
├── mcp_client.py                  # MCP client and external tool integrations
├── custom_weather_mcp_server.py   # Custom local Weather MCP server
├── requirements.txt               # Python dependencies
├── .env                           # Environment variables (not committed)
├── static/                        # CSS, JavaScript and frontend assets
├── templates/                     # Jinja2 HTML templates
└── tools/                         # Additional tool integrations, if applicable
```

## Prerequisites

Before running the application locally, make sure you have:

- Python 3.10 or newer
- PostgreSQL database
- Git
- `uv` / `uvx` for launching the AviationStack MCP server
- API keys for:
  - Groq
  - Tavily
  - AviationStack
  - OpenWeather

## Environment Variables

Create a `.env` file in the project root:

```env
DATABASE_URL=postgresql://user:password@localhost:5432/travel_db

GROQ_API_KEY=your_groq_api_key

AVIATION_STACK_API_KEY=your_aviationstack_api_key

TAVILY_API_KEY=your_tavily_api_key

OPENWEATHER_API_KEY=your_openweather_api_key
```

**Note:** The application automatically adds `sslmode=require` to the PostgreSQL connection URL when it is not already specified.

Never commit your `.env` file or expose API keys in your repository.

## Installation

### 1. Clone the repository

```bash
git clone <your-repository-url>
cd <your-project-folder>
```

### 2. Create a virtual environment

**Windows (PowerShell):**

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

**Linux / macOS:**

```bash
python -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure environment variables

Create the `.env` file and add your API keys and PostgreSQL connection URL.

### 5. Verify uvx installation

```bash
uvx --version
```

The AviationStack MCP integration requires `uvx` to be available in the active environment.

## Running the Application

Start the FastAPI application:

```bash
python app.py
```

Alternatively, run it using Uvicorn:

```bash
uvicorn app:app --reload --host 127.0.0.1 --port 8000
```

Open the application in your browser:

```text
http://127.0.0.1:8000/
```

The MCP client launches the configured local MCP servers when their tools are requested. Tavily connects through its remote Streamable HTTP endpoint.

## API Endpoints

### 1. Health Check

```http
GET /health
```

Checks whether the application is running and returns the available feature list.

### 2. Submit a Travel Request

```http
POST /api/travel
```

Example request:

```json
{
  "message": "Plan a 3-day trip to Tokyo with a budget of $1200"
}
```

An optional `thread_id` can be supplied to identify an existing LangGraph workflow thread.

Example:

```json
{
  "message": "Plan a 3-day trip to Tokyo with a budget of $1200",
  "thread_id": "optional-thread-id"
}
```

Example using cURL:

```bash
curl -X POST http://127.0.0.1:8000/api/travel \
  -H "Content-Type: application/json" \
  -d '{"message":"Plan a 3-day trip to Tokyo with a budget of $1200"}'
```

The response includes the generated itinerary, thread ID, selected agents, travel constraints, and whether human approval is required.

### 3. Approve or Request Revisions

```http
POST /api/travel/approve
```

After the draft itinerary is generated, the workflow pauses for human approval.

**Approve the itinerary:**

```json
{
  "thread_id": "user-generated-thread-id",
  "approved": true,
  "feedback": ""
}
```

**Request revisions:**

```json
{
  "thread_id": "user-generated-thread-id",
  "approved": false,
  "feedback": "Reduce accommodation expenses and include more budget-friendly sightseeing options."
}
```

The application resumes the interrupted LangGraph workflow using the supplied human decision and generates the final response.

## Human-in-the-Loop Workflow

Human approval is implemented using LangGraph's `interrupt()` and `Command(resume=...)` mechanisms.

The workflow follows these steps:

1. The Itinerary Agent generates a draft.
2. LangGraph interrupts execution and saves the workflow state through PostgreSQL checkpointing.
3. The application presents the draft itinerary to the user.
4. The user approves the draft or provides revision feedback.
5. The FastAPI approval endpoint resumes the workflow using the original `thread_id`.
6. The Final Agent incorporates the human decision and generates the final response.

This approach provides a reviewable workflow instead of automatically finalizing every generated itinerary.

## Database Persistence

PostgreSQL is integrated through LangGraph's `PostgresSaver` checkpointer.

It enables:
- Persistent workflow state.
- Thread-based execution.
- Recovery of interrupted workflows.
- Resumption after Human-in-the-Loop approval.

The database checkpoint is initialized when the backend starts.

## Current Scope and Limitations

- Flight guidance is generated using airport and airline reference information; verified live flight availability and ticket prices are not currently implemented.
- Hotel information is obtained through web research and does not guarantee room availability or booking prices.
- Weather forecast data is retrieved, but its integration into itinerary generation is still a work in progress.
- Budget calculations are estimates and may differ from actual travel expenses.
- The application generates travel plans and does not directly book flights or accommodation.

## Future Improvements

- Integrate live flight search and airfare APIs.
- Incorporate forecast data into weather-aware itinerary generation.
- Add more advanced budget optimization.
- Improve itinerary personalization and destination recommendations.
- Introduce additional travel-related MCP tools.
- Expand human approval and revision workflows.

## Acknowledgements

Built using LangGraph, LangChain, Model Context Protocol, Groq, FastAPI, and PostgreSQL, with external integrations from AviationStack, Tavily, and OpenWeather.