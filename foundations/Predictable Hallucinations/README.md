# Predictable Hallucinations

If you are using an LLM to work with a fixed corpus or body of text and getting it to answer things only from that body of text and it ends up hallucinating, it's _always_ because you are not taking into account how an LLM works and making it work in a way that will make it hallucinate.

> The analogy in this explanation is an oversimplification but can help you predict where you are going to run into this problem.

## Predictable Problem

Look at the first part of interaction in the chat with Gemini Flash Lite linked below where the model is given a list of transactions and asked to do a sum of the amounts. The answer it gets is wrong at first. And then asking the model to list the numbers first and then sum it and it is still wrong. But then you ask it to sum only the first two values and after it has done that you ask it to do the same for the whole list and it gets the sum right.

https://gemini.google.com/share/4acac9218cf7

This problem pattern is extremely predictable. You probably won't see this with bigger models than Gemini Flash Lite now but this used to be a problem even with Claude Sonnet until last year.

## A Single Concatenated Attention Head

The way an LLM works is that it has multiple attention heads that read the text. Different heads are focusing on different aspects. The key thing to notice is that all the heads are concatenated or combined into a single one. This part that attention heads get combined into a single head means that an LLM thinks in a sequential way. Like humans can process multiple things at once but only focus on one at a time, so do LLMs only focus on one thing at a time and this attention flows based on the prompt you give to it. This is a moving stream (consider how the LLM output is streamed out word by word to put this in perspective). This stream format exists even before it starts outputting values to the user. The way you are reading this text is the way an LLM writes text.

## Key Part to Note

An LLM cannot read things in context, finish reading it and then come back to use parts of the context. The flow of an LLM is to be a forward pass.

## Explanations of the Three Attempts in the Gemini Chat

The problem with the first attempt in the chat is that you ask it to do two things simultaneously with what it's processing. With each line you ask it to first extract the number and then sum it to the previous number. But if you imagine an LLM with an attention head moving through the text, when it gets to the first number it extracts it and then it keeps going but reading the second transaction line it has to both extract the new number and add it to the previous number and keep going to the third line. What you've done here is given it two points to keep its attention head on. The most common problem you will see is that it will slip on managing to focus on keeping either the numbers correct or staying on the correct transaction line. It cannot save the numbers anywhere while it extracts them and then sum them up later.

The second case where it failed after listing the numbers is something where you are asking it to go through all the numbers first and then try to sum them up but it can't both maintain all the numbers in focus and add them in the same attempt without an intermediate state to hold the calculation.

The third case in the chat where it actually gets the sum right is where you manage the attention head to follow this stream where it takes the last number it reads and add it to the next number and as it does this its attention head focuses on the output sum which is again the previous number and it just needs to go ahead and read the next number and add it again and keep following the same attention head flow. At no point is the combined attention head required to focus on two routes. Its processing follows a single flow.

---

## Questions & Issues

If you have any questions or run into issues implementing this, [raise an issue](https://github.com/talhaashraf94/42/issues) in this repo.

---

Warm Biscuits,
Talha Ashraf
