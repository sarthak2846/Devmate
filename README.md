# Devmate

A full-stack, multi-agent software development system designed to turn a natural-language product idea into a working software implementation. The project combines a LangGraph-based orchestration engine, Gemini-powered reasoning agents, a web dashboard, and a sandboxed execution environment to simulate a small AI engineering team that plans, builds, reviews, and validates software.

This repository is not just a chatbot wrapper. It is an orchestration platform where multiple specialized AI agents collaborate on different stages of a software lifecycle. A user provides a requirement, the system clarifies ambiguities, creates a technical blueprint, builds a task plan, creates a sandbox environment, generates code, verifies the result, and can continue iterating until the product is structurally sound and ready for review.

---

## Project Objective

The primary goal of this project is to demonstrate how an AI agentic workflow can automate the early and middle parts of software engineering. The system is structured around a software lifecycle model:

1. Requirements analysis and clarification
2. Solution architecture and blueprint creation
3. Planning and task breakdown
4. Code generation in a safe sandbox
5. Review, validation, and debugging
6. Execution and delivery feedback

Instead of relying on a single model call to generate a complete app, the system decomposes the problem into specialized roles. Each agent is responsible for a single step, making the pipeline easier to reason about, easier to debug, and more aligned with real-world engineering practices.

---

## Why This Project Matters

Traditional software generation tools often generate a single response or output without paying attention to validation, error recovery, or structured system design. This project addresses that weakness by treating software creation as a workflow with state, checkpoints, and iterative review.

The value of the system is that it introduces:

- Specialized AI roles instead of one generic assistant
- A graph-based workflow that orchestrates execution order
- Human-in-the-loop clarification when requirements are ambiguous
- State persistence across agent transitions
- Validation and debugging loops for code quality
- A visual dashboard for monitoring the pipeline
- An isolated execution sandbox for safe code generation

This makes the project a practical learning and prototyping environment for agentic software engineering.

---

## Core Architecture

The project is composed of several layers:

### 1. AI Orchestration Layer
The workflow is built using LangGraph. Each node in the graph represents a step such as PM clarification, architecture design, planner setup, coding, review, execution, or debugging. The graph decides which node runs next depending on the current state.

This graph is defined in `src/config/graph.js` and uses a shared state schema stored in `src/config/state.js`.

### 2. Agent Layer
The system includes multiple agents, each with a specific responsibility:

- PM Agent: interprets the user request and creates a project specification
- Architect Agent: generates the technical blueprint and product structure
- Blueprint Validator: checks if the architecture is consistent and valid
- Planner Agent: turns the blueprint into a sequence of build tasks
- Coder Agent: produces implementation code
- Reviewer Agent: checks quality and correctness
- Executor Agent: runs or validates the implemented code
- Debugger Agent: handles failures and proposes fixes
- Human Escalation: allows human guidance when needed

These agents are all orchestrated through the graph runner.

### 3. Backend API Layer
The backend is implemented using Express.js and exposes REST endpoints for creating a project, retrieving data, and managing project state. It also runs a WebSocket server so the dashboard can receive live progress updates from the AI pipeline.

The server is defined in `server/index.js` and routes are located in `server/routes/projects.js`.

### 4. Dashboard Layer
The frontend is a React + Vite application with a mission-control style interface. It enables users to:

- Enter a software requirement
- Launch a new AI project
- Watch progress in real time
- See logs, outputs, token usage, and pipeline state
- Answer clarifying questions when the AI requests input
- Resume or retry a failed workflow

The dashboard is located in the `dashboard/` directory.

### 5. Sandbox and Execution Layer
The project supports isolated execution using a sandbox manager. This protects the generated work from directly changing the host environment and provides a controlled space where tasks can be executed and validated.

---

## Workflow Overview

The system operates in phases that approximate an AI product team.

### Phase 1: Requirements and Clarification
The user provides an idea in plain English. The PM agent examines the request and decides whether it is clear enough. If the requirement is ambiguous, it asks clarifying questions before moving forward.

### Phase 2: Architecture Blueprint
Once the requirement is understood, the architect agent creates a blueprint that includes:

- Entities and data models
- API endpoints
- Frontend pages
- Database recommendations
- Folder structure
- High-level system design

The validator checks whether the blueprint is internally coherent and if it should be revised.

### Phase 3: Planning and Sandbox Setup
The planner creates a set of development tasks and organizes them into phases. Then the system configures a sandbox environment to prepare for code generation and execution.

### Phase 4: Development Loop
This is the heart of the system. The graph repeatedly performs the following cycle:

- Select the next task
- Build the relevant context
- Ask the coder agent to write or modify code
- Update the task registry
- Verify the code via a reviewer
- Execute or validate results
- Take a snapshot
- Debug failures if needed
- Continue until the project is complete

This loop is designed to behave like a real software team rather than a one-shot code generator.

---

## Project Structure

