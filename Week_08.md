## Guidance
Answer the following questions considering the learning outcomes for
- [Week 08](https://learn.foundersandcoders.com/course/syllabus/developer/week08-project04-test-deploy/learning-outcomes/)

Make sure to record evidence of your processes. You can use code snippets, screenshots or any other material to support your answers.

Do not fill in the feedback section. The Founders and Coders team will update this with feedback on your progress.

## Assessment
 ### 1. Show evidence of some of the learning outcomes you have achieved this week.
This week I wanted to make the most out of Execute Program and start learning Python, as it's a language I am interested in.
So far, I only learnt the basics, but I realised it's much cleaner and prettier than JS. 

## React
Since I have been ill, I haven't done as much as I would have liked. However, I did work on building an image carousel.

* UseEffect & UseState to fetch the images from our database.
```ts
useEffect(() => {
    const fetchProductImages = async () => {
      try {
        const response = await fetch(
          `http://18.171.123.115:3000/product-images`
        );
        if (!response.ok) {
          throw new Error("Failed to fetch product images");
        }
        const data = await response.json();
        const slideArr = data.map((i: any) => i.image_url);

        setProductImages(slideArr);
      } catch (error) {
        setError(
          error instanceof Error ? error.message : "Image not available"
        );
      }
    };

    fetchProductImages();
  }, []);
```
* UseState to create the functions to go from one image to another.
```ts
const previousSlide = () => {
    if (current === 0) {
      setCurrent(productImages.length - 1);
    } else {
      setCurrent(current - 1);
    }
  };

  const nextSlide = () => {
    if (current === productImages.length - 1) {
      setCurrent(0);
    } else {
      setCurrent(current + 1);
    }
  };
```
* And the actual carousel component
```ts
return (
    <div className="carousel-images overflow-hidden relative">
      <div
        className={"flex transition ease-out duration-400"}
        style={{
          transform: `translateX(-${current * 100}%)`,
        }}
      >
        {productImages.map((imgSrc) => {
          return <img key={imgSrc} src={imgSrc} />;
        })}
      </div>

      <div className="absolute top-0 h-full w-full px-3 flex justify-between items-center text-white text-3xl">
        <button onClick={previousSlide}>
          <IoArrowBackCircle />
        </button>
        <button onClick={nextSlide}>
          <IoArrowForwardCircle />
        </button>
      </div>
    </div>
  );
```

## Debugging 🕵️‍♀️
I think my biggest contribution to the team has been doing some ✨debugging✨.
Every week I have helped my team find bugs and typos in the code and this one was no different.

* Dani was struggling because she couldn't make the artist page actually show the content of the artists.
In the App.tsx was using the following Router
```ts
<Route path="/artist/:id" element={<ArtistPage />} />
```
but then in the Artist page was using
```ts
artistId` instead of just `id`: `const { artistId } = useParams<{ artistId: string }>();
```
Since she used artistId throught the rest of the function and code, the solucion was:
```ts
const { id: artistId } = useParams<{ id: string }>();
```

* Getting the correct IP address and fixing typos:
`const response = await fetch(`http://8.171.123.115/:3000/product-images`);` ❌
`const response = await fetch(`http://18.171.123.115/:3000/product-images`);` ❌
`const response = await fetch(`http://18.171.123.115:3000/product-images`);` ✅

 ### 2. Show an example of some of the learning outcomes you have struggled with and/or would like to re-visit.
## Deployment
Maybe it's a sign from the universe, but in both weeks that we have done deployment I missed a couple of days being ill. And both times I had Levi on the team working on it. 
So I completely neglected that part of the learning. That's why after the BootCamp I will carry on learning this on my own. 

## Python
Although this is not part of the BootCamp, I want to keep learning the language. I think it's easier once you know JavaScript, so I hope it doesn't take much time.

## Saying good-bye 🥲
The biggest struggle this week has been accepting it's my last week in this BootCamp. The fact that I was ill and had to miss two days didn't help whatsoever.
I am happy I had the opportunity to learn with this amazing cohort, but I will miss going to space4 every day and working with them 💘


## Feedback (For CF's)
> [**Course Facilitator name**]  
> [*What went well*]  
> [*Even better if*]
