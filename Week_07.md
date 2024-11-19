## Guidance
Answer the following questions considering the learning outcomes for
- [Week 07](https://learn.foundersandcoders.com/course/syllabus/developer/week07-project04-authentication/learning-outcomes/)

Make sure to record evidence of your processes. You can use code snippets, screenshots or any other material to support your answers.

Do not fill in the feedback section. The Founders and Coders team will update this with feedback on your progress.

## Assessment
 ### 1. Show evidence of some of the learning outcomes you have achieved this week.

## Execute Program - SQL
I carried on learning about SQL and again learned useful things.

### Selecting expressions from tables using AS
* In SQLite, we concatenate with `"a" || "b"`.
```js
exec(`CREATE TABLE cats (name TEXT)`);
exec(`INSERT INTO cats (name) VALUES ('Ms. Fluff')`);
exec(`SELECT name || ' the cat' AS name FROM cats`);

###### Result:
[{name: 'Ms. Fluff the cat'}]
```
* When we select `x BETWEEN y AND z`, the `AND` is part of `BETWEEN`. It has nothing to do with the logical `AND` of `SELECT x AND y`.
`exec(SELECT name, age BETWEEN 2 AND 3 AS is_2_or_3 FROM cats);`

### Bind parameters
```js
const users = exec(`SELECT name FROM users WHERE id = ?`, [2]);
const user = users[0];
user.name;
```
### Multiple Statements
```js
exec(`
  CREATE TABLE users (name TEXT);
  INSERT INTO users (name) VALUES ('Amir');
  SELECT * FROM users;
`);
```

## Authentication

This week I only focused solely on authentication and explored the subject as much as possible. 
For this last project, I have been taking a different approach, as I have been focusing on exploring new subjects that I want to learn and understand. (Instead of rushing and focusing on finishing the project and delivering the final product). 

So after going down an authentication rabbit hole for 2 days, trying first to implement a local strategy using passport.js, I finally decided to go with JWT.

What is a JWT?
* JWT is a compact and self-contained method for securely transmitting information between parties as a JSON object.
* Since it is stateless, all the necessary information is contained within the token. This way you avoid managing any sessions.
![image](https://github.com/user-attachments/assets/64387af4-c053-499b-a4f3-55c16544da62)

How does it work?
![image](https://github.com/user-attachments/assets/f12ba54b-6b22-49e1-8b46-24612d572ab9)

The first step was creating the JWT strategy, following the documentation (which is not very good) and then doing some research to know what things to amend for our project.
```ts
const JwtStrategy = passportJwt.Strategy;
const ExtractJwt = passportJwt.ExtractJwt;

const opts: any = {};
opts.jwtFromRequest = ExtractJwt.fromAuthHeaderAsBearerToken();
opts.secretOrKey = process.env.JWT_SECRET;
opts.issuer = process.env.JWT_ISSUER;
opts.audience = process.env.JWT_AUDIENCE;

export default function loadJWTStrategy() {
  passport.use(
    new JwtStrategy(opts, function (jwt_payload, done) {
      const user: User = getUserByEmail(jwt_payload.email);
      if (!user) {
        return done(null, false);
      }
      return done(null, user);
    })
  );
}
```

### Log in

For hashing and salting the password, we went with bcrypt, so we don't have to store the salt in the database.
I created a small function to compare the stored hashed_password with the one the user is using to log in.
```ts
export const comparePassword = async (
  password: string,
  hashedPassword: string
) => {
  return await bcrypt.compare(password, hashedPassword);
};
```

Then I created a controller to check if the user trying to log in is entering the correct details, and if that's the case, generate a JWT.
```ts
export const login = async (req: Request, res: Response) => {
  const { email, password } = req.body;

  try {
    const user: User = getUserByEmail(email);
    if (!user) {
      return res.status(400).json({ message: "Invalid email or password" });
    }

    const isMatch = await bcrypt.compare(password, user.hashed_password);
    if (!isMatch) {
      return res.status(400).json({ message: "Invalid email or password" });
    }

    const token = jwt.sign({ email: user.email }, JWT_SECRET, {
      expiresIn: "1h",
      issuer: process.env.JWT_ISSUER,
      audience: process.env.JWT_AUDIENCE,
    });

    res.status(200).json({ token });
  } catch (error) {
    console.error(error);
    res.status(500).json({ message: "Server error" });
  }
};
```
On my terminal I pasted the CURL command `curl -s -X POST -H "Content-Type: application/json" --data '{"email": "itziar@gmail.com", "password": "123456"}' http://localhost:3000/login`, and a token has been generated:
![image](https://github.com/user-attachments/assets/916afee8-f055-4696-b672-01dd84a73c8b)

### Sign up

 Using bcrypt again, this function will generate a hashed and salted password:
 ```ts
export const hashPassword = async (password: string) => {
  const hashedPassword = await bcrypt.hash(password, 10);
  return hashedPassword;
};
```
And then this function will insert the new registered user in our database if the email and username don't exist:
```ts
export const register = async (req: Request, res: Response) => {
  const { username, email, password } = req.body;
  try {
    const existingEmail = await getUserByEmail(email);
    if (existingEmail)
      return res.status(400).json({ message: "Email already exists" });

    const existingUsername = await getUserByUsername(username);
    if (existingUsername)
      return res.status(400).json({ message: "Usernames already exists" });

    const hashedPassword = await hashPassword(password);
    await createUser({
      username,
      email,
      hashed_password: hashedPassword,
    });

    res.status(201).json({ message: "User registered successfully" });
  } catch (err) {
    res.status(500).json({ message: "Error registering user" });
  }
};
```
```ts
export const createUser = (user: any) => {
  const stmt = db.prepare(
    "INSERT INTO users (username, email, hashed_password) VALUES (?, ?, ?)"
  );
  stmt.run(user.username, user.email, user.hashed_password);
};
```
Time for testing 
```sh
curl -X POST http://localhost:3000/register \
  -H "Content-Type: application/json" \
  -d '{
    "username": "testuser",
    "email": "testuser@example.com",
    "password": "secure123456"
  }'
```
![image](https://github.com/user-attachments/assets/8a1734d7-0822-4450-8fc8-71ff59123788)

🎉

 ### 2. Show an example of some of the learning outcomes you have struggled with and/or would like to re-visit.
### Authentication
I found the workshop quite difficult. Doing all the authentication from scratch was interesting, especially because it's always good to know how things work. 
However, for the implementation, I wanted to use a library: passport.js.

The biggest struggle was to understand the documentation, which is a bit rubbish. 
I did their tutorial, but it just tells you to copy and paste some code snippets and doesn't really explain anything. 

So I had to do some research watching tutorials and reading about it from other sources. 

### Log in
Implementing the login was the hardest part of using JWT for me. I need to do deep research to understand how to create the tokens.

## Feedback (For CF's)
> [**Course Facilitator name**]

Alexander

> [*What went well*]

Thorough exploration of JWT authentication with clear code examples and implementation details. Good progression from basic login/register functionality to token generation. Strong understanding demonstrated through practical examples and error handling.

> [*Even better if*]

Add validation for password strength during registration and implement token refresh logic for better security. Documentation comments could be more detailed for complex functions.
