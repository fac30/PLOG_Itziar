## Guidance
Answer the following questions considering the learning outcomes for
- [Week 06](https://learn.foundersandcoders.com/course/syllabus/developer/week06-project04-databases/learning-outcomes/)

Make sure to record evidence of your processes. You can use code snippets, screenshots or any other material to support your answers.

Do not fill in the feedback section. The Founders and Coders team will update this with feedback on your progress.

## Assessment
 ### 1. Show evidence of some of the learning outcomes you have achieved this week.

## Execute Program = **SQL**
Since this week we had to build a database, I focused on doing only SQL on Execute Program, and it was handy because I ended up using what I learned.

* `SELECT WHERE` to return only if conditions are true: `SELECT name FROM cats WHERE age > 4`
* We can select by multiple columns using `AND` and `OR`
  * ```
    SELECT email FROM users
    WHERE name = 'Betty' AND email = 'betty.j@example.com';
    ```
  * `OR` will return all values that have that condition:
    ```
    SELECT age FROM cats 
    WHERE name = 'Keanu' OR name = 'Katy Purry';
    ```

* There are no booleans in SQLite, instead, we use integers: **0 means false** and **1 means true**.
* We can Update rows with `SET`: `UPDATE cats SET name = 'Mr. Reeves' WHERE name = 'Keanu'`.
* `UNIQUE` keyword in the columns, useful for usernames, emails, etc.

*  `PRIMARY KEY` & `FOREIGN KEY`
 1. A Table can only have one Primary Key
 2. The primary key column is automatically `UNIQUE`, so it can never have duplicate values.
 3. In most databases, the primary key is also `NOT NULL` (not in SQLite).
 4. Add `AUTOINCREMENT`
 5. Foreign Keys are IDs that are linked to the Primary Key of a different table.
  In the image below, user_id is a Primary Key in the users table and a Foreign Key in the orders table:
  ![image](https://github.com/user-attachments/assets/c370bd9e-1fdf-41c4-a9b8-059e3a020c8d)

## Designing our Database Schema
After doing the workshop with Jason (which I found extremely useful), we started designing our own database schema and thinking about what we needed for our project.
We decided what would be the PK and the FK and how they relate to each other.
I really enjoyed this part of the process, and adding what we drew on a paper in the db diagram.
```
Table products {
  id integer [primary key]
  name varchar
  description text
  artist_id integer [ref: > artists.id]
  category_id varchar [ref: > categories.id]
  creation_date timestamp
}
```
![ecommerce-fac (2)](https://github.com/user-attachments/assets/b025442c-c5b7-420f-b3a0-41afcb8746b9)


## Creating the Database

**SQLite3 vs better-SQlite3** 
We decided to use better-SQLite as the project is small and fast-paced, and since better-SQLite is synchronous, it means we avoid using callback functions. 

1. Creating the tables.
Once we had the diagram, I could export the SQL version of it (then ChatGPT converted it to make it compatible with SQLite3) and I created a script to add them to our database.
The challenge here was using it with typescript, as every tutorial I found on the subject was with JavaScript. So it took me longer to import the DatabaseConstructor, than building it.

```ts
import DatabaseConstructor, { Database } from "better-sqlite3";

export const db: Database = new DatabaseConstructor("db.sqlite3");

const query = `
    CREATE TABLE IF NOT EXISTS "artists" (
        "id" INTEGER PRIMARY KEY AUTOINCREMENT,
        "name" TEXT,
        "bio" TEXT,
        "socials" TEXT,
        "image" TEXT,
        "creation_date" TIMESTAMP DEFAULT CURRENT_TIMESTAMP
    );
...
 db.exec(query);
```
Once that was done, I created a script command in our package.json to generate the database and create the tables.
Running `npm run db` in the terminal will create all the tables if they don't exist.
```
"scripts": {
    "db": "ts-node src/scripts/create-database.ts",
  }
```

2. Adding data to our database
To begin with, we needed to add dummy data in our database, in order to have products and artists to display in our frontend.
We agreed what our MVP for this week will be, so I only had to create the script and the dummy data for a few specific tables (Artists, Categories, Products, Procuct_Variations, and Product_Images).

I decided to Insert that dummy data from a JSON file, so the script reads that JSON  file, takes each object inside and it Inserts in the table using SQL keywords.

```ts
interface Artist {
  name: string;
  bio: string;
  socials: string;
  image: string;
}

fs.readFile(
  "data/artists.json",
  "utf8",
  (err: NodeJS.ErrnoException | null, data: string) => {
    if (err) {
      console.error("Error reading JSON file:", err);
      return;
    }

    const artistsData: Artist[] = JSON.parse(data);

    const insertArtistsData = db.prepare(
      "INSERT INTO artists (name, bio, socials, image) VALUES (?, ?, ?, ?)"
    );

    artistsData.forEach((artist) => {
      try {
        insertArtistsData.run(
          artist.name,
          artist.bio,
          artist.socials,
          artist.image
        );
      } catch (insertErr) {
        console.error("Error inserting artist:", insertErr);
      }
    });

    db.close();
  }
);
```
Similar to before, I created another script for our package.json to run the command easily.
Running `npm run data` in the terminal will populate the database.
```
"scripts": {
    "data": "ts-node src/scripts/insert-db.ts"
  }
```

I decided to use a SQL Browser to visualize the data.

![image](https://github.com/user-attachments/assets/c328d165-0611-4c84-9538-eda612806402)
![image](https://github.com/user-attachments/assets/66557c16-fa32-4974-9dc2-76b447010e22)
![image](https://github.com/user-attachments/assets/36797334-9a03-4877-879b-516e78f8d74c)

I have to say, I had so much fun designing and creating the database 🤩

## A Small Tanget
I started to think about storing passwords in our database and read about **hashing passwords** and **salting** them.
Hashing passwords without salting them can be vulnerable to cyber-attacks because if two users have the same password, the hash will be the same, which will make it easier to decipher. 

> Let’s say that we have password farm1990M0O and the salt f1nd1ngn3m0. We can salt that password by either appending or prepending the salt to it. For example: farm1990M0Of1nd1ngn3m0 or f1nd1ngn3m0farm1990M0O are valid salted passwords. Once the salt is added, we can then hash it. Let's see this in action:
>
> Prepending the Salt:
>
> Password: farm1990M0O
> Salt: f1nd1ngn3m0
> Salted input: f1nd1ngn3m0farm1990M0O
> Hash (SHA-256): 7528ed35c6ebf7e4661a02fd98ab88d92ccf4e48a4b27338fcc194b90ae8855c

Different users, same password --> Different salts, different hashes. 
If someone looked at the full list of password hashes, no one would be able to tell that two users use the same password.

## Creating Custom Utilities with Tailwind
Thanks to Dani I learned that you can create your own custom utilities, to style specific components. This way you avoid having to add all the styles classes to one button for example, and simply add the new class you have created.

In the CCS file:
```css
@layer utilities {
  .btn-blue-sm {
    @apply bg-blue-500 text-white text-xs w-24 h-12 rounded;
  }
}
```
In your jsx or tsx project:
```ts
function App() {
  return (
    <div className="App p-12">
            <div><button className='btn-blue-sm'>Small Button</button></div>
          </div>
    </div>
  );
}
```

 ### 2. Show an example of some of the learning outcomes you have struggled with and/or would like to re-visit.
This week has been the week that I realised how much I have learned in the previous 5 weeks without me noticing. 
I did not encounter massive difficulties like I have been doing.

However, I did have a few problems with:

### TYPESCRIPT
I haven't had many issues with TypeScript so far, apart from forgetting to add types constantly. But this week I have found myself struggling with that a little bit. 
These two gave me a headache until I found out what is the err type:`(err: NodeJS.ErrnoException | null, data: string)` 

I also forgot to add the interface at the beginning, so the entire code was complaining. 

And then as mentioned above, importing better-sqlite3 needed that extra DatabaseConstructor in order to work.
`import DatabaseConstructor, { Database } from "better-sqlite3";`

### ATTENTION TO DETAIL 🕵️‍♀️
This week again, I had to be a detective bug-chasing.
This time it was about having typos in my code because guess what? `-` !== `_`

While my tables were perfectly named `product_variations` and `product_images`, for some reason, on my script I decided to write:
`"INSERT INTO product-variations (product_id, size, colour, price, stock) VALUES (?, ?, ?, ?, ?)"` and `"INSERT INTO product-images (product_id, image_url, is_main_image) VALUES (?, ?, ?)"`.
It took me quite a long to realise that I added a `-` instead of `_`.

Similarly, Gaj and I were trying to find out why his functions weren't working, and it was also a small thing that was making everything break. 

I guess a big part of being a developer is also to be willing to spend a lot of time chasing the small mistakes.

## Feedback (For CF's)
> [**Course Facilitator name**]  
> [*What went well*]  
> [*Even better if*]
