# LangGraph Learning Notes

## BMI Workflow - Extended with Conditional Logic

### What I Learned:

#### 1. **Adding Multiple Nodes to a Workflow**
- Created a second node function `label_bmi()` that categorizes BMI values
- Each node function follows the same pattern:
  - Takes the state as input (BMIState)
  - Performs specific logic/transformations
  - Returns the updated state
  - Can modify one or more fields in the state

#### 2. **Sequential Node Execution**
- Nodes are connected using `graph.add_edge()`
- The workflow now flows: `START -> calculate_bmi -> label_bmi -> END`
- Each node processes the state and passes it to the next node
- This demonstrates how to chain multiple processing steps

#### 3. **State Management Across Nodes**
- The `BMIState` TypedDict now includes:
  - `weight_kg`: Input data
  - `height_m`: Input data
  - `bmi`: Calculated by first node
  - `bmi_category`: Calculated by second node
- State is shared and modified across the entire workflow
- Each node can read values set by previous nodes

#### 4. **Conditional Logic in Node Functions**
- Implemented WHO BMI categorization standards:
  - BMI < 18.5: Underweight
  - BMI 18.5-24.9: Normal weight
  - BMI 25-29.9: Overweight
  - BMI ≥ 30: Obesity
- Node functions can contain any Python logic (conditionals, loops, etc.)

#### 5. **Workflow Compilation and Execution**
- The workflow is compiled once: `workflow = graph.compile()`
- Can be invoked multiple times with different initial states
- Each invocation runs through all connected nodes in order
- Final state contains all computed values

### Key Takeaway:
LangGraph workflows are composable - you can break down complex logic into smaller, focused node functions and connect them in a graph structure. This makes the code more maintainable and easier to understand.
