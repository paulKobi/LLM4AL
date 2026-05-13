# Automata Learning with LLMs
#project
#automatalearning
#llm

# Queries
## Direct Model
```
You will be assigned the role of a Software Testing Assistant and will receive a the protocol specification text as PDF. Your primary task is to prepare the Finite State Machine (FSM) to be used as starting point for adaptive learning as a Python implementation. It is imperative that your responses be strictly based on the text provided. If the text does not contain information relevant to the query, respond with: ’No’.

Output the FSM in .dot format.
```


## Via Internal Representation
```
You will be provided with the PDF of the protocol
specification text. Please provide
responses to the questions in the specified order and format
as outlined in the text provided. If the file does not contain
information relevant to the query, respond with: ’No’.

Please respond to the questions based on the provided text
with the required format. Queries are
as follows:
1. Extract all needed datatypes in the specification and their corresponding value ranges. Ensure the return format is a Dictionary.
2. Extract all commands, and for each command, extract the datatype and value range for each of its arguments. Ensure the return format is a Dictionary.
3. Extract all attributes, and for each attribute, extract its datatype and value range. Ensure the return format is a Dictionary.
4. Return a Python list that includes all command IDs.
5. Return a Python list that includes all attribute IDs.
```

```
You will be assigned the role of a Software Testing Assistant and will receive a the protocol specification text as PDF. Your primary task is to prepare the Finite State Machine (FSM) to be used as starting point for adaptive learning. It is imperative that your responses be strictly based on the text provided. If the text does not contain information relevant to the query, respond with: ’No’. 

Chain-of-Thought:
1. Extract the value range of each command’s argument to transfer from the initial state to the destination state. Please refer to the content marked as "Datatype Knowledge" regarding datatypes and their value ranges.
2. Extract the invalid value of each command’s argument and error messages, if specified in the cluster text.
3. Extract unaccepted values of each state.

[Output from 1]

Output the FSM in .dot format.
```

## From learned Python code
```
Write a minimal Python implementation for the attached specification, given as Markdown file.
Ensure that all possible transitions are covered by the Python code by including and testing test cases.
```
```
You will be assigned the role of a Software Testing Assistant and will receive a the protocol specification text as Markdown file. Your primary task is to use the generated Python implementation to prepare the Finite State Machine (FSM) to be used as starting point for adaptive learning. It is imperative that your responses be strictly based on the specification and code provided. If the text does not contain information relevant to the query, respond with: ’No’.

Chain-of-Thought:
1. Construct a transition function convering all functions in the code and the specification.
2. Generate a FSM from the previously generated transtion function, specification and code under a sufficiently coarse abstraction only on the actions, each state should be individually contained.
3. Use the Markdown Specification to generate traces that violate the FSM.
4. Repeat 3. until no more violating traces can be generated, i.e., the FSM correctly captures the specification.
5. Remove all states that are unreachable from the start state from the FSM.
6. Verify that the state model is still correct.

Output the FSM in .dot format.
```