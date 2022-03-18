---
title: EventWiki A Knowledge Base of Major Events
categories: nlp
tags: 
    - nlp
    - knowledge base
author: Zhou Tong
sidebar:
  nav: Paper-notes
  avatar: true
article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(34, 139, 87 , .4), rgba(139, 34, 139, .4))'
    src: /pictures/bg1.jpg
---

EventWiki: A Knowledge Base of Major Events

<!--more-->

See [original paper](https://aclanthology.org/L18-1079) for more details.

## Abstract

This paper introduces the first knowledge base resource of major events in mankind history, which is a useful resource for information extraction regrading events in NLP.

## Introduction

EventWiki is a knowledge base of events, which collects 21,275 major events of 95 types from Wikipedia.

Problem: Existing knowledge bases have no explicit indicator that indicates whether a page refers to an event or an static entity, or event-event relation links, which makes it difficult to distinguish these events from overwhelming static entities and directly use these resources for event-related knowledge discovery.

## Data

For an event entry, there are 4 kinds of information in EventWiki: event type, event infobox, event summary and full text description of the event. In addition, for some events, there are relations between them.

 - Every event entry in EventWiki belongs to one of these 95 event types.
 - For each event type, we extract the schema of this event type by finding out the most frequent non-empty slots from the infobox of events belonging to this type.

 ### Application

 1. Event classification for event types
 2. The event schema of the event type can be directly used as the schema for event extraction.
 3. Event-event relation extraction
 4. Event summary generation
 5. Event-centric knowledge base construction

