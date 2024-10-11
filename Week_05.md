## Guidance
Answer the following questions considering the learning outcomes for
- [Week 05](https://learn.foundersandcoders.com/course/syllabus/developer/week05-project03-test-deploy/learning-outcomes/)

Make sure to record evidence of your processes. You can use code snippets, screenshots or any other material to support your answers.

Do not fill in the feedback section. The Founders and Coders team will update this with feedback on your progress.

## Assessment
 ### 1. Show evidence of some of the learning outcomes you have achieved this week.

This week in Execute Program I have been toggling between Everyday Typescript and JavaScript Concurrency. I think this is the first week that I enjoyed having those 45 minutes of music and concentrating on learning, rather than feeling I am wasting time that I could be using doing the project.

## Everyday Typescript

Whenever you create a type object, you can Pick or Omitt properties: `type NameOnly = Pick<User, 'name'>;` / `type NameOnly = Omit<User, 'email' | 'age'>;`. I see it as a complement to the `?` that you can add to the properties to make them optional.

### Discriminated Unions
Some types can have multiple objects with a shared property that will be the discriminator, and this discriminator will be the one deciding which union we are looking at:
```ts
type StartedCourse = {
  started: true
  lastInteractionTime: Date
};
type UnstartedCourse = {
  started: false
};
type Course = StartedCourse | UnstartedCourse;
```

Using discriminators can be a good way to handle errors as well:
```ts
type ConversionSucceeded = {
  kind: 'success'
  value: number
};
type ConversionFailed = {
  kind: 'failure'
  reason: string
};

type Conversion =
  ConversionSucceeded | ConversionFailed;

function safeNumber(s: string): Conversion {
  /* convert to number here */
}
```
### Logical Operator Narrowing
You can use logical (&& and ||) operators in TS as well.
```ts
function arrayLength(strings: string[] | undefined): number | undefined {
  return strings && strings.length;
}
[arrayLength(['a', 'b']), arrayLength(undefined)];
```
```ts
function numberOrOne(n: number | undefined): number {
  return n || 1;
}
[numberOrOne(3), numberOrOne(undefined)];
```
Also using ternary operators:
```ts
function lengthOrNumber(n: number | number[]): number {
  return Array.isArray(n) ? n.length : n;
}
[lengthOrNumber([1, 2]), lengthOrNumber(5)];
```

## Concurrency
### Recovering from rejection:
We can use `.catch` to recover from rejection, we'll get a callback from that. We can then `.then` callbacks:
```js
Promise.resolve()
  .then(() => {
    throw new Error('this will reject!');
  })
  .catch(() => 'oh no')
  .then(message => message.length);
```

### Saving the resolve function for later
New Promise is synchronous, so we can save it for later:
```js
et resolve;
const promise = new Promise(r => {
  resolve = r;
});
/* If the Promise constructor callback were
 * asynchronous, `resolve` would be `undefined`
 * here. */
resolve(3);
```

### waitForAll & Promise.all
`waitForAll` takes any number of promises, collects their fulfilled values into an array, and returns a new promise that fulfils with that array.

```js
function waitForAll(promises) {
  let arrayPromise = Promise.resolve([]);
  
  for (const promise of promises) {
    /* Build a new promise holding an array that contains all of the old
     * array elements, plus the one from this promise. */
    arrayPromise = arrayPromise.then(array =>
      promise.then(value =>
        [...array, value]
      )
    );
  }
  
  return arrayPromise;
}

/* Once the `waitForAll` function is written, waiting for multiple API
 * calls is easy! */
waitForAll([getUser(1), getUser(2), getUser(3)]);
```

There is a built-in `Promise.all` which would be a better solution.
```js
Promise.all([getUser(1), getUser(2), getUser(3), getUser(4)]);
```

---

## React & Full-stack development

Two weeks ago when we started this project, we were focusing solely on backend, and last week only on frontend, this week was the turn for ✨**full-stack**✨.
That means I had two VS code editors open and had to go back and forth changing on both sides.

This week I have learned a very important thing which is the **thought process**:

> I need A --> To achieve that I need to change B --> And then go back to A to adjust and make it work.

### Filtering the questions by ID

So we had a list of quizzes with different categories displayed in cars, now to select a specific quiz and play it, we needed to click `Play` and navigate to the specific quiz.
When we built our API and created specific endpoints, we only did generic ones like `"/questions"`, which means I would `GET` all the questions, but not the ones related to a specific quiz.

> So first of all, I had to go back to the backend and make a function to Filter the quizzes by ID.
> Inside this `GET` first I will get all the Answers, and all the Questions stored in our JSON database. 
> Next, I am going to `.map` through all the questions to return only the ones I need.
> While going through each question, I am going to filter the Answers using the questionId.
> I am going to `.map` those filtered answers and remove the "isCorrect" property (because we don't want the user to see which one is correct if they inspect it), and the "questionId" (because it's unnecessary data to display).
> Now that we have all the questions filtered we can return those as a JSON.
> But if someone tries to use an ID that is not assigned to any quiz, then we will return all the questions inside the database.
```ts
app.get("/questions", (req: Request, res: Response) => {
  const allAnswers = Answer.createAnswersFromJSON(answersJson);
  let allQuestions = Question.createQuestionsFromJSON(questionsJson).map(
    (question) => {
      const questionAnswers = allAnswers
        .filter((answer) => answer.questionId === question.id)
        .map((qa) => {
          delete qa["isCorrect"];
          delete qa["questionId"];
          return qa;
        });
      question["answers"] = questionAnswers;
      return question;
    }
  );

  const quizId = parseInt(req.query.quizId as string);
  if (!quizId) {
    res.json(allQuestions);
  } else {
    const filteredQuestions = allQuestions.filter((e) => e.quizId === quizId);
    res.json(filteredQuestions);
  }
});
```
Once that was done, I could go back to the frontend and fetch the correct data, using a query string:
```ts
useEffect(() => {
    const fetchQuestions = async () => {
      try {
        const questionsResponse = await fetch(
          `http://localhost:3000/questions?quizId=${quizId}`
        );
        if (!questionsResponse.ok)
          throw new Error("Failed to fetch questions.");
        const questionsData = await questionsResponse.json();
        setQuestions(questionsData);
      } catch (error) {
        setError("Failed to fetch questions or answers.");
      }
    };

    fetchQuestions();
