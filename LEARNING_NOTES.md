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

---

## 3. Prompt Chaining - Multi-Step LLM Workflows

### What I Learned:

#### 3.1 **Prompt Chaining Pattern**
- **Prompt chaining** = Using the output of one LLM call as input for the next
- Creates more sophisticated AI workflows than single-shot prompting
- Each step builds upon the previous step's output
- Enables complex content generation tasks

#### 3.2 **Blog Generation Workflow**
- Created a two-step blog writing system:
  1. `create_outline`: Generates a structured outline from a title
  2. `create_blog`: Writes full blog content based on the outline
- Workflow: `START → create_outline → create_blog → END`
- State structure (`BlogState`):
  - `title`: User's input topic
  - `outline`: LLM-generated outline
  - `content`: Final blog post

#### 3.3 **Benefits of Prompt Chaining**
- **Better quality**: Breaking tasks into steps produces better results
- **Transparency**: Can inspect intermediate outputs (outline before content)
- **Control**: Can modify/validate outputs between steps
- **Reusability**: Individual nodes can be reused in different workflows

#### 3.4 **State Accumulation Pattern**
- State progressively builds up through the workflow:
  - Start: Only `title`
  - After step 1: `title` + `outline`
  - After step 2: `title` + `outline` + `content`
- Each node reads what it needs and adds new fields
- Final state contains complete processing history

#### 3.5 **Real-World Applications**
- Content generation (blogs, articles, reports)
- Research assistance (outline → detailed analysis)
- Code generation (spec → pseudocode → actual code)
- Creative writing (plot → chapters → full story)
- Any multi-step reasoning task

### Key Takeaways:
1. **BMI Workflow**: LangGraph workflows are composable - you can break down complex logic into smaller, focused node functions and connect them in a graph structure.

2. **LLM Workflow**: LangGraph bridges the gap between traditional programming and AI - you can combine deterministic workflows with powerful language models to build intelligent applications.

3. **Prompt Chaining**: Breaking complex AI tasks into sequential steps produces higher quality results and provides better control over the generation process. LangGraph's state management makes prompt chaining natural and elegant.

---

## 4. Batsman Statistics Workflow - Parallel Node Execution ✅

### What I Learned:

#### 4.1 **Cricket Statistics Calculator**
- State structure (`BatsmanState`):
  - Input fields: `runs`, `balls`, `fours`, `sixes`
  - Calculated fields: `sr` (strike rate), `bpb` (balls per boundary), `boundary_percent`
  - Output field: `summary` (formatted statistics summary)

#### 4.2 **Implemented Node Functions**
- **`calculate_sr`**: Calculates strike rate = (runs / balls) × 100
- **`calculate_bpb`**: Calculates balls per boundary = balls / (fours + sixes)
- **`calculate_boundary_percent`**: Calculates boundary % = (boundary runs / total runs) × 100
- **`summary`**: Generates formatted summary of all statistics

#### 4.3 **Parallel Node Execution Pattern** 🌟
- **Key Innovation**: Multiple nodes execute simultaneously from START
- Workflow structure:
  ```
  START → calculate_sr ────┐
  START → calculate_bpb ────┤→ summary → END
  START → calculate_boundary_percent ─┘
  ```
- All three calculation nodes run in parallel (no dependencies between them)
- Summary node waits for all three calculations to complete
- Demonstrates LangGraph's ability to handle concurrent operations

#### 4.4 **Return Dictionary Pattern**
- Node functions return dictionaries instead of modifying state directly
- Example: `return {'sr': sr}` instead of `state['sr'] = sr; return state`
- LangGraph automatically merges returned dictionaries into state
- Cleaner, more functional programming style

#### 4.5 **Real-World Insights**
- Parallel execution improves performance for independent calculations
- Sports analytics often involves multiple independent metrics
- Pattern applicable to any scenario with parallel data processing needs
- Examples: financial analysis, sensor data processing, multi-metric dashboards

### Key Takeaway:
This workflow demonstrates LangGraph's power for parallel processing - when calculations don't depend on each other, they can run simultaneously, with a final aggregation step. This is a significant advantage over sequential workflows.

---

## 5. UPSC Essay Evaluation - Advanced Patterns ✅

### What I Learned:

