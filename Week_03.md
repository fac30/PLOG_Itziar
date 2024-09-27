## Guidance
Answer the following questions considering the learning outcomes for
- [Week 03](https://learn.foundersandcoders.com/course/syllabus/developer/week03-project03-server/learning-outcomes/)

Make sure to record evidence of your processes. You can use code snippets, screenshots or any other material to support your answers.

Do not fill in the feedback section. The Founders and Coders team will update this with feedback on your progress.

## Assessment
 ### 1. Show evidence of some of the learning outcomes you have achieved this week.
> This week I had the opportunity to fully understand and build APIs.
> I have learned more about Classes and Objects, which wasn't fully clear last week.

> This week I had the scrum master role, which I didn't particularly enjoy, which is funny because I am the scrum master in my current job. 
> But I realised that in order to be a good one, you have to understand the project and what we have to do.
> That was my main struggle, facing the project as a whole and trying to slice it into smaller steps.
> Once I knew the steps and everything fell into place, coding and working on the project became much easier.

> Learned the importance of Data Modelling, and why we should always take a piece of paper or a whiteboard, and discuss with the team the whats, hows, and whys, before actually coding anything:
### Objects (Entities) and Their Attributes:

1. **Quiz**:
   - **ID**: Unique identifier for the quiz.
   - **Name**: The name of the quiz.
   - **Description**: A brief overview of the quiz.
   - **Category**: The type or topic of the quiz (e.g., science, history).
   - **Difficulty**: The difficulty level (e.g., easy, medium, hard).
   - **Number of Questions**: How many questions are in the quiz.

> Classes and Constructors // Blueprint vs Instance
> I had to ask ChatGPT to explain this to me as I were 10, but it worked, because I could visualize it.
```ts
class Quiz {
  id: number;
  name: string;
  description: string;
  category: string;
  difficulty: string;
  numberOfQuestions: number;

  constructor(
    id: number,
    name: string,
    description: string,
    category: string,
    difficulty: string,
    numberOfQuestions: number
  ) {
    this.id = id;
    this.name = name;
    this.description = description;
    this.category = category;
    this.difficulty = difficulty;
    this.numberOfQuestions = numberOfQuestions;
  }
```
> Endpoints and using REST
> + some other functionalities --> req.params, res.status, etc.
```ts
//DELETE the quiz
app.delete("/quizzes/:id", (req: Request, res: Response) => {
  const quizId = parseInt(req.params.id);
  let quizzes = readQuizzes();

  const quizIndex = quizzes.findIndex((quiz) => quiz.id === quizId);
  if (quizIndex === -1) {
    return res.status(404).json({ message: "Quiz not found" });
  }
  quizzes.splice(quizIndex, 1);

  writeQuizzes(quizzes);

  // Status code 204: no content
  res.status(204).json();
});
```
> I think some concepts are still not clear in my head. I can read them, I can know what they do (-ish), but I cannot see them clearly in my head.
> I am hoping that in the next weeks, they will slowly establish more clearly in my head.

> I carried on with the TypeScript course in Execute Program, and I have learned new things.
```ts
function isNumber(arg: number | string) {
  return typeof arg === 'number';
}

[isNumber(1), isNumber('a')];
```
```ts
function add1IfNumber(n: number | boolean) {
  return (typeof n === 'number') ? n + 1 : n;
}
[add1IfNumber(1), add1IfNumber(true)];
```
> I finally understand the syntax of ternary operators (now I only have to remember what they are called!) --> Example above.

> One big thing that I learned this week is that VS Code can be very helpful in solving problems. While in the past, if I VS Code was complaining about something, I would look for solutions on google,chatGPT, asking peers...  But didn't realise that you can hover over the issue and it will tell you what is happening, and a lot of times will let you know the solution. It saved me SO MUCH time fixing my code! Maybe it's something that everyone knows, but I had no idea and it's a game changer for me.

> Not a massive achievement, but I'm getting better at writing in Markdown thanks to taking notes in Obsidian.

 ### 2. Show an example of some of the learning outcomes you have struggled with and/or would like to re-visit.
> I still want to dig in more on the concepts we worked with this week. I want to fully understand how to work with APIs.

## Feedback (For CF's)
> [**Course Facilitator name**]  
> [*What went well*]  
> [*Even better if*]