```text
AIDevFinal/
├── dashboard/
│   ├── index.html
│   ├── package.json
│   ├── vite.config.js
│   └── src/
│       ├── App.jsx
│       ├── components/
│       ├── hooks/
│       ├── lib/
│       ├── store/
│       └── index.css
├── server/
│   ├── index.js
│   ├── routes/
│   ├── services/
│   └── ws/
├── src/
│   ├── agents/
│   ├── config/
│   ├── nodes/
│   ├── utils/
│   └── index.js
├── tests/
│   ├── test-architect.js
│   ├── test-devloop.js
│   ├── test-graph-skeleton.js
│   ├── test-planner.js
│   ├── test-pm-agent.js
│   ├── test-sandbox.js
│   └── test-validator.js
├── package.json
├── README.md
└── .env.example
```

---

## Tech Stack

This project uses a modern JavaScript stack:

- Node.js for backend orchestration
- Express.js for REST API and server setup
- WebSockets for live dashboard streaming
- React + Vite for the UI dashboard
- LangGraph for stateful multi-agent workflow orchestration
- Google Gemini via `@google/genai` for AI model inference
- Redis support for checkpoint persistence
- Docker-style sandbox patterns for isolated execution
- dotenv for environment-based configuration

This combination allows the project to feel like a real engineering control plane for AI-driven software generation.

---

## Installation and Setup

### Prerequisites

Before running the project, install the following:

- Node.js 18 or newer
- npm
- A Google Gemini API key
- Optional: Redis if you want proper checkpoint persistence
- Optional: Docker if sandbox features are enabled in your environment

### 1. Install Root Dependencies

```bash
cd AIDevFinal
npm install
```

### 2. Install Dashboard Dependencies

```bash
cd dashboard
npm install
cd ..
```

### 3. Create Environment File
Create a `.env` file in the project root with your keys, for example:

```bash
GEMINI_API_KEY=your_key_here
GEMINI_MODEL=gemini-2.5-flash
REDIS_URL=redis://localhost:6379
SERVER_PORT=3000
FRONTEND_URL=http://localhost:5173
TOKEN_BUDGET=2.0
```

---

## How to Run the Project

### Start the full project

```bash
npm run dev
```

This starts:

- the Express server
- the WebSocket layer
- the dashboard frontend

### Run the CLI workflow directly

```bash
node src/index.js "Build a todo app with authentication and categories"
```

This invokes the AI orchestration graph directly and outputs the project specification, architecture plan, and task flow in the terminal.

### Run specific tests

```bash
npm run test:graph
npm run test:pm
npm run test:architect
npm run test:validator
npm run test:planner
npm run test:sandbox
npm run test:devloop
```

---

## Key Functional Ideas

### Agentic Workflow
This project uses an agentic model, where each AI step acts as a specialized worker. That is the key difference between a simple prompt-based app and an orchestration system:

- one agent does not handle everything
- the workflow remains stateful
- decisions are conditional and traceable
- the system can recover from failures

### Human-in-the-Loop Interaction
When a requirement is unclear, the system asks the user for clarification instead of making risky assumptions. This improves accuracy and keeps the flow realistic.

### Validation and Recovery
The system includes multiple stages of review and verification. If a generated implementation fails validation, it can move into debugging or simplification loops rather than stopping abruptly.

### Token Budget Awareness
The project tracks token usage so the system can reason about cost and limit agent interactions when needed. This is useful when using model APIs with constrained quotas.

---

## Why It Is a Strong Learning Project

This repository is valuable for learning because it combines several important modern engineering ideas:

- AI workflow design
- Stateful orchestration with graph-based execution
- Multi-agent collaboration patterns
- Human interaction in automation pipelines
- Debugging loops and validation cycles
- Real-time monitoring via dashboard and WebSocket streams
- Cost-aware LLM orchestration

It is a practical example of how generative AI can be used beyond chat responses and toward software engineering workflows.

---

## Future Directions

The project is already structured as a strong foundation for additional features such as:

- stronger code generation templates for full-stack apps
- persistent project storage and database-backed history
- deployment integration with cloud providers
- sandbox-based code execution with more security isolation
- richer project analytics and token reporting
- support for more agent roles and specialized tools
- automatic Git-based source control management

This shows the repository is designed to evolve from an experimental prototype into a more complete AI software factory.

---

## Summary

AI Dev Final is an experimental but powerful multi-agent software development system. It demonstrates how multiple AI agents can collaborate to transform a user idea into a structured, validated product plan and eventually into executable code. By combining LangGraph, Gemini, React, Express, and sandbox orchestration, the project serves as both a functional prototype and an educational model of agentic software engineering.

The goal is not only to generate code, but to orchestrate the entire process intelligently: clarify, plan, build, validate, debug, and iterate.

---

## Quick Start

```bash
cd AIDevFinal
npm install
cd dashboard && npm install && cd ..
npm run dev
```

Then open the dashboard in the browser and begin by describing the app you want to build.

