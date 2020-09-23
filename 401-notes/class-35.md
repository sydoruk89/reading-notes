# DSA Review
## The Best Whiteboard Interview Advice: 
1. ***Communicate!***
Do you understand what they’re asking you to do? Prove it. Restate the question for them and seek affirmation. You might actually be surprised to find you don’t fully understand what they’re asking for — perhaps the question is similar, but not the same, as a practice problem you have completed in the past. Using the tried-and-true fizz-buzz example, you could restate the problem as follows:
“So I’d like to restate the problem to you to make sure I understand what you’re looking for. The sole parameter for my function will be an integer. The sole output of my function will be an incrementing array, starting from the number 1 and ending at the input number.
If a number is a multiple of 3, the output will instead be `fizz`. If a number is a multiple of 5, the output will instead be `buzz`. However, if the output is a multiple of both 3 and 5, the output will instead be `fizzbuzz`. Is my understanding correct?”

2. ***Ask About Edge Cases***
Let’s use the fizz-buzz example. After successfully restating the problem, a valid way to ask about edge cases would be as follows:
“Now that I confirmed my understanding of the problem, I’d like to ask about some potential edge cases. Is it possible that the input would be a type other than a number? If so, what should the function do? Can the input be 0 or negative? Again, if so, what should the function do?”
3. ***Ask About Test Cases***
This is free and you should take advantage of it. Simply ask if there are any test cases that the function should pass. Your interviewer might be expecting you to ask this question, so it might be necessary. But it’s also possible the interviewer was not expecting the question and will think “ah, this candidate knows about testing!”

4. ***Write Pseudocode and Ask If It Makes Sense***
Here’s the kicker: you can ask if your pseudocode makes sense to the interviewer. It’s possible they will be the type that doesn’t want to “give you hints,” but it’s also possible they’ll be more interested in how you think and want to discuss your pseudocode with you. When I interview candidates, I’m more interested in the latter — rarely do we ever actually develop software in a vacuum.

5. ***Write the Actual Code and Ask if it Looks Good***
At this point, don’t hesitate to ask if your solution looks good! If it doesn’t they might offer some tips to improve it. All of this communication scores points for you — you’ll seem articulate and easier to work with if you’re willing to objectively discuss ways to improve your work.

6. ***Stuck? Ask for Help!***
If you’re having trouble along the way, it’s not illegal to ask for some help. Just phrase it conversationally. For example:
“I’m a bit stuck here, do you have any tips to nudge me in the right direction?”

7. ***Bonus: Communicating Prior to the Interview.***
You should have a human resources or interview point of contact prior to the interview. Are you curious whether there will be a coding portion of the interview? Ask them! Furthermore, you can ask if there is anything in particular you should prepare. They very well might give you hints like specifying the language in which they’ll ask questions, the number of questions, the style of question (e.g., develop an algorithm vs. find the bug). They might tell you whether you’ll be sitting at a computer or standing at a whiteboard — very useful information you might be able to use to practice or, at the very least, mentally prepare.

8. ***Take a few minutes.***
“Okay, got it. If it’s okay with you, I’m just going to take a minute or two here and think about the problem, and then I’ll start talking.”
Shut your eyes, stare at the ceiling, scribble on the whiteboard — whatever it takes. Think through the basics of the problem, and decide on an approach. Then re-engage the interviewer, and let him know what you’ve concluded. Don’t forget to provide your reasoning!
So, I think that, since the array is sorted, it makes the most sense to do something like a binary search here. Here are the general steps that I think are involved in the solution: …”

9. ***Don’t sweat the small stuff.***
If you can’t remember something like this, it’s often acceptable to say that you’d look it up.
“…and here, I’d use whatever the list.contains() equivalent is in JavaScript. I know it’s not contains, but I can’t remember the method name right now, so we’ll call it contains() for now and I’d look it up right now if I were coding this.”

10. ***Sit down. Be humble.***
A programming interview isn’t just a written exam. It’s an assessment of your programming abilities, and there are plenty of things that fall into that category beyond mere coding prowess. For example:
Are you able to express your ideas and solutions clearly and effectively?
Do you treat the interviewer with respect?
How do you accept criticism (technical, constructive or otherwise)?
Are you able to collaborate with others to come up with solutions?
Do you communicate in a way that demonstrates empathy?
Are you honest (especially about yourself)? 

11. ***Review your work.***
Think about how silly you look if your solution is not right.
Instead, if you have the time, review your code as you would in a peer review. Show the interviewer that you are committed to producing accurate, high-quality work, and that they can trust you to do so without being prompted.
In particular, focus on these two areas, as they receive heavy scrutiny in interviews:
- ***Algorithmic efficiency.*** Have you found the fastest and most optimal solution given the requirements?
- ***Correctness.*** Sure, you’ve solved for the simple, easy use case, but have you checked for edge cases? Off-by-one errors? Issues with negative numbers or empty arrays or missing keys or strings with weird patterns of characters? Maybe you won’t find all of them, but failing to at least LOOK for edge cases before declaring your solution to be correct is a pretty hefty mistake in an interview.