```

### Saving the scores & navigate to the Scores Page

Now that all the questions are displaying correctly and filtered, I had to create an async function to `POST` the answers in our Results database, once the player clicks the submit button.
```ts
const submitQuiz = async () => {
    const quizResult = await fetch("http://localhost:3000/results", {
      method: "POST",
      body: JSON.stringify({
        quizId: quizId,
        results: selectedAnswers,
        totalQuestions: questions.length,
      }),
      headers: {
        "Content-Type": "application/json",
      },
    });
    const jsonData = await quizResult.json();
    console.log(jsonData);
    navigate(`/results/${jsonData.id}`);
  };
```
Similarly, I had to go back to the backend and fix our `POST` method, so it can check the submitted answers are equal to the answer with `isCorrect: true` property, to add the scores.

```ts
app.post("/results", (req: Request, res: Response) => {
  const allAnswers = Answer.createAnswersFromJSON(answersJson);

  let score = 0;
  for (const [questionId, answerId] of Object.entries(req.body.results)) {
    const correctAnswer = allAnswers.find(
      (answer) => answer.questionId === parseInt(questionId) && answer.isCorrect
    );
    if (correctAnswer?.id === answerId) {
      score++;
    }
  }

  const results = readResults();
  const newResults = new Result(
    results[results.length - 1].id + 1,
    req.body.quizId,
    0,
    score,
    req.body.totalQuestions
  );

  results.push(newResults);

  writeResults(results);

  res.status(201).json(newResults);
});
```
Once the user clicks `Submit` and we `POST` the results, I added a small piece of code to navigate to that specific result ID (see above).
But, once again I had to go to the backend and create a new endpoint, which we didn't have. This new endpoint will find the result id linked to the one we just posted.
```ts
app.get("/results/:id", (req: Request, res: Response) => {
  const resultId = parseInt(req.params.id);

  let results = readResults();
  const result = results.find((result) => result.id === resultId);
  res.json(result);
});
```
And after that the entire quiz maker and quiz player was DONE and WORKING. YAY!

---

## Avoid ChatGPT

This week I have tried avoiding ChatGPT as much as possible. Yes, it's a very useful and powerful tool, but also a dangerous one. 
Since the beginning of the Bootcamp, I have tried using it only when necessary or feeling very stuck, or to understand certain concepts better (like: explain me as I'm 10). However, I realised that the more complex the project and the tighter the deadlines, the more I would reach out to the chat. I don't like that, because I don't want to develop bad habits, and also, I don't learn much when I use it.

So this week I challenged myself, and I used it as less as possible. Using other resources like Stack Overflow, or simply googling my questions.
I found this way more helpful, because the answers are generic, and you have to fully understand them to apply the solutions to your own case. And even if it takes longer this way, the learning is also greater. 

 ### 2. Show an example of some of the learning outcomes you have struggled with and/or would like to re-visit.
 
## Full Stack Developer
As mentioned above, working as a full stack in such a big project has been challenging. Having to go back and forth, and finding what and where to change has been a challenge on its own. And although it has been quite stressful, it has also allowed me to learn A LOT. 
In retrospect, I think the project was too big, which was not intentional but made it difficult to finish. There are still some functionalities and features that we did not have time to implement, but we had to prioritise what we wanted to work on and what we could leave out. That was a learning point as well.

## debugging === detectiveMode 🕵🏻‍♀️
One of the things I struggled with was chasing the bugs and errors, and then fixing them.

* number !== string
```ts
if (correctAnswer?.id === answerId) {
      score++;
    }
