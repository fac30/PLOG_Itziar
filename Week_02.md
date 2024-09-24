## Guidance
Answer the following questions considering the learning outcomes for
- [Week 02](https://learn.foundersandcoders.com/course/syllabus/developer/week02-project02-chatbot/learning-outcomes/)

Make sure to record evidence of your processes. You can use code snippets, screenshots or any other material to support your answers.

Do not fill in the feedback section. The Founders and Coders team will update this with feedback on your progress.

## Assessment
 ### 1. Show evidence of some of the learning outcomes you have achieved this week.
 
>  npm & using libraries to implement APIs.
> It has been a long learning process this week. I had to read a lot of documentation and watch a lot of videos to understand the code.

> I learned that sometimes it's more important to slow down and understand what you are actually doing, rather than copying the documentation code and asking chatGPT to make sense of it and to fill the gaps. 

> Object oriented programming - how to work with classes (it actually took my a really long time to understand how this works)

```javascript
const { SlashCommandBuilder } = require("discord.js");

module.exports = {
  data: new SlashCommandBuilder()
    .setName("explain")
    .setDescription("Replies how asking for something to explain"),
  async execute(interaction) {
    await interaction.reply(
      "Sigh... can't anyone figure things out on their own? What do you need explained?"
    );
  },
};
```
> Asynchronous functions: try, await, catch
```javascript
try {
    await command.execute(interaction);
  } catch (error) {
    console.error(error);
```
> TypeScript (I didn't know it existed, but I can see why it's useful).
```typescript
let n: number = 2;
let s: string = 'a string';
let b: boolean = true;
```

> Tuples - the ones I liked the most because of readability.
```typescript
let numberString: [number, string] = [1, 'a'];
numberString[1];
```
> Generic functions
```typescript
function first<T>(elements: Array<T>): T {
  return elements[0];
}
```
> Testing (and realised that I actually don't like it)
```javascript
test("Calculate should make the correct math operations", () => {
  equal(calculate(5, "+", 6), 11);
  equal(calculate(25, "-", 4), 21);
  equal(calculate(5, "*", 6), 30);
  equal(calculate(15, "/", 5), 3);
  equal(calculate(5, "&", 6), "Please enter a valid sign (+, -, *, /)");
});
```
> gitignore and why it's important to use it.

 ### 2. Show an example of some of the learning outcomes you have struggled with and/or would like to re-visit.
> I struggled with understanding the new concepts while trying to keep up with the rhythm of the cohort. I didn't have any previous experience with backend and I am a visual person, which makes it a bit harder for me to understand more abstract concepts.
> I definitely want to revisit Ojects and Classes, and keep working with them.

## Feedback (For CF's)
> [**Course Facilitator name**]

Alexander

> [*What went well*]

Very good. I really liked that you also included testing snippets and basic TypeScript concepts.

> [*Even better if*]

There isn't much I can suggest to improve. It would be good to include some project planning ideas, but apart from that, it is very good.
