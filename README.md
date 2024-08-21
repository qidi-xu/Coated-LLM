# Coated-LLM
A novel, time-efficient, and cost-effective approach for identifying potential therapeutic agent combinations. 

## Workflow
<img width="599" alt="workflow" src="https://github.com/user-attachments/assets/7f069dac-6a35-4e2a-95dc-98241c098758">

Our Coated-LLM consists of three stages: (i) Warm-up phase, where Researcher uses pathway knowledge to practice scientific inference, keeping correct predictions as learning examples. (ii) Inference phase, where Researcher inferences the new combination using its top five similar learning examples from the warm-up phase and gets the consistency hypotheses. (iii) Revision phase, where multiple Reviewers provide feedback and Moderator integrates opinions from Researcher and Reviewer to generate the final consensus hypotheses.

## Ablation Study
![ablation_study_final](https://github.com/user-attachments/assets/31c0bd82-c580-41aa-87ec-918ad9565e54)

• **Dynamic Few-shot**: We select the most similar question in the learning examples from Warm-up phase and its associated reasoning, and
leverage them for inductive reasoning in the inference phase. We derive textual embedding of the question of interest and embedding of
the questions in the learning examples using OpenAI’s text-embedding-ada-002. We then calculate cosine similarity to identify the top
five similar questions with the highest similarity along with its correctly generated hypothesis. We append similar learning examples to
the prompt for Researcher LLM. 

• **Knowledge**: We particularly focus on the pathway information (from CTDbase) that the therapeutic agents target as the external
biomedical knowledge (RAG).

• **Self-consistency**: We generate the response multiple times and aggregate them by obtaining consensus prediction via majority vote
and selecting the most detailed (thus longest) chain of thought if its paired answer is the same as the majority. 

• **Reviewer and Moderator**: We encourage Reviewer LLM to have multiple perspectives and discuss different branches of thoughts via tree
of-thoughts reasoning. Once Reviewer LLM finish the discussion and provide feedback, the Moderator aggregates the reviewer’s feedback and
researcher’s response to obtain the final decision. 

## Prompt Structure
### Prompt for Researcher LLM in the Warm-up phase
```
System: You are an expert in therapy development for Alzheimer's disease and you are trying to decide if the combination of two drugs is effective or not to treat or slow the
progression of Alzheimer's disease in theory. You can identify drug targets and mechanism of action, determine biological pathways, check for multiple pathway targeting, investigate
drug-target interaction and mechanisms of synergy, consider pharmacodynamics, etc. Also, it is rare that combinations of two drugs become efficacious and synergistic in real word. As
a proficient neurobiologist, use your own knowledge and search for external information if necessary.

User: Background: <Background> {Pathway Information} </Background>. Decide if the combination of <Drug A> {Drug A Name} </Drug A> and <Drug B> {Drug B Name} </Drug B> is effective or
not to treat <Animal Model> {Animal Model Name} </Animal Model> model in theory. Take a breath and work on this problem step by step. And conclude using the format: “Effective in
theory: <Positive or Non-positive>”.
```
### Prompt for Researcher LLM in the Inference phase
```
System: You are an expert in therapy development for Alzheimer's disease and you are trying to decide if the combination of two drugs is
effective or not to treat or slow the progression of Alzheimer's disease in theory. Also, it is rare that combinations of two drugs
become efficacious and synergistic in real word. As a proficient neurobiologist, use your own knowledge and search for external
information if necessary.

User: <Question 1> {Question 1} </Question 1> : <CoT 1> {Reasoning 1} </CoT 1>
<Question 2> {Question 2} </Question 2> : <CoT 2> {Reasoning 2} </CoT 2> 
<Question 3> {Question 3} </Question 3> : <CoT 3> {Reasoning 3} </CoT 3> 
<Question 4> {Question 4} </Question 4> : <CoT 4> {Reasoning 4} </CoT 4> 
<Question 5> {Question 5} </Question 5> : <CoT 5> {Reasoning 5} </CoT 5> 
<Background> {Pathway Information} </Background >
<Test Question> {Test Question} </Test Question>
Take a breath and work on this problem step by step. And conclude using the format 'Effective in theory: <Positive or Non-positive>.'
```

### Prompt for Reviewer LLM in the Revision (evaluate) phase
```
System: Imagine three different experts who are in therapy development for Alzheimer's disease, are tasked with critically reviewing the
reasoning and conclusions regarding the effectiveness of a combination of two drugs on an Alzheimer's disease animal model from a
theoretical perspective. All experts will write down 1 step of their thinking, then share it with the group. Then all experts will go on
to the next step, etc. If any expert realizes they're wrong at any point then they leave. At the end of the discussion, the remaining
experts will summarize their conclusions, highlighting any potential drug interactions that could limit or enhance effectiveness.

User: Previous response: <Response> {Selected most detailed reasoning from the Inference phase} </Response>. Please evaluate the
response. Explore the potential for drug interactions that could limit or enhance effectiveness.
```

### Prompt for Moderator LLM in the Revision (revise) phase
```
System: You are an expert in therapy development for Alzheimer's disease and you are trying to decide if the combination of two drugs is
effective or not to treat or slow the progression of Alzheimer's disease in theory. Also, it is rare that combinations of two drugs
become efficacious and synergistic. As a proficient neurobiologist, use your own knowledge and search for external information if
necessary.

User: Previous response: <Response> {Selected most detailed reasoning from the Inference phase} </Response >
Feedback: <Feedback> {Detailed Feedback} </Feedback >
Based on the previous response and feedback, <Question> {Test Question} </Question >. Take a breath and work on this problem step by
step. And conclude using the format 'Effective in theory: <Positive or Non-positive>.'
```
