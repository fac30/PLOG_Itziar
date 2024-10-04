## Guidance
Answer the following questions considering the learning outcomes for
- [Week 04](https://learn.foundersandcoders.com/course/syllabus/developer/week04-project03-frontend/learning-outcomes/)

Make sure to record evidence of your processes. You can use code snippets, screenshots or any other material to support your answers.

Do not fill in the feedback section. The Founders and Coders team will update this with feedback on your progress.

## Assessment
 ### 1. Show evidence of some of the learning outcomes you have achieved this week.

#### SQL in Execute Program
I actually enjoyed this course, and I can see it as something that will be useful in the future.

##### Common keywords in SQL
* To create a new table (by default there is a maximum of 2,000 columns). `CREATE TABLE users (email TEXT, name TEXT)`

* To add elements to the table. `INSERT INTO users (email, name) VALUES ('amir@example.com', 'Amir')

* To return an array of the content from the table (* to retrieve all the info  in the table). `SELECT * FROM users

##### Column types

The types are necessary to ensure that the data inside is correct, PostgreSQL is very reliable, and it will never let us store invalid data in the tables.

* `TEXT` - string
* `INTEGER` - whole number (positive or negative)
* `REAL` - real number with decimals (e.g. 5.17 or 0.0003)

### Calling columns

You can call only the columns you want.

`SELECT name FROM users`  instead of using * to retrieve all.
Or do multiple at the same time, separating with a comma. `SELECT name, age FROM users`

##### SQLite

It doesn't enforce column type. You can do the below without having an error, which other databases won't let:
`CREATE TABLE rectangles (width REAL, height REAL)`
`INSERT INTO rectangles (width, height) VALUES ("oh", "no")`

If you insert a number `7` into a text column, it will convert it to a text `"7"`.
If you mix the name and age fields, for example, it will allow it:

```bash
CREATE TABLE cats (name TEXT, age INTEGER)
INSERT INTO cats (name, age) VALUES (7, 'Catsup')
SELECT * FROM cats

###### Result:
[{name: '7', age: 'Catsup'}]
```
### Inserting multiple rows

```bash
CREATE TABLE users (name TEXT NOT NULL);

INSERT INTO users (name) VALUES
    ('Amir'),
    ('Betty'),
    ('Cindy')
    
SELECT * FROM users

###### Result:
[{name: 'Amir'}, {name: 'Betty'}, {name: 'Cindy'}]
```

---

#### React
This week we have focused on using **REACT**.
During this week I have been following some tutorials about it, and I can see the benefits of using it, and how it makes building a dynamic front-end much easier.

Thanks to the tutorial I was following, I installed the [React Snippets](https://marketplace.visualstudio.com/items?itemName=burkeholland.simple-react-snippets) on Vs Code, which made using React a bit easier.

##### Components
I found Monday's workshop very useful because when we finished the design, we could think of which components of the app and which would be reusable. 
I enjoyed the exercise of doing this on the whiteboard before even starting to code. It means the whole team is on the same page, and we know clearly how the project is structured.

The funny thing about React is that sometimes it made me feel it was the easiest thing in the world, and the next second I would be thinking it was too complicated. I think I found a good balance in the end.

#### Props & Tailwind
One of the first things I learned in React was using Props. Simple and understandable. I found it very useful.

I needed a refresher on Tailwind because I haven't used it in 4 years. The [cheat sheet](https://nerdcave.com/tailwind-cheat-sheet) was a lifesaver.
I think once I started using the classes, and remembering the names, it made styling so much faster! 

```tsx
type CardProps = { quizName: string; description: string; level: string };

function Card(props: CardProps) {
  return (
    <>
      <div className="card p-10 rounded-md">
        <h3 className="quiz-name text-2xl font-semibold p-2">
          {props.quizName}
        </h3>
        <p className="quiz-description p-2">{props.description}</p>
        <p className="level text-medium font-semibold p-2">
          Level: {props.level}
        </p>
        {/* Here goes the Play button */}
      </div>
    </>
  );
}
```
##### useState vs useEffect

* `useState`: Use it when you need to **store and update data** inside the component (like a counter, form input, or toggle switch). React will re-render the component when this data changes.Imagine you have a counter on a website that starts at 0, and every time you press a button, it goes up by 1. You need to **store** that number somewhere so that React knows what number to show on the screen.

* `useEffect`: Use it when you need to **run side effects** like fetching data, interacting with things outside React, or cleaning up resources. React will know when to run this code based on when the component is rendered or updated. Imagine you need to **fetch weather data** from the internet when the page first loads. You want to do this **only once** when the component appears.

Once I understood the difference between them, I used them to fetch data from our mock database stored in our back-end project. 
```tsx
type Quiz = {
  id: number;
  name: string;
  description: string;
  category: string;
  difficulty: string;
};

function Categories() {
  const [quizzes, setQuizzes] = useState<Quiz[]>([]);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    fetch("http://localhost:3000/quizzes")
      .then((response) => {
        if (!response.ok) {
          throw new Error("Failed to fetch quizzes");
        }
        return response.json();
      })
      .then((data) => {
        setQuizzes(data);
      })
      .catch((error) => {
        setError(error.message);
      });
  }, []);
```
##### CORS (Cross-Origin Resource Sharing)
When we were fetching the data, even though it worked, we realised that the browser was blocking it for us. So that's when we learned about CORS. 
We installed it on our API, passing our front-end server address, and specifying which methods we accept.
TADA! Now it's working.

```ts
pp.use(
  cors({
    origin: "http://localhost:5173",
    methods: ["GET", "POST", "PATCH", "DELETE"],
    credentials: true,
  })
);

const port = process.env.PORT || 3000;

app.use(cors(corsOptions));
```

##### Navigation between pages & Peer programming
When we had all the components working, we could retrieve the data from our database. We tried connecting the different pages using buttons for navigation. 
Marika and I did some peer programming to make this possible. We only managed to make a couple of pages work, but it's still progress!

```tsx
const HomePage: React.FC = () => {
  const navigate = useNavigate();

  const goToExplore = () => {
    navigate('/explore')
  }

  const goToInput = () => {
    navigate("./InputPages")
  }
  return (
    <div className="logo p-8">
      <h1>Wisdom War!</h1>
      <Button 
        type ="button"
        onClick={goToInput} 
        buttonName="Create new quiz"
        text="Create new quiz">
      </Button>
      <Button onClick={goToExplore} buttonName="Explore quizzes" text="Explore quizzes"></Button>
    </div>
  );
```
```tsx
<Router>
        <Navbar title="Explore Quizzes" />
        <Categories />
        <Routes>
          <Route path="/" element={<HomePage />} />
          <Route path="/explore" element={<ExplorePage />} />
          <Route path="/quiz" element={<QuizQuestionsPage />} />
        </Routes>
      </Router>
```

 ### 2. Show an example of some of the learning outcomes you have struggled with and/or would like to re-visit.
#### React with typescript
While last week I did not struggle using TypeScript on our API, for some reason this week I did struggle a little using it on React. 
Perhaps because I've only done the basic Exevute program course, and I had to use more complex TypeScript syntax in this project:
`const listOfQuizzes: Record<string, Quiz[]>`.
I will carry on with the courses as I want to dig deeper. 

#### Page navigation
As mentioned, Marika and I had to work together to make it work. However, we struggled a lot. 
We only managed to make navigation possible in a couple of pages. I 100% will revisit this during the weekend, to try and solve this issue. 

Marika told me she enjoyed programming together, and she gave some good feedback about me. She mentioned that under stressful situations (having to present in 1h without having the presentation slides, and our project with a constant error message on the screen), I did not panic and managed to keep focused and try to find the bug following the errors. She said it's a good skill, and honestly, I didn't realise I had it until she mentioned. :)

#### Understand how to communicate the back-end with the front-end.
I have already mentioned CORS above, but just the idea on how both servers communicate took a while for me to understand. 

#### Time management
This week I haven't been feeling my best health-wise, and I think it has affected how I have worked with my team. 
I personally feel I haven't managed my time well, and perhaps I spent too much time on some tasks, neglecting others.
However, I am not being hard on myself about it, I take it as a lesson. 
I always think it's better to split the work and work separately to make the most of out the two days we have to build the project. While it can be true for some tasks, what happened on Thursday, made me realise that sometimes working with your peers helps to find solutions much faster. 
It has been a week with a lot of lessons. 

## Feedback (For CF's)
> [**Course Facilitator name**]  
> [*What went well*]  
> [*Even better if*]