``` 
Because we are working on teams, I was using part of my team's code to build my own.
In the frontend, part of the code that I was using was written by someone else, and `answerId` was returning as a `string`.
But, in the database, the `correctAnswer`.id was a number.
After writing SO many `console.log`s, I realised that, so I could go back to the frontend, change it for a `number`, and then it was finally working.
It took me hours to find this, but the experience of going back and forth, step by step, checking and printing messages to find where was actually going wrong, was a huge learning.
Also, how satisfying is it to find a bug and fix it? 🥹

* Nodemon --> `"ERR_CONNECTION_RESET"`

When everything seemed to be working, and the code was correct, when clicking the Submit button, I was getting the above error in the browser.
Opening the Inspect page in the browser, I could see that the data was successfully sending and posting, but then it was failing to fetch the data back.

I had to ask for help for this, and Jon and I did a bit of peer programming to find out what was going on.
He noticed the error was coming from Nodemon and I realised that every time we were submitting something and updating the database, Nodemon would restart.
So once that was clear, the solution was adding a `--watch` flag in our `package.json` to only restart the server when the changes were made inside the `src` folder.
```
"scripts": {
    "dev": "nodemon --watch src --ext ts --exec ts-node src/index.ts",
  },
```

## AWS & Cypress
Since I was ill during the weekend and on Monday, I missed the AWS workshop. Plus, we were very behind in our project, so we focussed on finishing it and we completely neglected both testing and deployment.
We didn't realise the magnitude of this project when we first started it, as we genuinely thought that was the assignment. But last week when we were trying to finish and implement all the functionalities, the reality was that it was too much. So this week we just wanted to have a finished product, and we relied on Levi already having it deployed. So I will revisit and learn these two tasks because I also want to know them.

## Feedback (For CF's)
> [**Course Facilitator name**]  
> [*What went well*]  
> [*Even better if*]
