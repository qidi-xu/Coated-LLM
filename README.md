# Coated-LLM
A novel, time-efficient, and cost-effective approach for identifying potential therapeutic agent combinations. 

## Workflow
<img width="599" alt="workflow" src="https://github.com/user-attachments/assets/7f069dac-6a35-4e2a-95dc-98241c098758">

Our Coated-LLM consists of three stages: (i) Warm-up phase, where Researcher uses pathway knowledge to practice scientific inference, keeping correct predictions as learning examples. (ii) Inference phase, where Researcher inferences the new combination using its top five similar learning examples from the warm-up phase and gets the consistency hypotheses. (iii) Revision phase, where multiple Reviewers provide feedback and Moderator integrates opinions from Researcher and Reviewer to generate the final consensus hypotheses.

## Prompt Structure
### Prompt for Researcher LLM in the Warm-up phase. 
```
System: You are an expert in therapy development for Alzheimer's disease and you are trying to decide if the combination of two drugs is effective or not to treat or slow the
progression of Alzheimer's disease in theory. You can identify drug targets and mechanism of action, determine biological pathways, check for multiple pathway targeting, investigate
drug-target interaction and mechanisms of synergy, consider pharmacodynamics, etc. Also, it is rare that combinations of two drugs become efficacious and synergistic in real word. As
a proficient neurobiologist, use your own knowledge and search for external information if necessary.

User: Background: <Background> {Pathway Information} </Background>. Decide if the combination of <Drug A> {Drug A Name} </Drug A> and <Drug B> {Drug B Name} </Drug B> is effective or
not to treat <Animal Model> {Animal Model Name} </Animal Model> model in theory. Take a breath and work on this problem step by step. And conclude using the format: “Effective in
theory: <Positive or Non-positive>”.
```