#### 5.1 **Structured Output with Pydantic**
- Using **Pydantic BaseModel** to define output schema
- `EvaluationSchema` with two fields:
  - `feedback`: Detailed string feedback
  - `score`: Integer between 0-10 (validated with `ge=0, le=10`)
- LLM returns structured data instead of raw text
- Ensures type safety and automatic validation

#### 5.2 **with_structured_output() Method**
- Key LangChain feature for type-safe LLM responses
- `model.with_structured_output(EvaluationSchema)`
- Ensures LLM output matches the defined schema
- Automatic validation and type checking
- Production-ready pattern for reliable AI systems

#### 5.3 **Annotated State with operator.add** 🌟
- **Advanced state management** pattern
- `individual_scores: Annotated[list[int], operator.add]`
- Automatically aggregates scores from parallel nodes
- LangGraph merges lists using the `add` operator
- Eliminates manual list concatenation logic

#### 5.4 **Multi-Criteria Parallel Evaluation**
- Three parallel evaluation nodes:
  1. `evaluate_language`: Language quality assessment
  2. `evaluate_analysis`: Depth of analysis evaluation
  3. `evaluate_clarity`: Clarity of thought assessment
- All run simultaneously (parallel execution)
- Each returns structured feedback + score
- Scores automatically aggregated into `individual_scores` list

#### 5.5 **Final Aggregation Node**
- `final_evaluation` node waits for all parallel evaluations
- Accesses all three feedbacks from state
- Calculates average score: `sum(scores) / len(scores)`
- Generates overall summarized feedback using LLM
- Demonstrates combining structured and unstructured outputs

#### 5.6 **Workflow Structure**
```
START → evaluate_language ──┐
START → evaluate_analysis ───┤→ final_evaluation → END
START → evaluate_clarity ────┘
```

### Real-World Application:
- Comprehensive essay evaluation system
- Useful for UPSC/competitive exam preparation
- Provides multi-dimensional feedback
- Objective scoring across multiple criteria
- Actionable insights for improvement

### Key Takeaway:
This workflow combines THREE advanced LangGraph patterns:
1. **Structured outputs** (Pydantic schemas)
2. **Parallel execution** (independent evaluations)
3. **Annotated state** (automatic aggregation with operator.add)

This is a production-grade pattern for building sophisticated AI evaluation systems.

---

## 6. Review Reply Workflow - Conditional Edges ✅

### What I Learned:

#### 6.1 **Conditional Edges** 🌟
- **Branching logic** in LangGraph workflows
- `add_conditional_edges()` method
- Router function returns next node name
- Different paths based on state values
- Essential for decision-making workflows

#### 6.2 **Router Function Pattern**
```python
def check_sentiment(state) -> Literal['positive_response', 'diagnose_issue']:
    if state['sentiment'] == 'positive':
        return 'positive_response'
    else:
        return 'diagnose_issue'
```
- Returns node name as string
- Type hints with `Literal` for clarity
- Reads state to make decision
- Directs workflow to appropriate path

#### 6.3 **Multiple Structured Outputs**
- Two different Pydantic schemas:
  - `SentimentSchema`: sentiment classification
  - `DiagnosisSchema`: issue_type, tone, urgency
- Different models for different tasks
- Demonstrates schema versatility

#### 6.4 **Workflow Structure**
```
START → find_sentiment → check_sentiment (router)
                              ↓
                    ┌─────────┴──────────┐
                    ↓                    ↓
            positive_response    diagnose_issue → negative_response
                    ↓                              ↓
                    └──────────→ END ←────────────┘
```

#### 6.5 **Use Case: Customer Support Automation**
- Analyzes customer review sentiment
- Routes to appropriate response path
- Positive reviews: Simple thank you
- Negative reviews: 
  - Diagnoses issue (UX/Performance/Bug/Support)
  - Assesses tone (angry/frustrated/disappointed)
  - Determines urgency (low/medium/high)
  - Generates contextual support response

### Real-World Application:
- Automated customer support systems
- Review management platforms
- Sentiment-based routing
- Personalized response generation
- Scalable support workflows

### Key Takeaway:
Conditional edges enable **decision-making workflows** that adapt based on data. This pattern is essential for building intelligent systems that respond differently to different inputs - the foundation of real AI applications.
