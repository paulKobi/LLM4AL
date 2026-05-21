# Automata Learning with LLMs
#project
#automatalearning
#llm

# Queries
## Direct Model
```
You will be assigned the role of a Software Testing Assistant and will receive a the protocol specification text as PDF. Your primary task is to prepare the Finite State Machine (FSM) to be used as starting point for adaptive learning as a Python implementation. It is imperative that your responses be strictly based on the text provided. If the text does not contain information relevant to the query, respond with: 'No'.

Output the FSM in .dot format.
```


## Via Internal Representation
```
You will be provided with the PDF of the protocol
specification text. Please provide
responses to the questions in the specified order and format
as outlined in the text provided. If the file does not contain
information relevant to the query, respond with: \E2\80\99No\E2\80\99.

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
You will be assigned the role of a Software Testing Assistant and will receive a the protocol specification text as PDF. Your primary task is to prepare the Finite State Machine (FSM) to be used as starting point for adaptive learning. It is imperative that your responses be strictly based on the text provided. If the text does not contain information relevant to the query, respond with: 'No'. 

Chain-of-Thought:
1. Extract the value range of each command\E2\80\99s argument to transfer from the initial state to the destination state. Please refer to the content marked as "Datatype Knowledge" regarding datatypes and their value ranges.
2. Extract the invalid value of each command\E2\80\99s argument and error messages, if specified in the cluster text.
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
You will be assigned the role of a Software Testing Assistant and will receive a the protocol specification text as Markdown file. Your primary task is to use the generated Python implementation to prepare the Finite State Machine (FSM) to be used as starting point for adaptive learning. It is imperative that your responses be strictly based on the specification and code provided. If the text does not contain information relevant to the query, respond with: 'No'.

Chain-of-Thought:
1. Construct a transition function covering all functions in the code and the specification.
2. Generate a FSM from the previously generated transtion function, specification and code under a sufficiently coarse abstraction only on the actions, each state should be individually contained.
3. Use the Markdown Specification to generate traces that violate the FSM.
4. Repeat 2. and 3. until no more violating traces can be generated, i.e., the FSM correctly captures the specification.
5. Remove all states that are unreachable from the start state from the FSM.
6. Verify that the state model is still correct.

Output the FSM in .dot format.
```


## Learn AALpy model
Python implementation:
```
You will be assigned the role of a Software Testing Assistant and receive the 'gridworld_spec_3x3.md' file as protocol specification as Markdown file. Your primary task is to use the specification to write a minimal Python implementation. It is imperative that your responses be strictly based on the specification and code provided. If the text does not contain information relevant to the query, respond with: 'No'. Ensure that all possible transitions are covered by the Python code by including test cases.

Chain-of-Thought:
1. Write a minimal Python implementation for the protocol.
2. Use the Markdown Specification to generate traces that violate the implementation.
3. Repeat 2. and refine the implementation until no more violating traces can be generated, i.e., the FSM correctly captures the specification.
4. Ensure that the implementation is minimal.
5. Verify that the implementation is still correct.
```

AAlpy Model:
```
You will be assigned the role of a Software Testing Assistant. Now, your primary task is to use the generated Python implementation to prepare the Finite State Machine (FSM) as AALpy object to be used as starting point for adaptive learning. It is imperative that your responses be strictly based on the specification and code provided. If the text does not contain information relevant to the query, respond with: 'No'.

Chain-of-Thought:
1. Construct a transition function convering all functions in the code and the specification.
2. Generate an AALpy object from the previously generated transtion function, specification and code under a sufficiently coarse abstraction only on the actions, each state should be individually contained.
3. Use the Python implementation to generate traces that violate the AALpy object.
4. Repeat 2. and 3. until no more violating traces can be generated, i.e., the AALpy object correctly captures the specification.
5. Remove all states that are unreachable from the start state from the AALpy object.
6. Verify that the AALpy object is still correct.
```

## Java Code
```
You will be assigned the role of a Software Testing Assistant and receive the 'gridworld_spec_3x3.md' file as protocol specification as Markdown file. Your primary task is to use the specification to write a minimal Java implementation. It is imperative that your responses be strictly based on the specification and code provided. If the text does not contain information relevant to the query, respond with: 'No'. Ensure that all possible transitions are covered by the Java code by including test cases.
Write all generated files to '/out'.

Chain-of-Thought:
1. Write a minimal Java implementation for the protocol.
2. Use the Markdown Specification to generate traces that violate the implementation.
3. Repeat 2. and refine the implementation until no more violating traces can be generated, i.e., the FSM correctly captures the specification.
4. Ensure that the implementation is minimal.
5. Verify that the implementation is still correct.
```


## C Code
```
You will be assigned the role of a Software Testing Assistant and receive the 'gridworld_spec_3x3.md' file as protocol specification as Markdown file. Your primary task is to use the specification to write a minimal C implementation. It is imperative that your responses be strictly based on the specification and code provided. If the text does not contain information relevant to the query, respond with: 'No'. Ensure that all possible transitions are covered by the C code by including test cases.
Write all generated files to '/out'.

Chain-of-Thought:
1. Write a minimal C implementation for the protocol.
2. Use the Markdown Specification to generate traces that violate the implementation.
3. Repeat 2. and refine the implementation until no more violating traces can be generated, i.e., the FSM correctly captures the specification.
4. Ensure that the implementation is minimal.
5. Verify that the implementation is still correct.
```
