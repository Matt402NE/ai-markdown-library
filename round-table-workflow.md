
# Workflow for AI Assistant Round Table

> This workflow is used to coordinate the feedback between multiple AI Assistants.  


## 1. Owner Initialization Prompt

I am working on  {{description}}. I need help creating a guide. I am going to provide you with my working draft.  I need you to review it and improve upon it.

```
document
```


## 2. Owner Feedback Prompt


You should make this a markdown document.  I am going to provide it to other AI Assistants to get their feedback. 



## 3. Reviewer Initialization Prompt

I am working on {{description}}. I had an AI Assistant create me the following guide. I need your help to make it better. 

Review it. I want to list each opportunities for improvement in its own section. You need to give reasons on why it should be changes. I am going to give each piece of feedback to another AI Assistant to decide if they want to include your change or not.

Here is a template for the feedback.  You should include each section in its own markdown code block to make it easy to copy it into my clipboard.

```
#### Improvement 1 — Clarify How Instruction Files Are Merged
- Issue:
- Why it matters:
- Recommended change:
- Accuracy check:
```

Let me know if any of the information is in it is not accurate. 

---



## 4. Owner Update Prompt

This is feedback from another AI Assistant.  I will provide you each piece of feedback in a separate post. You will need to reach each piece of feedback and decide whether or not to include it in the document.

---


## 5. Reviewer Review Prompt

This is an updated version of the guide.  All the feed from another AI Assistants was evaluated another AI Assistant determine whether each piece of feedback should be included.

Review it. Let me know if you see another other opportunities for improvement.

---




