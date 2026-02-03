# LangGraph Learning Notes

## 1. BMI Workflow - Extended with Conditional Logic

### What I Learned:

#### 1.1 **Adding Multiple Nodes to a Workflow**
- Created a second node function `label_bmi()` that categorizes BMI values
- Each node function follows the same pattern:
  - Takes the state as input (BMIState)
  - Performs specific logic/transformations
  - Returns the updated state
  - Can modify one or more fields in the state

#### 1.2 **Sequential Node Execution**
- Nodes are connected using `graph.add_edge()`
- The workflow now flows: `START -> calculate_bmi -> label_bmi -> END`
- Each node processes the state and passes it to the next node
- This demonstrates how to chain multiple processing steps

#### 1.3 **State Management Across Nodes**
- The `BMIState` TypedDict now includes:
  - `weight_kg`: Input data
  - `height_m`: Input data
  - `bmi`: Calculated by first node
  - `bmi_category`: Calculated by second node
- State is shared and modified across the entire workflow
- Each node can read values set by previous nodes

#### 1.4 **Conditional Logic in Node Functions**
- Implemented WHO BMI categorization standards:
  - BMI < 18.5: Underweight
  - BMI 18.5-24.9: Normal weight
  - BMI 25-29.9: Overweight
  - BMI ≥ 30: Obesity
- Node functions can contain any Python logic (conditionals, loops, etc.)

#### 1.5 **Workflow Compilation and Execution**
- The workflow is compiled once: `workflow = graph.compile()`
- Can be invoked multiple times with different initial states
- Each invocation runs through all connected nodes in order
- Final state contains all computed values

---

## 2. LLM Workflow - Integrating Language Models with LangGraph

### What I Learned:

#### 2.1 **Integrating LangChain Components**
- LangGraph works seamlessly with LangChain components
- Used `ChatOpenAI` from `langchain_openai` package
- Environment variables loaded via `dotenv` for API keys
- LLMs are initialized once and reused across workflow invocations

#### 2.2 **LLM Node Functions**
- Node functions can invoke LLMs and process their responses
- The `llm_qa()` function demonstrates:
  - Extracting input from state (`question`)
  - Formatting prompts for the LLM
  - Invoking the model with `.invoke(prompt).content`
  - Storing the response in state (`answer`)

#### 2.3 **Simple Question-Answering Workflow**
- Created a minimal workflow: `START -> llm_qa -> END`
- State structure (`LLMState`):
  - `question`: User's input question
  - `answer`: LLM's generated response
- This pattern can be extended for more complex AI applications

#### 2.4 **Environment Configuration**
- Used `.env` file to store sensitive API keys
- `load_dotenv()` loads environment variables
- Keeps credentials separate from code
- Essential for production deployments

#### 2.5 **Real-World AI Integration**
- LangGraph provides structure for AI applications
- Combines deterministic logic (graph structure) with AI (LLM responses)
- Enables building complex AI agents with multiple processing steps
- State management ensures data flows correctly through AI components

### Key Takeaways:
1. **BMI Workflow**: LangGraph workflows are composable - you can break down complex logic into smaller, focused node functions and connect them in a graph structure.

2. **LLM Workflow**: LangGraph bridges the gap between traditional programming and AI - you can combine deterministic workflows with powerful language models to build intelligent applications.
