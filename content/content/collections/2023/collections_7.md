---
Title: Front War 2 - Programmation C#/Javascript
Subtitle: ""
Date: 2023-12-05
Lastmod : 
Tags: ["Prog"]
image : ""
Description: "Article sur mon projet actuel."
Draft: 
---

stateDiagram-v2
   [*] --> New
   New --> Ready: admitted
   Ready --> Running: scheduler dispatch
   Running --> Ready: interrupt
   Running --> Waiting: I/O or event wait
   Waiting --> Ready: I/O or event completion
   Running --> Terminated: exit
   Terminated --> [*]

