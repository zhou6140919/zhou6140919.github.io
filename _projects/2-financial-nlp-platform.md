---
name: Financial NLP Platform
tools: [Python]
image: https://s2.wp.com/wp-content/themes/a8c/jetpackme-new/images/2021/social-growth.jpg
description: Langboat zero-shot information extraction platform can extract entities with specific meanings from texts. For example, in the scenario of sorting out punishment announcements, it is necessary to extract the entity information such as law enforcement agencies, punishment objects, and punishment amounts, and quickly structure a large number of texts to improve efficiency.
---

# Financial Zero-shot Information Extraction Platform

{% include elements/button.html link="https://langboat.com/" text="Demo" block=true %}

## Introduction

This platform can extract entities with specific meaning in text. 
For example, in the scene of sorting out punishment announcements, 
it is necessary to extract entity information such as law enforcement agencies, punishment objects, and punishment amounts, 
and quickly structure a large amount of text to improve efficiency.

This platform supports users to establish entity types and customize personalized information extraction models according to their own needs.
Different from the traditional AI model creation method that needs to manually annotate hundreds or thousands of sample data, 
on the zero-shot platform, it only takes three simple steps to realize the customization of the information extraction model.

![progress](https://cdn.langboat.com/image/zero-shot/progress.png)

## Concepts

### Schemas

The schema is the entity information that needs to be output in the information extraction task.

For example, if you need to extract "January 1st" and "Company A" from "Company A acquired Company B on January 1st",
then you can create two new schemas: "Time" and "Purchaser".

### Prompts

Model prompts are additional words that help the model locate key information when inputting text to be extracted;
generally speaking, they are "questions" posed to the model.

For example, if you need to extract "time" from "Company A acquired Company B on January 1st",
you can enter the model prompt "What is the time mentioned above?"

For different schemas, different model prompts also have different extraction effects.
You can choose the most appropriate model prompt for each schema to ensure the accuracy of the model.
